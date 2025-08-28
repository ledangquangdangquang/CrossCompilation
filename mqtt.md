# 📡 Message Queuing Telemetry Transport (MQTT) với Raspberry Pi & Ubuntu (EC2 AWS)

## 🚀 Install 

### 🐧 Raspberry Pi

Install **Mosquitto** and **mosquitto-clients**:

```bash
sudo apt update
sudo apt install mosquitto mosquitto-clients
```

---

### ☁️ Ubuntu EC2 (Broker)

1. Install **Mosquitto** and **mosquitto-clients**:

```bash
sudo apt update
sudo apt install mosquitto mosquitto-clients
sudo systemctl enable mosquitto
sudo systemctl start mosquitto
```

2. Check Mosquitto have listening in port **1883**:

```bash
sudo ss -tulnp | grep 1883
```

👉 Expected `0.0.0.0:1883` 

> [!NOTE]
> If not see `0.0.0.0:1883` check **Security Groups** in AWS and edit inbound 

3. Edit file config Mosquitto:

```bash
sudo vi /etc/mosquitto/mosquitto.conf
```

Add:

```
listener 1883
allow_anonymous true
```

Restart mqtt:

```bash
sudo systemctl restart mosquitto
```

4 Test mqtt
* **Ubuntu (broker)**:

```bash
mosquitto_sub -h 0.0.0.0 -p 1883 -t "pi/to/ubuntu"
```

* **Pi (client)**:

```bash
mosquitto_pub -h <Ubuntu-IP> -p 1883 -t "pi/to/ubuntu" -m "Hello Ubuntu"
```
---

## 🔧 Build Qt MQTT

### 1. Clone source (Ubuntu)

```bash
cd ~/qt6/src
git clone https://code.qt.io/qt/qtmqtt.git
cd qtmqtt
git checkout 6.5
```

---

### 2. Build module

#### 🖥️ Build for host (Ubuntu)

```bash
cd $HOME/qt6/host-build
rm -rf *
cmake ../src/qtmqtt -GNinja \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=$HOME/qt6/host \
  -DQT_NO_PACKAGE_VERSION_CHECK=TRUE

cmake --build . --parallel 8
cmake --install .
```

#### 🍓 Build for Raspberry Pi

```bash
cd $HOME/qt6/pi-build
rm -rf *
cmake ../src/qtmqtt -GNinja \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_TOOLCHAIN_FILE=$HOME/qt6/toolchain.cmake \
  -DCMAKE_STAGING_PREFIX=$HOME/qt6/pi \
  -DCMAKE_INSTALL_PREFIX=/usr/local/qt6 \
  -DQT_HOST_PATH=$HOME/qt6/host \
  -DQT_NO_PACKAGE_VERSION_CHECK=TRUE

cmake --build . --parallel 8
cmake --install .
```

---

### 3. Copy binary to Pi

```bash
rsync -avz --rsync-path="sudo rsync" pi@192.168.30.77:/usr/local/lib rpi-sysroot/usr/local 
rsync -avz --rsync-path="sudo rsync" pi@192.168.30.77:/usr/local/include rpi-sysroot/usr/local
```

---

## 📄 CMakeLists.txt (project use Qt MQTT)

```cmake
cmake_minimum_required(VERSION 3.5)

project(testMQTTRPI VERSION 0.1 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

# Tìm Qt
find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Widgets)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Widgets Mqtt)

# Nguồn project
set(PROJECT_SOURCES
    main.cpp
)

# Tạo executable
add_executable(testMQTTRPI ${PROJECT_SOURCES})

# Link thư viện
target_link_libraries(testMQTTRPI PRIVATE Qt${QT_VERSION_MAJOR}::Widgets Qt::Mqtt)

# Cài đặt
install(TARGETS testMQTTRPI
    RUNTIME DESTINATION bin
)
```

