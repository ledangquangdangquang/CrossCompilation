<h1 align="center">General-Purpose Input/Output Setup for Qt 6 Cross-Compilation</h1>

## On Raspberry Pi
***1. Download wiringPi***
```
cd ~
git clone https://github.com/WiringPi/WiringPi.git
```

***2. Build wiringPi***
```
./build debian
mv debian-template/wiringpi-3.x.deb .
sudo apt install ./wiringpi-3.x.deb
```

***3. Check if wiringPi is installed correctly***
* Check version of gpio
    ```
    gpio -v 
    ```

* Check in `/usr/lib`
    ```
    ls -l /usr/lib
    ```
    Check available file `libwiringPiDev.so` and `libwiringPi.so`
* Check in `/usr/include`
    ```
    ls -l /usr/include
    ```
    Check available file `wiringPi.h`
## On host (Ubuntu)
***1. rsync***
```
rsync -avz --rsync-path="sudo rsync" pi@192.168.30.77:/usr/include rpi-sysroot/usr
rsync -avz --rsync-path="sudo rsync" pi@192.168.30.77:/usr/lib rpi-sysroot/usr 
```
***2. Edit `CMakeLists.txt`*** 
Create new wiget project in **Qt creator** then append this text in file **CMakeLists.txt** 
```
# --------------------------------------------------------------------------------
#              			 --- Wiring Pi library set up ---
find_library(WIRINGPI_LIB wiringPi PATHS /usr/lib)

if(WIRINGPI_LIB)
    target_link_libraries(gpioTest PRIVATE Qt${QT_VERSION_MAJOR}::Widgets ${WIRINGPI_LIB})
else()
    message(FATAL_ERROR "Khong tim thay thu vien wiring Pi")
endif()
# --------------------------------------------------------------------------------
```
***3. ERORR: Could not find the WiringPi library*** 

