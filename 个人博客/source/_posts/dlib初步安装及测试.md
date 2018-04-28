title: dlib初步安装及测试
date: 2018-04-01 
categories: 
tags: [Notes]

------

## [dlib](http://www.dlib.net/ml.html )

> dlib是一套包含了机器学习、计算机视觉、图像处理等的函数库，使用C++开发而成，目前广泛使用于工业及学术界，也应用于机器人、嵌入式系统、手机、甚至于大型的运算架构中，而且最重要的是，它不但是开源的而且还可以跨平台使用（Linux、Mac OS 、Windows），并且还提供了Python API。开发者是[Davis King]([http://blog.dlib.net] )

## dlib依赖

- dlib安装需要依赖库有`openblas`，`opencv`。可以直接使用`brew`安装。

  ```
  $ brew install openblas

  $ brew install opencv3 --with-python3 --c++11 --with-contrib
  $ brew link --force opencv3
  ```

  安装`opencv`可以参考[这个](https://gravityjack.com/news/opencv-python-3-homebrew/)，出现的问题有详细的说明

- Mac下安装X11

  `X11`是执行Unix程序的图形窗口环境。Mac OS X本身的程序是`Aqua`界面的，但是为了能够兼容`unix`和`linux`移植过来的程序（Mac OS X由BSD-UNIX修改而来），比如MatLab，就需要x11窗口环境。

  运行`dlib`需要`X11`，但Mac目前没有自带X11，需要重新下载安装，下载地址为：<https://www.xquartz.org/>，下载后直接安装，默认安装目录为`/opt/X11`，需要在`/usr/loca/opt`目录下创建软连接，创建命令如下，创建后重启Mac。

  ```
  $ cd /usr/local/opt

  $ ln -s /opt/X11 X11dlib
  ```

## dlib安装

- 下载dlib，也可直接去Git下载	

  ```
  git clone https://github.com/davisking/dlib.git
  ```

- 下载后解压，安装dlib

  ```
  cd dlib/examples
  mkdir build
  cd build
  cmake .. -DUSE_SSE4_INSTRUCTIONS=ON
  cmake --build . --config Release
  ```

- 安装python模块

  ```
  cd dlib

  sudo python setup.py install

  python

  # 不报错，说明安装python模块成功
  import dlib
  ```

## Demo

- 检测人脸

    ```
  cd dlib/examples/build/

  #下载face landmark模型
  wget http://dlib.net/files/shape_predictor_68_face_landmarks.dat.bz2

  # 解压文件

  ./webcam_face_pose_ex
    ```


- 利用`python`十行代码进行人脸检测

  首先需要安装相关依赖库

  ```
  brew install boost
  brew install boost-python
  pip install dlib
  pip install scikit-image
  ```

  具体代码`main.py`

  ```python
  import dlib
  from skimage import io
  from skimage.draw import polygon_perimeter

  detector = dlib.get_frontal_face_detector()
  sample_image = io.imread('目标图片')
  faces = detector(sample_image, 1)

  for d in faces:
      rr, cc = polygon_perimeter([d.top(), d.top(), d.bottom(), d.bottom()], [d.right(), d.left(), d.left(), d.right()])
      sample_image[rr, cc] = (0, 255, 0)
  io.imsave('识别后新生成图片', sample_image)
  ```

> 我是使用`python3`执行`main.py`的，需要注意的是要将`shape_predictor_68_face_landmarks.dat.bz2`的解压文件和`main.py`放在同一目录下