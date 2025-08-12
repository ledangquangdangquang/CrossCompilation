# Installation
# For Host (x86_64)
## 0. Important
```
sudo apt install gstreamer1.0-plugins-base \\
                 gstreamer1.0-plugins-good \\
                 gstreamer1.0-plugins-bad \\
                 gstreamer1.0-plugins-ugly \\
                 gstreamer1.0-libav \\
                 gstreamer1.0-tools \\
                 v4l-utils\
```
## 1.
```
cd ~/qt6/src
wget https://download.qt.io/official_releases/qt/6.5/6.5.1/submodules/qtmultimedia-everywhere-src-6.5.1.tar.xz
tar xf qtmultimedia-everywhere-src-6.5.1.tar.xz
```
## 2. 
```
cd ~/qt6/host-build
rm -rf *
$HOME/qt6/host/bin/qt-configure-module ../src/qtmultimedia-everywhere-src-6.5.1
cmake --build . --parallel 8
cmake --install .
```
## 3. Qt Creator 
`CMakeLists.txt`
```
cmake_minimum_required(VERSION 3.14)

project(camtest2 LANGUAGES CXX)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(Qt6 COMPONENTS Core Network Multimedia MultimediaWidgets REQUIRED)

add_executable(camtest2
  main.cpp
)
# Link thư viện Qt
target_link_libraries(camtest2
    Qt6::Core
    Qt6::Network
    Qt6::MultimediaWidgets
    Qt6::Multimedia
)
install(TARGETS camtest2
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})
```
# For RPI (ARM64)
## 0. On Raspberry Pi
```
 sudo apt-get install gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav
```
## 1. On Host
```
cd ~/qt6/src
wget https://download.qt.io/official_releases/qt/6.5/6.5.1/submodules/qtmultimedia-everywhere-src-6.5.1.tar.xz
tar xf qtmultimedia-everywhere-src-6.5.1.tar.xz
```
## 2. On Host
```
cd ~/qt6/pi-build
rm -rf *
$HOME/qt6/pi/bin/qt-configure-module ../src/qtmultimedia-everywhere-src-6.5.1
cmake --build . --parallel 8
cmake --install .
```
## 3. On Host
```
rsync -avz --rsync-path="sudo rsync" $HOME/qt6/pi/* pi@192.168.30.77:/usr/local/qt6
```
## 4. On Host
```
ln -s /home/quang/rpi-sysroot/usr/lib/aarch64-linux-gnu/pulseaudio/libpulsecommon-14.2.so /home/quang/rpi-sysroot/usr/lib/aarch64-linux-gnu/libpulsecommon-14.2.so
```
