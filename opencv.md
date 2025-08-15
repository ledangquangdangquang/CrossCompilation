<h1 align = "center">OpenCV </h1>

# For Pi
## 0. 
```
sudo apt update
sudo apt install -y \
    build-essential cmake git pkg-config \
    libgstreamer1.0-dev \
    libgstreamer-plugins-base1.0-dev \
    gstreamer1.0-plugins-base \
    gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad \
    gstreamer1.0-plugins-ugly \
    gstreamer1.0-libav
```
## 1. On Pi
```bash
cd ~
git clone https://github.com/opencv/opencv.git
cd opencv
git checkout 4.5.5
cd ~
git clone https://github.com/opencv/opencv_contrib.git
cd opencv_contrib
git checkout 4.5.5
cd ~/opencv

mkdir build && cd build
rm -rf ~/opencv/build
mkdir ~/opencv/build
cd ~/opencv/build

cmake -D CMAKE_BUILD_TYPE=Release \
      -D CMAKE_INSTALL_PREFIX=/usr/local \
      -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules \
      -D BUILD_opencv_python3=OFF \
      -D BUILD_opencv_python2=OFF \
      -D WITH_JAVA=OFF \
      -D BUILD_TESTS=OFF \
      -D BUILD_EXAMPLES=OFF \
      -D WITH_IPP=OFF \
      -D WITH_TBB=OFF \
      -D WITH_OPENCL=OFF \
      -D WITH_OPENGL=OFF \
      -D WITH_V4L=ON \
      -D WITH_GSTREAMER=ON \
      ..
```
## 2. On Pi
```bash
make -j4
sudo make install
```
## 3. On host
```bash
rsync -avz --rsync-path="sudo rsync" pi@192.168.30.77:/usr/local/lib rpi-sysroot/usr/local 
rsync -avz --rsync-path="sudo rsync" pi@192.168.30.77:/usr/local/include rpi-sysroot/usr/local
```
## 4. CmakeLists.txt (on host)
```c
cmake_minimum_required(VERSION 3.18)
project(testOpenPi LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

# -------------------------------
# Chỉ định sysroot
set(CMAKE_SYSROOT /home/quang/rpi-sysroot)
# Bổ sung include path cho libstdc++
include_directories(
    ${CMAKE_SYSROOT}/usr/include/c++/10
    ${CMAKE_SYSROOT}/usr/include/aarch64-linux-gnu/c++/10
)
# -------------------------------

# --- Qt6 Widgets ---------------
find_package(Qt6 REQUIRED COMPONENTS Widgets Core)
# -------------------------------

# --- OpenCV --------------------
# Trỏ tới sysroot đã rsync header + CMake config
set(OpenCV_DIR /home/quang/rpi-sysroot/usr/local/lib/aarch64-linux-gnu/cmake/opencv4)
find_package(OpenCV REQUIRED)
if(NOT OpenCV_FOUND)
    message(FATAL_ERROR "Không tìm thấy OpenCV")
endif()
message(STATUS "Found OpenCV version: ${OpenCV_VERSION}")
include_directories(${OpenCV_INCLUDE_DIRS})
# -------------------------------

# --- Build executable ----------
add_executable(testOpenPi main.cpp)
target_link_libraries(testOpenPi
    Qt6::Core
    Qt6::Widgets
    ${WIRINGPI_LIB}
    ${OpenCV_LIBS}
    pthread
    dl
    m
    rt
)
# -------------------------------

# --- Install -------------------
install(TARGETS testOpenPi
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
)
# -------------------------------

```
## main.cpp
```
#include <QApplication>
#include <QLabel>
#include <QTimer>
#include <QDebug>
#include <opencv2/opencv.hpp>
#include <QImage>
#include <QPixmap>
#include <memory>

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QLabel label;
    label.setWindowTitle("Color Tracking RTSP Stream");
    label.resize(640, 480);
    label.show();

    QString rtspUrl = "rtsp://admin:Admin@192.168.30.202:554/rtpvideoch11.sdp";

    auto cap = std::make_shared<cv::VideoCapture>(rtspUrl.toStdString());
    if (!cap->isOpened()) {
        qDebug() << "Không mở được RTSP stream";
        return -1;
    }

    QTimer *timer = new QTimer(&app);
    QObject::connect(timer, &QTimer::timeout, &label, [cap, &label, rtspUrl]() mutable {
        cv::Mat frame;
        (*cap) >> frame;
        if (frame.empty()) {
            qDebug() << "Frame rỗng, reconnecting...";
            cap->open(rtspUrl.toStdString());
            if (!cap->isOpened()) {
                qDebug() << "Reconnect thất bại!";
            }
            return;
        }

        cv::Mat hsv;
        cv::cvtColor(frame, hsv, cv::COLOR_BGR2HSV);

        cv::Mat mask1, mask2, mask;
        cv::inRange(hsv, cv::Scalar(0, 120, 70), cv::Scalar(10, 255, 255), mask1);
        cv::inRange(hsv, cv::Scalar(170, 120, 70), cv::Scalar(180, 255, 255), mask2);
        cv::bitwise_or(mask1, mask2, mask);

        cv::Mat kernel = cv::getStructuringElement(cv::MORPH_RECT, cv::Size(3,3));
        cv::erode(mask, mask, kernel, cv::Point(-1, -1), 2);
        cv::dilate(mask, mask, kernel, cv::Point(-1, -1), 2);

        std::vector<std::vector<cv::Point>> contours;
        cv::findContours(mask, contours, cv::RETR_TREE, cv::CHAIN_APPROX_SIMPLE);

        if (!contours.empty()) {
            size_t largest_idx = 0;
            double max_area = 0;
            for (size_t i = 0; i < contours.size(); i++) {
                double area = cv::contourArea(contours[i]);
                if (area > max_area) {
                    max_area = area;
                    largest_idx = i;
                }
            }

            if (max_area > 500) {
                cv::Rect bounding = cv::boundingRect(contours[largest_idx]);
                cv::rectangle(frame, bounding, cv::Scalar(0, 255, 0), 2);
            }
        }

        cv::cvtColor(frame, frame, cv::COLOR_BGR2RGB);
        QImage img((const uchar*)frame.data, frame.cols, frame.rows, frame.step, QImage::Format_RGB888);
        label.setPixmap(QPixmap::fromImage(img.copy()).scaled(label.size(), Qt::KeepAspectRatio));
    });

    timer->start(30);

    return app.exec();
}
```
# For Host (Optional)

```bash
cd $HOME
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git

mkdir -p $HOME/opencv/build
cd $HOME/opencv/build

cmake ../ \
  -GNinja \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=$HOME/opencv-install \
  -DOPENCV_EXTRA_MODULES_PATH=$HOME/opencv_contrib/modules \
  -DWITH_GSTREAMER=ON \
  -DWITH_FFMPEG=ON \
  -DWITH_V4L=ON \
  -DBUILD_EXAMPLES=OFF \
  -DBUILD_TESTS=OFF \
  -DBUILD_DOCS=OFF

ninja -j8
ninja install

```
