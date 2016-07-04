---
layout:     post
title:      "从零安装 Caffe (Ubuntu 14.04)"
subtitle:   "Install Caffe in Ubuntu 14.04 from Scratch"
date:       2015-08-03 23:16:00
author:     "Coldmooon"
header-img: "img/home-bg.jpg"
---

## 一、前言
本文记录了在新安装的 `ubuntu 14.04` 系统下安装 `caffe` 的过程。这里主要参考了两个链接：
1) <http://caffe.berkeleyvision.org/installation.html>
2) <http://ouxinyu.github.io/Blogs/20140723001.html>

很多人不太喜欢看官方教程，但其实 `caffe` 的官方安装指导做的非常好。我在看到 2) 之前，曾根据官方指导在 `OSX 10.9`, `10.10`, `Ubuntu 12.04`, `14.04` 下安装过 10 多次不同版本的 `caffe`，都成功了。

本文有不少内容参考了 1）和 2），但又有一些内容与二者不同。例如，2）中对 `gcc` 进行了降级，而我却对 `gcc` 进行了升级；与 2）的安装顺序也有些不同。我按照下面的顺序在一台新买的电脑上安装 `caffe`: 

安装 `win7/10`（略） --> 安装 `ubuntu 14.04`（略） --> 升级 `gcc 4.9` -> 安装 `nvidia` 显卡驱动 -> 安装 `cuda` 和 `cudnn` --> 安装 `anaconda` --> 安装 `Opencv 2.4.11` --> 安装 `Matlab` --> 安装 `Caffe`

我之所以要在装完系统的第一时间升级 `gcc 4.9`，是因为 `nvidia` 的驱动和 `cuda` 都需要 `gcc` 进行编译。如果先安装驱动和 `cuda`，再升级 `gcc` ，那么有时候会出现问题（我就遇到了）。当然，`ubuntu 14.04` 自带的 `gcc-4.8` 已经够用了。但我比较喜欢用最新的稳定版，所以就选择了升级 `gcc 4.9`。

后文将按照上述安装顺序来写。上述安装顺序还需要做一些调整，以后再慢慢改吧。

-------------------------------------------------------

## 二、升级 gcc 4.9

如果只用 `ubuntu 14.04` 自带的 `gcc-4.8` 则本节可以跳过。

```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get install gcc-4.9
sudo apt-get install g++-4.9
```
此时，系统默认使用的还是 `gcc 4.8`。如果想把 `gcc 4.9` 设为默认, 必须要重新做一下软连接

```
sudo ln -sf /usr/bin/gcc-4.9 /usr/bin/gcc
sudo ln -sf /usr/bin/gcc-ar-4.9 /usr/bin/gcc-ar
sudo ln -sf /usr/bin/gcc-ranlib-4.9 /usr/bin/gcc-ranlib
```
然后，可以用 `gcc -v` 来查看 `gcc` 的版本。

```
...
Thread model: posix
gcc version 4.9.2 (Ubuntu 4.9.2-0ubuntu1~14.04) 
```

**注意**: 在编译 `matcaffe` 接口时，可能会出现警告:

```
$ make matcaffe -j 8
MEX matlab/+caffe/private/caffe_.cpp
Building with 'g++'.

Warning: You are using gcc version '4.9.2-0ubuntu1~14.04)'. The version of gcc is not supported. The version currently supported with MEX is '4.7.x'. For a list of currently supported compilers see: http://www.mathworks.com/support/compilers/current_release.
MEX completed successfully.
```
暂且忽略它, 后面我会做一些配置。反正我目前在 `ubuntu 14.04` 和 `OSX 10.10` 两个系统里用 `gcc-4.9` 做 `RCNN` 实验没遇到问题。

参考链接:
<http://askubuntu.com/questions/428198/getting-installing-gcc-g-4-9-on-ubuntu>

-------------------------

## 三、安装 nvidia 显卡驱动

如果电脑没有 `nvidia` 的显卡，此步跳过。

网上的许多教程都指出要进入 `tty`，然后把 `lightdm` 关了。但我发现直接用 `apt-get` 安装的话，无需关闭 `lightdm`。

```
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt-get update
sudo apt-get install nvidia-352 nvidia-settings nvidia-prime
```

> 注意：
> 之前的第三方源 `xorg-edgers` 已经被 ubuntu 和 nvidia 合作的官方源
> `graphics-drivers` 代替。现在已经无法通过 xorg-edgers 源来安装新驱动。见链接: [[1]](http://www.omgubuntu.co.uk/2015/08/ubuntu-nvidia-graphics-drivers-ppa-is-ready-for-action), [[2]](http://askubuntu.com/questions/655322/i-want-to-install-nvidia-module-352-30-on-ubuntu-14-04-2-video-cards-intel-and)

**重启电脑。**

若要安装其他版本的驱动，则输入:

```
# 331 driver
sudo apt-get install nvidia-331

# 334 driver
sudo apt-get install nvidia-334

# install the latest version
sudo apt-get install nvidia-current
```
安装完后，输入 `prime-select query` 查看当前正在使用的显卡。

```
$ prime-select query
nvidia
```
输入 `cat /proc/driver/nvidia/version` 查看正在使用的 `nvidia` 驱动版本和编译时采用的 `gcc` 版本

```
$ cat /proc/driver/nvidia/version 
NVRM version: NVIDIA UNIX x86_64 Kernel Module  352.30  Tue Jul 21 18:53:45 PDT 2015
GCC version:  gcc version 4.9.2 (Ubuntu 4.9.2-0ubuntu1~14.04) 
```

虽然 `cuda` 里已经包含了 `nvidia` 驱动，但是根据 `caffe` 官方指导，`cuda` 与显卡驱动最好分开安装。

参考链接:
<http://ubuntuhandbook.org/index.php/2015/04/install-nvidia-driver-346-59-in-ubuntu-from-ppa/>
<http://www.binarytides.com/install-nvidia-drivers-ubuntu-14-04/>
<http://my.oschina.net/eechen/blog/227134>
https://devtalk.nvidia.com/default/topic/810964/linux/black-screen-after-prime-select-nvidia-and-log-out-using-v346-35-drivers/2/

---------------------------------


## 四、安装 cuda 7.0

如果电脑没有 `nvidia` 的显卡，此步跳过。有人说即使电脑上没有 `nvidia` 显卡也必须装 `cuda`, 否则会出问题。但我亲自实验过，完全可以不装 `cuda`。但必须在 `makefile` 文件中把 `CPU_ONLY := 1` 打开。并且不能使用 `cuda` 相关函数。如果使用 `cuda` 相关函数，则会报错。

从 [`cuda` 官方网站](https://developer.nvidia.com/cuda-downloads) 下载对应的 `deb`包。然后双击，在软件中心里安装。此时并没有完成安装，`deb` 包只是告诉系统去哪里下载 `cuda` 而已。

> Why doesn't the cuda-repo package install the CUDA Toolkit and Drivers?
>
> When using RPM or Deb, the downloaded package is a repository package. Such
> a package only informs the package manager where to find the actual installation
> packages, but will not install them.
>
> CUDA_Getting_Started_Linux.pdf

接下来输入下列命令安装 `cuda`:

```
sudo apt-get update
sudo apt-get install cuda
```
安装完成后，再配置环境

```
export PATH=/usr/local/cuda-7.0/bin:$PATH    
export LD_LIBRARY_PATH=/usr/local/cuda-7.0/lib64:$LD_LIBRARY_PATH    
```
另外，我发现上面的 `export` 操作在我电脑上不起作用。所以我直接把 `lib64` 里的库文件软连接到了 `/usr/local/lib/` 下 

PS: 安装 `cuda` 后，或许之前安装的显卡驱动会被 `cuda` 里的驱动覆盖掉一部分。此时不妨再次运行显卡驱动安装命令来检查一下是不是有需要重新安装的部分:

```
sudo apt-get install nvidia-352
```
```
nvidia-352 is already the newest version.
The following packages were automatically installed and are no longer required:
  account-plugin-windows-live cuda-command-line-tools-7-0 cuda-core-7-0
  cuda-cublas-7-0 cuda-cublas-dev-7-0 cuda-cudart-7-0 cuda-cudart-dev-7-0
  cuda-cufft-7-0 cuda-cufft-dev-7-0 cuda-curand-7-0 cuda-curand-dev-7-0
  cuda-cusolver-7-0 cuda-cusolver-dev-7-0 cuda-cusparse-7-0
  cuda-cusparse-dev-7-0 cuda-documentation-7-0 cuda-driver-dev-7-0
  cuda-license-7-0 cuda-misc-headers-7-0 cuda-npp-7-0 cuda-npp-dev-7-0
  cuda-nvrtc-7-0 cuda-nvrtc-dev-7-0 cuda-samples-7-0 cuda-toolkit-7-0
  cuda-visual-tools-7-0 freeglut3 freeglut3-dev libepoxy0 libevdev2 libllvm3.5
  libxmu-dev libxmu-headers libxt-dev libxvmc1 nvidia-modprobe
Use 'apt-get autoremove' to remove them.
0 upgraded, 0 newly installed, 0 to remove and 313 not upgraded.
```

--------------------------------

## 五、安装 cudnn

如果电脑没有 `nvidia` 的显卡，此步跳过。从官方网站下载 `cudnn` 后解压。得到的文件是 `.h` 和 `.so` 文件。所以，直接把他们拷贝到 `/usr/local/include` 和  `/usr/local/lib/` 下就好了。

```
sudo cp cudnn.h /usr/local/include
sudo cp libcudnn.so.6.5.48 /usr/local/lib

sudo ln -s /usr/local/lib/libcudnn.so.6.5.48 /usr/local/lib/libcudnn.so.6.5
sudo ln -s /usr/local/lib/libcudnn.so.6.5 /usr/local/lib/libcudnn.so

sudo ldconfig
```
**注意:** 检查一下刚刚拷贝到 `/usr/local/lib` 下的 `libcudnn.so.6.5.48` 的文件权限。

```
$ ls -l *cudnn*

lrwxrwxrwx 1 root root       33  8月  4 22:05 libcudnn.so -> /usr/local/lib/libcudnn.so.6.5.48
lrwxrwxrwx 1 root root       18  8月  4 22:09 libcudnn.so.6.5 -> libcudnn.so.6.5.48
-rw------- 1 root root 11172416  8月  2 23:18 libcudnn.so.6.5.48
-rw------- 1 root root 11623922  8月  2 23:19 libcudnn_static.a
```
从上面的显示结果可以看到，`libcudnn.so.6.5.48` 对于 `others` 用户是没有读取权限的，这会导致编译 `caffe`时出现下列错误: 

```
AR -o .build_release/lib/libcaffe.a
LD -o .build_release/lib/libcaffe.so
/usr/bin/ld: cannot find -lcudnn
collect2: error: ld returned 1 exit status
make: *** [.build_release/lib/libcaffe.so] Error 1
```
解决方法很简单，只要赋予 `others` 可读(写)权限即可:

```
sudo chmod 755 libcudnn.so.6.5.48
```

---------------------------------------------------


## 六、安装 anaconda
强烈推荐使用 `anaconda` 的 `python`。它里面集成了很多包，`ipython`, `mkl`, `numpy`等都预装了， 省去了很多麻烦。如果有 edu 邮箱的话，还可以获得 [`anaconda accelerate`](https://store.continuum.io/cshop/academicanaconda)，在矩阵运算的时候，可以启用并行计算，速度快很多。

```
安装 anaconda:
./Anaconda-2.3.0-Linux-x86_64.sh

安装 accelerate:
conda update conda
conda install accelerate
conda install iopro
```
接下来拷贝 `anaconda` 的许可文件到用户主目录

```
mv license_academic_20150611072013.txt ~/.continuum
```
然后升级 `ipython`, 如果不用 `ipython`，那就跳过下面这一步:

```
conda update ipython
conda update ipython-notebook
conda update ipython-qtconsole
```
下面测试一下 `anaconda python` 的功能，首先在终端下启用 `ipython-notebook`

```
$ ipython notebook
``` 
然后新建一个 `ipynb` 文件。在 `cell` 中输入

```
In [1]:  import mkl
         mkl.set_num_threads(4) # 设置最大线程数
         mkl.get_max_threads()  # 查看当前线程数

Out[1]:  4
```
可见 `anaconda` 已经预装了 `MKL`。测一下速度:

```
In [23]: a = np.random.random((4096, 4096))
         %timeit np.dot(a,a)

         1 loops, best of 3: 6.44 s per loop
```
------------------------------

## 七、安装 Opencv 2.4.11
喜欢 `opencv 3.0` 的，可以选择安装 `opencv 3.0`。这里我继续使用 `Opencv 2.4.11`。`Opencv` 的安装过程较繁琐，且网上已经有了大量的安装教程，推荐三个:
<http://www.sysads.co.uk/2014/05/install-opencv-2-4-9-ubuntu-14-04-13-10/>
<http://rodrigoberriel.com/2014/10/installing-opencv-3-0-0-on-ubuntu-14-04/>
<http://karytech.blogspot.hu/2012/05/opencv-24-on-ubuntu-1204.html>

其中 `ffmepg`的安装需要注意。如果按照第一个链接里的方法，先卸载再重新安装 `ffmepg` 的话，在某些机器机器上可以编译通过 `opencv`，但在另一些机器上就会出错(两种情况我都遇到了。。)。出错的解决方法是自己下载 `ffmepg`，从源码开始编译。具体参照下面两个帖子:
<http://stackoverflow.com/questions/28319376/installing-opencv-in-ubuntu-14-10>
<http://stackoverflow.com/questions/26592577/installing-opencv-in-ubuntu-14-10>

这里提供一个脚本，方便在 `terminal` 下编译 `Opencv` 程序。
脚本来自: <https://jayrambhia.wordpress.com/2012/05/08/beginning-opencv/>

```
echo "compiling $1"
if [[ $1 == *.c ]]
then
    gcc -g `pkg-config --cflags opencv`  -o `dirname $1`/`basename $1 .c` $1 `pkg-config --libs opencv`;
elif [[ $1 == *.cpp ]]
then
    g++ -g `pkg-config --cflags opencv` -std=c++11 -std=gnu++11 -o `dirname $1`/`basename $1 .cpp` $1 `pkg-config --libs opencv`;
else
    echo "Please compile only .c or .cpp files"
fi
echo "Output file => ${1%.*}"
```
将上述代码保存为一个 `xxx.sh` 文件，名字自己起。然后在终端里给该文件开启可执行权限: 

```
sudo chmod 777 xxx.sh
```
接下来在 `.bashrc` 中建立一个 `alias` 来指向 `xxx.sh`

```
subl ~/.bashrc
```
在 `.bashrc` 中键入

```
alias opencv="/path/to/xxx.sh"
```
以后要编译 `opencv` 程序的时候，只需要在终端里输入 `opencv xxx.cpp` 即可。无需敲入繁琐的 `pkg-config` 前后缀。例如, 直接在终端里键入 `opencv` 命令，会提示

```
$ opencv

compiling 
Please compile only .c or .cpp files
Output file => 
```

其他可选教程：
https://nusharex.wordpress.com/2015/06/01/18/
快捷安装脚本：
https://gist.github.com/Coldmooon/c2e146bb7e960556e055

----------------------

## 八、安装 Matlab
安装 `matlab` 的过程这里不赘述，重点说下安装后做的事:
提供两种方法实现在 `terminal` 中启动 `matlab`:

1） 将 `matlab` 的可执行程序加入到系统的环境变量中

```
export PATH="/path/to/matlab:$PATH"
```
2） 与 `opencv` 相同，在 `.bashrc` 中建立一个 `matlab` alias:

```
alias matlab='/path/to/matlab'
```

方法 2) 的优势是，`matlab` 在启动时可以调用一些指定的库文件。因为，`matlab` 在启动时，会优先读取自带的 `opencv` 库，而不读取系统中安装好的 `opencv 2.4.11` 库。在这种情况下做 `RCNN` 的实验，就可能报错。

所以我在 `Mac OSX 10.10` 下，做了如下 alias:

```
alias rcnn="DYLD_INSERT_LIBRARIES=/usr/local/lib/libopencv_highgui.2.4.dylib:/usr/local/lib/libtiff.5.dylib /Applications/MATLAB_R2014b.app/bin/matlab"
```
以后要做 `Rcnn` 实验的时候，只需要在终端里输入 `rcnn` 就可以启动 `matlab` 并优先读取自己安装的 `opencv 2.4.11` 库。

而在 `Ubuntu 14.04` 里，我先建立了一个 `matlab` 脚本，用来启动 `matlab`:

```
$ vim matlab.sh
```
键入下面的内容，用户名改成自己的:

```
export LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libstdc++.so.6
/home/your_username/MATLAB/R2014b/bin/matlab
```
然后按 `:wq` 保存。再赋予可执行权限:

```
$ sudo chmod 777 matlab.sh
```
接下来在 `.bashrc` 中建立一个 `alias`，指向刚才建立的脚本:

```
vim ~/.bashrc

增加:
alias matlab="/path/to/matlab.sh"
```
以后在终端里输入 `matlab` 即可启动, 并预先读取 `libstdc++.so.6` 这个库，以防止出现 `GLIBCXX_3.4.20` 相关错误。出错的原因分析太长了，请看[上一篇文章的第 12 ](http://coldmooon.github.io/2015/07/09/caffe/)，里面有详细记载。

当然，如果在你的电脑上不出错的话，那就不需要这么干了。直接按照本节开头的 1), 2) 操作即可。

----------------------------

## 九、安装 MKL, Openblas or Atlas

### -- MKL
其实 `anaconda` 已经自带了 `MKL`, 但不不妨这里再装一下。首先去下面的链接下载学生版 `MKL`
<https://software.intel.com/en-us/intel-mkl>
安装过程不说了，基本直接下一步就可以了。接下来配置环境:

```
sudo vim /etc/ld.so.conf.d/intel_mkl.conf

输入:
/opt/intel/lib/intel64
/opt/intel/mkl/lib/intel64
关闭并保存文件。

sudo ldconfig
```

### -- Openblas
去[官方网站](http://www.openblas.net/) 下载 `OpenBLAS` 的安装包，解压。进入安装目录，在终端输入

```
$ make -j 8
```
安装成功之后，继续在终端输入

```
$ make install PREFIX=your_directory
```
注意，如果这里自己选择安装目录，则在编译 `Caffe` 的时候，会提示找不到 `OpenBLAS`的库文件，此时，需要进一步设置 `LD_LIBRARY_PATH` 才行。

```
根据具体的安装路径设置:
export LD_LIBRARY_PATH=/opt/OpenBLAS/lib
```

### -- Atlas

```
sudo apt-get install libatlas-base-dev
```
-----------------------------------------------

## 十、安装 Boost

进入 `Boost` 的官方网站 <http://www.boost.org/> 下载安装包。按照官方指南进行安装。
最简单的方法是直接在 `ubuntu` 的软件仓库里搜索 `libboost`。也在可以用 `apt-get` 安装:

```
sudo apt-get install libboost-all-dev
```
其实这一步可以放到下一节来做。 
-------------------------------------------------

## 十一、其他依赖库

按照 [官方指南](http://caffe.berkeleyvision.org/install_apt.html) 进行:

```
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install --no-install-recommends libboost-all-dev
```
```
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
```
安装过 `anaconda` 的话，那 `libhdf5-serial-dev` 可以不装。如果编译时提示找不到 `hdf5` 的库。就把 `anaconda/lib` 加到 `ld.so.conf` 中去。

```
$ sudo vim /etc/ld.so.conf

添加一行,用户名改为你自己的:
/home/your_username/anaconda/lib
关闭并保存文件。

$ sudo ldconfig
```

------------------------------------

## 十二、编辑 Caffe makefile 文件:

重点要改的地方，电脑有 `nvidia` 显卡的配置:

```
# cuDNN acceleration switch (uncomment to build with cuDNN).
USE_CUDNN := 1

# CPU-only switch (uncomment to build without GPU support).
# CPU_ONLY := 1

# To customize your choice of compiler, uncomment and set the following.
# N.B. the default for Linux is g++ and the default for OSX is clang++
# CUSTOM_CXX := g++

# CUDA directory contains bin/ and lib/ directories that we need.
CUDA_DIR := /usr/local/cuda
# On Ubuntu 14.04, if cuda tools are installed via
# "sudo apt-get install nvidia-cuda-toolkit" then use this instead:
# CUDA_DIR := /usr

# CUDA architecture setting: going with all of them.
# For CUDA < 6.0, comment the *_50 lines for compatibility.
CUDA_ARCH := -gencode arch=compute_20,code=sm_20 \
                -gencode arch=compute_20,code=sm_21 \
                -gencode arch=compute_30,code=sm_30 \
                -gencode arch=compute_35,code=sm_35 \
                -gencode arch=compute_50,code=sm_50 \
                -gencode arch=compute_50,code=compute_50

# BLAS choice:
# atlas for ATLAS (default)
# mkl for MKL
# open for OpenBlas
BLAS := mkl
# Custom (MKL/ATLAS/OpenBLAS) include and lib directories.
# Leave commented to accept the defaults for your choice of BLAS
# (which should work)!
# BLAS_INCLUDE := /path/to/your/blas
# BLAS_LIB := /path/to/your/blas

# MATLAB directory should contain the mex binary in /bin.
MATLAB_DIR := /home/your_username/MATLAB/R2014b
# MATLAB_DIR := /Applications/MATLAB_R2012b.app

# PYTHON_INCLUDE := /usr/include/python2.7 \
#               /usr/lib/python2.7/dist-packages/numpy/core/include
# Anaconda Python distribution is quite popular. Include path:
# Verify anaconda location, sometimes it's in root.
ANACONDA_HOME := $(HOME)/anaconda
PYTHON_INCLUDE := $(ANACONDA_HOME)/include \
                $(ANACONDA_HOME)/include/python2.7 \
                $(ANACONDA_HOME)/lib/python2.7/site-packages/numpy/core/include \

# We need to be able to find libpythonX.X.so or .dylib.
# PYTHON_LIB := /usr/lib
PYTHON_LIB := $(ANACONDA_HOME)/lib

# Uncomment to support layers written in Python (will link against Python libs)
# WITH_PYTHON_LAYER := 1
```
电脑没有 `nvidia` 显卡的配置:

```
# cuDNN acceleration switch (uncomment to build with cuDNN).
# USE_CUDNN := 1

# CPU-only switch (uncomment to build without GPU support).
CPU_ONLY := 1

# To customize your choice of compiler, uncomment and set the following.
# N.B. the default for Linux is g++ and the default for OSX is clang++
# CUSTOM_CXX := g++

# CUDA directory contains bin/ and lib/ directories that we need.
# CUDA_DIR := /usr/local/cuda
# On Ubuntu 14.04, if cuda tools are installed via
# "sudo apt-get install nvidia-cuda-toolkit" then use this instead:
# CUDA_DIR := /usr

# CUDA architecture setting: going with all of them.
# For CUDA < 6.0, comment the *_50 lines for compatibility.
# CUDA_ARCH := -gencode arch=compute_20,code=sm_20 \
#                 -gencode arch=compute_20,code=sm_21 \
#                 -gencode arch=compute_30,code=sm_30 \
#                 -gencode arch=compute_35,code=sm_35 \
#                 -gencode arch=compute_50,code=sm_50 \
#                 -gencode arch=compute_50,code=compute_50

...

其余部分保持不变
```
接下来编译。

------------------------

## 十三、编译

在终端下输入:

```
$ make all -j 8
$ make test -j 8
$ make runtest -j 8
```

接下来编译 `python` 接口和 `matlab` 接口:

```
$ make pycaffe -j 8
```
```
$ make matcaffe -j 8
```
在编译过程中如果遇到错误，可以在上一篇文章里搜索一下。

--------------------------

## 十四、散热系统（建议不要跳过本节内容）

无论是做计算，还是玩游戏，散热都是极其重要的。CPU 或 GPU 的温度一旦达到了设定的阈值，它们就开始自动降频。于是 i7 变赛扬，Titan X 变集显。。所以，如果不好好处理，散热系统很容易成为整个电脑性能的瓶颈。

现在主流的散热方式是风冷和水冷。水冷的效果好，静音，但是成本高，需要一定的动手能力。风冷要便宜一些，但是噪音大，并且需要高档点的机箱来构造`风道`。大部分人使用的还是风冷。所以接下来重点讲一下 `Nvidia GPU` 的风冷。

**-- GPU 的风冷:**
大家用的一般都是 `nvidia` 的游戏卡，例如 `Titan X`。这类显卡主要为游戏优化，其 `BIOS` 的温度阈值设定并不适合深度学习。所以，我们必须自己控制风扇速度。在 `windows` 下控制温度阈值很简单，一般正规的厂商，如华硕、技嘉、微星，都会配送显卡超频软件，可以自己 “画” 温控曲线。而在 `Ubuntu 14.04` 下控制风扇转速就要稍微麻烦些:

在 `Ubuntu 14.04` 下控制显卡风扇转速有两种方法:
   1. 刷修改版的 `BIOS`，改变温度阈值；
   2. 将 `xorg.conf` 文件的 `coolbits` 字段打开。

介绍第二种方法:

首先打开 `coolbits` 字段。装好 `nvidia` 显卡驱动后，在终端下输入:

```
$ sudo nvidia-xconfig
```
这时，会在 `/etc/X11/` 下自动生成 `xorg.conf` 文件。我们要做的就是在 `xorg.conf` 文件里，给 `coolbits` 字段设定一个值。于是，在终端下输入:

```
$ cd /etc/X11
$ sudo nvidia-xconfig --cool-bits=28
```
重启电脑。然后在终端下输入:

```
$ nvidia-settings
```
这时会开启显卡控制界面，在 `Thermal Settings` 选项卡中，可以通过滑块来控制风扇的转速。令 `coolbits=28` 的原因是:

```
4(开启 GPU 风扇控制功能)
+
8(启用调节时钟频率功能，也就是允许超频)
+
16(`nvidia-settings` 的命令行接口可以给 GPU 增加电压) 
= 
28(超频太猛的话，可能会烧坏你的显卡)...
```
除了在显卡控制界面里修改风扇转速外，还可以使用命令行控制，这在用 `SSH` 远程登录时非常有用:

```
查询显卡当前状态
nvidia-smi

仅仅读取 GPU 温度:
nvidia-smi --query-gpu=temperature.gpu --format=csv,noheader

查询与 GPU 风扇相关的关键字，例如风扇的转速等
nvidia-settings -q all | grep Fan

设定风扇转速
nvidia-settings -a "[gpu:0]/GPUFanControlState=1" -a "[fan:0]/GPUTargetFanSpeed=70" 
```
**-- CPU 的风冷**
`Haswell` 架构的 CPU 能耗做的不是很好，普遍发热都很严重。如果用 `i7 4790k` 做计算的话，一般的散热器都压不住温度。不用水冷的话，可以选大霜塔或猫头鹰等散热器。次一点的可以选玄冰400。整个机箱风道的构造是，机箱前进风，后、上出风：

```
机箱风道:
-----------
| ⬅️ ⬆️    |
| ️      ⬅️ |
|----------
```

参考链接:
<https://timdettmers.wordpress.com/2015/03/09/deep-learning-hardware-guide/>
<https://devtalk.nvidia.com/default/topic/820497/-solved-coolbits-without-xorg-conf-/>
<http://www.phoronix.com/scan.php?px=MTY1OTM&page=news_item>
<https://www.gpugrid.net/forum_thread.php?id=2925>
<http://askubuntu.com/questions/61396/how-do-i-install-the-nvidia-drivers>
https://devtalk.nvidia.com/default/topic/821563/linux/can-t-control-fan-speed-with-beta-driver-349-12/2/
http://discourse.ubuntu.com/t/lets-burn-it-nvidia-overcloking-now-available-on-linux/1610