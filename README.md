<h1 align="center"> Cross compilation of Qt6.5.1 for RPI</h1>

- Cross Compilation https://youtu.be/8kpHgNKPooc
- Remote Debugging https://youtu.be/QWz-4R4kMIo
- Localization https://youtu.be/JtTtzYZ_Nk0
- Reference (LearnQT): https://youtube.com/playlist?list=PLw1hBEGKfRbmvt57e-JriZclgbSSHYzwH&feature=shared 
- Reference (MuyePan): https://www.youtube.com/watch?v=8kpHgNKPooc
> [!IMPORTANT]
> * ***Change the hostname and username of Raspberry Pi OS (`pi@192.168.30.77`)***
> * ***Change the username of Ubuntu (`quang`)***
> * ***Only one line to copy for each step.***
> * ***One terminal on the host to set up.***
> * ***Plz be patient and calm.***

# System Requirements
* Ubuntu 
```
Distributor ID:	Ubuntu
Description:	Ubuntu 22.04.5 LTS
Release:	22.04
Codename:	jammy
```
* Raspberry Pi Os 
```
Distributor ID:	Debian
Description:	Debian GNU/Linux 11 (bullseye)
Release:	11
Codename:	bullseye
```
# Prepare RPI
Install the lastest 64bit Raspberry Pi OS with desktop and update the system.
Check firmware:
```
cat /boot/.firmware_revision
```
```bash
sudo apt update
sudo apt full-upgrade
sudo reboot
sudo rpi-update 6db8c1cdd3da2f070866d2149c956ce86a4ccdd5
sudo reboot
```

Install necessary packages.
```
sudo apt-get install libboost-all-dev libudev-dev libinput-dev libts-dev libmtdev-dev libjpeg-dev libfontconfig1-dev libssl-dev libdbus-1-dev libglib2.0-dev libxkbcommon-dev libegl1-mesa-dev libgbm-dev libgles2-mesa-dev mesa-common-dev libasound2-dev libpulse-dev gstreamer1.0-omx libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev  gstreamer1.0-alsa libvpx-dev libsrtp2-dev libsnappy-dev libnss3-dev "^libxcb.*" flex bison libxslt-dev ruby gperf libbz2-dev libcups2-dev libatkmm-1.6-dev libxi6 libxcomposite1 libfreetype6-dev libicu-dev libsqlite3-dev libxslt1-dev 
```
```
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libx11-dev freetds-dev libsqlite3-dev libpq-dev libiodbc2-dev firebird-dev libxext-dev libxcb1 libxcb1-dev libx11-xcb1 libx11-xcb-dev libxcb-keysyms1 libxcb-keysyms1-dev libxcb-image0 libxcb-image0-dev libxcb-shm0 libxcb-shm0-dev libxcb-icccm4 libxcb-icccm4-dev libxcb-sync1 libxcb-sync-dev libxcb-render-util0 libxcb-render-util0-dev libxcb-xfixes0-dev libxrender-dev libxcb-shape0-dev libxcb-randr0-dev libxcb-glx0-dev libxi-dev libdrm-dev libxcb-xinerama0 libxcb-xinerama0-dev libatspi2.0-dev libxcursor-dev libxcomposite-dev libxdamage-dev libxss-dev libxtst-dev libpci-dev libcap-dev libxrandr-dev libdirectfb-dev libaudio-dev libxkbcommon-x11-dev gdbserver
```
Make a folder for qt6 installation.
```
sudo mkdir /usr/local/qt6
```
Grant full access to the fold used for the deployment from Qt Creator. 
```
sudo chmod 777 /usr/local/bin
```
Remember versions of gcc(10.2.1), ld(2.35.2) and ldd(2.31). Source code of the same version should be downloaded to build cross compiler later.

![image](https://github.com/MuyePan/CrossCompileQtForRpi/assets/136073506/ba2f1848-0c5c-426d-8d3f-1420931637bd)

Append following piece of code to the end of ~/.bashrc.
```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/qt6/lib/
```
Update the changes.
```
source ~/.bashrc
```
# Prepare host
Create a virtual machine for Ubuntu 22.04.2 and then update the system.
```
sudo apt update
sudo apt upgrade
```
Install necessary packages.
```
sudo apt-get install make build-essential libclang-dev ninja-build gcc git bison python3 gperf pkg-config libfontconfig1-dev libfreetype6-dev libx11-dev libx11-xcb-dev libxext-dev libxfixes-dev libxi-dev libxrender-dev libxcb1-dev libxcb-glx0-dev libxcb-keysyms1-dev libxcb-image0-dev libxcb-shm0-dev libxcb-icccm4-dev libxcb-sync-dev libxcb-xfixes0-dev libxcb-shape0-dev libxcb-randr0-dev libxcb-render-util0-dev libxcb-util-dev libxcb-xinerama0-dev libxcb-xkb-dev libxkbcommon-dev libxkbcommon-x11-dev libatspi2.0-dev libgl1-mesa-dev libglu1-mesa-dev freeglut3-dev build-essential gawk git texinfo bison file wget libssl-dev gdbserver gdb-multiarch libxcb-cursor-dev
```
## Build the lastest CMake from source
```
cd ~
git clone https://github.com/Kitware/CMake.git
cd CMake
./bootstrap && make -j8&& sudo make install
```
![image](https://github.com/MuyePan/CrossCompileQtForRpi/assets/136073506/e11f4e6c-a4c0-4f03-86dc-b6d463bab80b)

Folder CMake is not need any more. You can delete it.

## Build gcc as a cross compiler
Download necessary source code. **You should modify the following commands to your needs.**
For the time I make this page, they are:
* gcc 10.3.0(gcc version 10.2.1 does not exist and use the closest one)
* binutils 2.35.2(ld version)
* glibc 2.31(ldd version)
```
cd ~
mkdir gcc_all && cd gcc_all
wget https://ftpmirror.gnu.org/binutils/binutils-2.35.2.tar.bz2
wget https://ftpmirror.gnu.org/glibc/glibc-2.31.tar.bz2
wget https://ftpmirror.gnu.org/gcc/gcc-10.3.0/gcc-10.3.0.tar.gz
git clone --depth=1 https://github.com/raspberrypi/linux
tar xf binutils-2.35.2.tar.bz2
tar xf glibc-2.31.tar.bz2
tar xf gcc-10.3.0.tar.gz
rm *.tar.*
cd gcc-10.3.0
contrib/download_prerequisites
```
Make a folder for the compiler installation.
```
sudo mkdir -p /opt/cross-pi-gcc
sudo chown $USER /opt/cross-pi-gcc
export PATH=/opt/cross-pi-gcc/bin:$PATH
```
Copy the kernel headers in the above folder.
```
cd ~/gcc_all
cd linux
KERNEL=kernel7
make ARCH=arm64 INSTALL_HDR_PATH=/opt/cross-pi-gcc/aarch64-linux-gnu headers_install
```
Build Binutils. **You should modify the following commands to your needs.**
```
cd ~/gcc_all
mkdir build-binutils && cd build-binutils
../binutils-2.35.2/configure --prefix=/opt/cross-pi-gcc --target=aarch64-linux-gnu --with-arch=armv8 --disable-multilib
make -j 8
make install
```
Edit gcc-10.3.0/libsanitizer/asan/asan_linux.cpp. Add following piece of code.
```
#ifndef PATH_MAX
#define PATH_MAX 4096
#endif
```

Do a partial build of gcc. **You should modify the following commands to your needs.**
```
cd ~/gcc_all
mkdir build-gcc && cd build-gcc
../gcc-10.3.0/configure --prefix=/opt/cross-pi-gcc --target=aarch64-linux-gnu --enable-languages=c,c++ --disable-multilib
make -j8 all-gcc
make install-gcc
```
Partially build Glibc. **You should modify the following commands to your needs.**
```
cd ~/gcc_all
mkdir build-glibc && cd build-glibc
../glibc-2.31/configure --prefix=/opt/cross-pi-gcc/aarch64-linux-gnu --build=$MACHTYPE --host=aarch64-linux-gnu --target=aarch64-linux-gnu --with-headers=/opt/cross-pi-gcc/aarch64-linux-gnu/include --disable-multilib libc_cv_forced_unwind=yes
make install-bootstrap-headers=yes install-headers
make -j8 csu/subdir_lib
install csu/crt1.o csu/crti.o csu/crtn.o /opt/cross-pi-gcc/aarch64-linux-gnu/lib
aarch64-linux-gnu-gcc -nostdlib -nostartfiles -shared -x c /dev/null -o /opt/cross-pi-gcc/aarch64-linux-gnu/lib/libc.so
touch /opt/cross-pi-gcc/aarch64-linux-gnu/include/gnu/stubs.h
```
Back to gcc.
```
cd ~/gcc_all/build-gcc
make -j8 all-target-libgcc
make install-target-libgcc
```
Finish building glibc.
```
cd ~/gcc_all/build-glibc
make -j8
make install
```
Finish building gcc.
```
cd ~/gcc_all/build-gcc
make -j8
make install
```
At this point, we have a full cross compiler toolchain with gcc. Folder gcc_all is not need any more. You can delete it.

## Building Qt6
Make folders for sysroot and qt6.
```
cd ~
mkdir rpi-sysroot rpi-sysroot/usr rpi-sysroot/opt
mkdir qt6 qt6/host qt6/pi qt6/host-build qt6/pi-build qt6/src
```
Download QtBase source code
```
cd ~/qt6/src
wget https://download.qt.io/official_releases/qt/6.5/6.5.1/submodules/qtbase-everywhere-src-6.5.1.tar.xz
tar xf qtbase-everywhere-src-6.5.1.tar.xz
```
### Build Qt6 for host
```
cd $HOME/qt6/host-build/
cmake ../src/qtbase-everywhere-src-6.5.1/ -GNinja -DCMAKE_BUILD_TYPE=Release -DQT_BUILD_EXAMPLES=OFF -DQT_BUILD_TESTS=OFF -DCMAKE_INSTALL_PREFIX=$HOME/qt6/host
cmake --build . --parallel 8
cmake --install .
```
Binaries will be in $HOME/qt6/host
### Build Qt6 for rpi
copy and paste a few folders from rpi using rsync through SSH. **You should modify the following commands to your needs.**
```
cd ~
rsync -avz --rsync-path="sudo rsync" pi@192.168.30.77:/usr/include rpi-sysroot/usr
rsync -avz --rsync-path="sudo rsync" pi@192.168.30.77:/lib rpi-sysroot
rsync -avz --rsync-path="sudo rsync" pi@192.168.30.77:/usr/lib rpi-sysroot/usr 
rsync -avz --rsync-path="sudo rsync" pi@192.168.30.77:/opt/vc rpi-sysroot/opt
```
Create a file named toolchain.cmake in $HOME/qt6.
```
cmake_minimum_required(VERSION 3.18)
include_guard(GLOBAL)

set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR arm)

# You should change location of sysroot to your needs.
set(TARGET_SYSROOT /home/quang/rpi-sysroot)
set(TARGET_ARCHITECTURE aarch64-linux-gnu)
set(CMAKE_SYSROOT ${TARGET_SYSROOT})

set(ENV{PKG_CONFIG_PATH} $PKG_CONFIG_PATH:${CMAKE_SYSROOT}/usr/lib/${TARGET_ARCHITECTURE}/pkgconfig)
set(ENV{PKG_CONFIG_LIBDIR} /usr/lib/pkgconfig:/usr/share/pkgconfig/:${TARGET_SYSROOT}/usr/lib/${TARGET_ARCHITECTURE}/pkgconfig:${TARGET_SYSROOT}/usr/lib/pkgconfig)
set(ENV{PKG_CONFIG_SYSROOT_DIR} ${CMAKE_SYSROOT})

set(CMAKE_C_COMPILER /opt/cross-pi-gcc/bin/${TARGET_ARCHITECTURE}-gcc)
set(CMAKE_CXX_COMPILER /opt/cross-pi-gcc/bin/${TARGET_ARCHITECTURE}-g++)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -isystem=/usr/include -isystem=/usr/local/include -isystem=/usr/include/${TARGET_ARCHITECTURE}")
set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS}")

set(QT_COMPILER_FLAGS "-march=armv8-a")
set(QT_COMPILER_FLAGS_RELEASE "-O2 -pipe")
set(QT_LINKER_FLAGS "-Wl,-O1 -Wl,--hash-style=gnu -Wl,--as-needed -Wl,-rpath-link=${TARGET_SYSROOT}/usr/lib/${TARGET_ARCHITECTURE} -Wl,-rpath-link=$HOME/qt6/pi/lib")

set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY)
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)
set(CMAKE_BUILD_RPATH ${TARGET_SYSROOT})

include(CMakeInitializeConfigs)

function(cmake_initialize_per_config_variable _PREFIX _DOCSTRING)
  if (_PREFIX MATCHES "CMAKE_(C|CXX|ASM)_FLAGS")
    set(CMAKE_${CMAKE_MATCH_1}_FLAGS_INIT "${QT_COMPILER_FLAGS}")
        
    foreach (config DEBUG RELEASE MINSIZEREL RELWITHDEBINFO)
      if (DEFINED QT_COMPILER_FLAGS_${config})
        set(CMAKE_${CMAKE_MATCH_1}_FLAGS_${config}_INIT "${QT_COMPILER_FLAGS_${config}}")
      endif()
    endforeach()
  endif()


  if (_PREFIX MATCHES "CMAKE_(SHARED|MODULE|EXE)_LINKER_FLAGS")
    foreach (config SHARED MODULE EXE)
      set(CMAKE_${config}_LINKER_FLAGS_INIT "${QT_LINKER_FLAGS}")
    endforeach()
  endif()

  _cmake_initialize_per_config_variable(${ARGV})
endfunction()

set(XCB_PATH_VARIABLE ${TARGET_SYSROOT})

set(GL_INC_DIR ${TARGET_SYSROOT}/usr/include)
set(GL_LIB_DIR ${TARGET_SYSROOT}:${TARGET_SYSROOT}/usr/lib/${TARGET_ARCHITECTURE}/:${TARGET_SYSROOT}/usr:${TARGET_SYSROOT}/usr/lib)

set(EGL_INCLUDE_DIR ${GL_INC_DIR})
set(EGL_LIBRARY ${XCB_PATH_VARIABLE}/usr/lib/${TARGET_ARCHITECTURE}/libEGL.so)

set(OPENGL_INCLUDE_DIR ${GL_INC_DIR})
set(OPENGL_opengl_LIBRARY ${XCB_PATH_VARIABLE}/usr/lib/${TARGET_ARCHITECTURE}/libOpenGL.so)

set(GLESv2_INCLUDE_DIR ${GL_INC_DIR})
set(GLIB_LIBRARY ${XCB_PATH_VARIABLE}/usr/lib/${TARGET_ARCHITECTURE}/libGLESv2.so)

set(GLESv2_INCLUDE_DIR ${GL_INC_DIR})
set(GLESv2_LIBRARY ${XCB_PATH_VARIABLE}/usr/lib/${TARGET_ARCHITECTURE}/libGLESv2.so)

set(gbm_INCLUDE_DIR ${GL_INC_DIR})
set(gbm_LIBRARY ${XCB_PATH_VARIABLE}/usr/lib/${TARGET_ARCHITECTURE}/libgbm.so)

set(Libdrm_INCLUDE_DIR ${GL_INC_DIR})
set(Libdrm_LIBRARY ${XCB_PATH_VARIABLE}/usr/lib/${TARGET_ARCHITECTURE}/libdrm.so)

set(XCB_XCB_INCLUDE_DIR ${GL_INC_DIR})
set(XCB_XCB_LIBRARY ${XCB_PATH_VARIABLE}/usr/lib/${TARGET_ARCHITECTURE}/libxcb.so)

list(APPEND CMAKE_LIBRARY_PATH ${CMAKE_SYSROOT}/usr/lib/${TARGET_ARCHITECTURE})
list(APPEND CMAKE_PREFIX_PATH "/usr/lib/${TARGET_ARCHITECTURE}/cmake")
```
Fix absolute symbolic links
```
cd ~
wget https://raw.githubusercontent.com/riscv/riscv-poky/master/scripts/sysroot-relativelinks.py
chmod +x sysroot-relativelinks.py 
python3 sysroot-relativelinks.py rpi-sysroot
```
Compile source code for rpi.
```
cd $HOME/qt6/pi-build
cmake ../src/qtbase-everywhere-src-6.5.1/ -GNinja -DCMAKE_BUILD_TYPE=Release -DINPUT_opengl=es2 -DQT_BUILD_EXAMPLES=OFF -DQT_BUILD_TESTS=OFF -DQT_HOST_PATH=$HOME/qt6/host -DCMAKE_STAGING_PREFIX=$HOME/qt6/pi -DCMAKE_INSTALL_PREFIX=/usr/local/qt6 -DCMAKE_TOOLCHAIN_FILE=$HOME/qt6/toolchain.cmake -DQT_QMAKE_TARGET_MKSPEC=devices/linux-rasp-pi4-aarch64 -DQT_FEATURE_xcb=ON -DFEATURE_xcb_xlib=ON -DQT_FEATURE_xlib=ON
cmake --build . --parallel 8
cmake --install .
```
Send the binaries to rpi. **You should modify the following commands to your needs.**
```
rsync -avz --rsync-path="sudo rsync" $HOME/qt6/pi/* pi@192.168.30.77:/usr/local/qt6
```
## With Qt Creator
Download Qt Creator
```
cd ~ 
wget https://github.com/qt-creator/qt-creator/releases/tag/v10.0.0 
sudo dpkg -i qtcreator-linux-x64-10.0.0.deb 
rm qtcreator-linux-x64-10.0.0.deb 
```
Then open **Qt Creator** in `/opt/qt-creator/bin`

Set up **Compilers**.
![image](https://github.com/MuyePan/CrossCompileQtForRpi/assets/136073506/e98645c4-cf99-45e3-a8b4-ecc0899d6fa0)

Set up **Debuggers**.
![image](https://github.com/MuyePan/CrossCompileQtForRpi/assets/136073506/f75adf17-b8eb-4149-a5fc-cf59978aa3d9)

Set up **Devices**.
![image](https://github.com/MuyePan/CrossCompileQtForRpi/assets/136073506/57609ea4-6901-41a8-8264-c6bb7aeac844)

Click **Deploy Public Key...** to deploy the key. Create one if not existed.

Test the device.
![image](https://github.com/MuyePan/CrossCompileQtForRpi/assets/136073506/9883e600-7963-48e3-98fc-dc3f2e651bff)

Set up **Qt Versions**.
![image](https://github.com/MuyePan/CrossCompileQtForRpi/assets/136073506/6c43b6f0-a256-4d2d-86f6-80bb393602af)

Set up **Kits**.
![image](https://github.com/MuyePan/CrossCompileQtForRpi/assets/136073506/93e04b07-7cbc-43d6-a17c-53fe6d272de9)

On **CMake Configuration** opton, click Change and add follow commands. **You should modify the following commands to your needs.**
```
-DCMAKE_TOOLCHAIN_FILE:UNINITIALIZED=/home/quang/qt6/pi/lib/cmake/Qt6/qt.toolchain.cmake
```
![image](https://github.com/MuyePan/CrossCompileQtForRpi/assets/136073506/d7c4600a-7058-4541-bdfd-ce184e7fd94c)

## Test HelloWorld
On **Help** option select **About Plugins**.Then uncheck **ClangCodeModel**(**No need for Qt Creator 10 or later**)..

![image](https://github.com/MuyePan/CrossCompileQtForRpi/assets/136073506/efb1db08-c5cc-4210-adfe-85507e36d329)

Append following piece of code to the end of CMakeLists.txt(**No need for Qt Creator 10 or later**).
```
install(TARGETS HelloWorld
    RUNTIME DESTINATION ""
    BUNDLE DESTINATION ""
    LIBRARY DESTINATION ""
)
```
Goto **Projects**
Under **Run** section, on **X11 Forwarding** check **Forward to local display** and input :0 to the text field. 
![image](https://github.com/MuyePan/CrossCompileQtForRpi/assets/136073506/b396954b-fb04-48ae-a3c4-8ae67178513e)

Under **Environment** section, click **Details** to expand the environment option. Click **Add**, then on **Variable** column type **LD_LIBRARY_PATH**. On the **Value** column, type **:/usr/local/qt6/lib/**.
![image](https://github.com/MuyePan/CrossCompileQtForRpi/assets/136073506/059f275c-bfa4-4357-b4b6-82880b5c1054)

Run.

![image](https://github.com/MuyePan/CrossCompileQtForRpi/assets/136073506/ee26ad77-f370-433b-8734-89e70c21903c)

We have HelloWorld running on rpi now.
## Add QML module
> [!TIP]
> - ***Create a copy of the host-build and pi-build folders.***
> - ***It is recommended to start copying from the block below.***

Download source code.
```
cd ~/qt6/src
wget https://download.qt.io/official_releases/qt/6.5/6.5.1/submodules/qtshadertools-everywhere-src-6.5.1.tar.xz
tar xf qtshadertools-everywhere-src-6.5.1.tar.xz
wget https://download.qt.io/official_releases/qt/6.5/6.5.1/submodules/qtdeclarative-everywhere-src-6.5.1.tar.xz
tar xf qtdeclarative-everywhere-src-6.5.1.tar.xz
```
You can check dependencies at ~/qt6/src/qtdeclarative-everywhere-src-6.5.1/dependencies.yaml and ~/qt6/src/qtshadertools-everywhere-src-6.5.1/dependencies.yaml
Make sure required modules should be built and installed first. 

Build the modules for host
```
cd ~/qt6/host-build
rm -rf *
$HOME/qt6/host/bin/qt-configure-module ../src/qtshadertools-everywhere-src-6.5.1
cmake --build . --parallel 8
cmake --install .
rm -rf *
$HOME/qt6/host/bin/qt-configure-module ../src/qtdeclarative-everywhere-src-6.5.1
cmake --build . --parallel 8
cmake --install .
```
Build the modules for rpi
```
cd ~/qt6/pi-build
rm -rf *
$HOME/qt6/pi/bin/qt-configure-module ../src/qtshadertools-everywhere-src-6.5.1
cmake --build . --parallel 8
cmake --install .
rm -rf *
$HOME/qt6/pi/bin/qt-configure-module ../src/qtdeclarative-everywhere-src-6.5.1
cmake --build . --parallel 8
cmake --install .
```
Send the binaries to rpi. **You should modify the following commands to your needs.**
```
rsync -avz --rsync-path="sudo rsync" $HOME/qt6/pi/* pi@192.168.30.77:/usr/local/qt6
```
## Test HelloWorldQml
***Create Project -> Select Qt Quick Application to enable QML project***
![image](https://github.com/MuyePan/CrossCompileQtForRpi/assets/136073506/f67fd349-3537-42f0-8e15-244f138a09d4)
> [!IMPORTANT]
> * Each new project
>     * Under **Run** section, on **X11 Forwarding** check **Forward to local display** and input :0 to the text field. 
>     * Under **Environment** section, click **Details** to expand the environment option. Click **Add**, then on **Variable** column type **LD_LIBRARY_PATH**. On the **Value** column, type **:/usr/local/qt6/lib/**.
>       ```
>       LD_LIBRARY_PATH
>       ```
>       ```
>       :/usr/local/qt6/lib
>        ```

---
Once everything is set up, each time you build in Qt Creator, the build output will automatically appear in `/usr/local/bin` on the Raspberry Pi OS.
