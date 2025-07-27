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

***2. Edit `CMakeLists.txt`*** 

***3. ERORR: Could not find the WiringPi library*** 

