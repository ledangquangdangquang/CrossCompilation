<h1 align = "center">SQLite and PostGreSQL</h1>

## Build for host
```bash
sudo apt update
sudo apt install libpq-dev
cd $HOME/qt6/host-build
rm -rf *
$ cmake ../src/qtbase-everywhere-src-6.5.1/ -GNinja \
  -DCMAKE_BUILD_TYPE=Release \
  -DINPUT_opengl=es2 \
  -DQT_BUILD_EXAMPLES=OFF \
  -DQT_BUILD_TESTS=OFF \
  -DCMAKE_INSTALL_PREFIX=/home/quang/qt6/host \
  -DFEATURE_sql=ON \
  -DFEATURE_sql_psql=ON
  
cmake --build . --parallel 8
cmake --install .
```

## Build for Pi
```bash
cd $HOME/qt6/pi-build
rm -rf *
cmake ../src/qtbase-everywhere-src-6.5.1/ -GNinja -DCMAKE_BUILD_TYPE=Release -DINPUT_opengl=es2 -DQT_BUILD_EXAMPLES=OFF -DQT_BUILD_TESTS=OFF -DQT_HOST_PATH=$HOME/qt6/host -DCMAKE_STAGING_PREFIX=$HOME/qt6/pi -DCMAKE_INSTALL_PREFIX=/usr/local/qt6 -DCMAKE_TOOLCHAIN_FILE=$HOME/qt6/toolchain.cmake -DQT_QMAKE_TARGET_MKSPEC=devices/linux-rasp-pi4-aarch64 -DQT_FEATURE_xcb=ON -DFEATURE_xcb_xlib=ON -DQT_FEATURE_xlib=ON -DFEATURE_sql=ON -DFEATURE_sql_psql=ON
cmake --build . --parallel 8
cmake --install .
```
