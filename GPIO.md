<h1 align="center">General-Purpose Input/Output Setup for Qt 6 Cross-Compilation</h1>

- Reference (LearnQT): https://www.youtube.com/watch?v=w31cLOh3wiE
> [!IMPORTANT]
> * ***Change the hostname and username of Raspberry Pi OS (`pi@192.168.30.77`)***
> * ***Change the username of Ubuntu (`quang`)***
> * ***Only one line to copy for each step.***
> * ***One terminal on the host to set up.***
> * ***Plz be patient and calm.***
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

Create new **Qt Widgets Application** project in **Qt creator** then edit file **CMakeLists.txt** 
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


* Delete line: `target_link_libraries(showImg PRIVATE Qt${QT_VERSION_MAJOR}::Widgets)` 
* Append this code:
    ```
    # --------------------------------------------------------------------------------
    #              			 --- Wiring Pi library set up ---
    find_library(WIRINGPI_LIB wiringPi PATHS /usr/lib)

    if(WIRINGPI_LIB)
        target_link_libraries(gpioTest PRIVATE Qt${QT_VERSION_MAJOR}::Widgets ${WIRINGPI_LIB})
    else()
        message(FATAL_ERROR "Không tìm thấy thư viện wiringPi")
    endif()
    # --------------------------------------------------------------------------------
    ```
    > [!NOTE]
    > `gpioTest` is the name of the project. If you have a different name, you should edit it accordingly.
***3. ERORR: Could not find the WiringPi library*** 
```
rsync -avz --rsync-path="sudo rsync" pi@192.168.30.77:/usr/local/lib rpi-sysroot/usr/local 
ln -sf ~/rpi-sysroot/usr/local/lib/libwiringPi.so.* libwiringPi.so
ln -sf ~/rpi-sysroot/usr/local/lib/libwiringPiDev.so.* libwiringPiDev.so
```
