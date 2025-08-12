```
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
