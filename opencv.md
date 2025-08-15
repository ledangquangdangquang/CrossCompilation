<h1 align = "center">OpenCV </h1>

# For Pi
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
      -DBUILD_opencv_python3=OFF \
      -DBUILD_opencv_python2=OFF \
      -DWITH_JAVA=OFF \
      -DBUILD_TESTS=OFF \
      -DBUILD_EXAMPLES=OFF \
      -DWITH_IPP=OFF \
      -DWITH_TBB=OFF \
      -DWITH_OPENCL=OFF \
      -DWITH_OPENGL=OFF ..
```
```bash
make -j4
sudo make install
```
```bash
rsync -avz --rsync-path="sudo rsync" pi@192.168.30.77:/usr/local/lib rpi-sysroot/usr/local 
rsync -avz --rsync-path="sudo rsync" pi@192.168.30.77:/usr/local/include rpi-sysroot/usr/local
```
## CmakeLists.txt
```c
cmake_minimum_required(VERSION 3.18)
project(testOpenPi LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

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
