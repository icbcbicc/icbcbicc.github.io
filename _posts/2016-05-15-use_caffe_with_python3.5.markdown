---
layout:     post
title:      "在 python3.5 下使用 Caffe"
subtitle:   "Using Caffe with Python3.5"
date:       2016-05-15 21:43:00
author:     "Coldmooon"
header-img: "img/home-bg.jpg"
---

## 一、前言
本文介绍如何在 `python3.5` 下使用 `caffe` 的 `python` 接口。之所以要强调 `python3.5` 是因为:  
1）`python3.5` 目前是 `python3` 的最新版;  
2）安装方法没官网介绍那么简单，里面暗桩很多。一不小心就遇到不给任何错误提示的情况下程序崩溃。而且`python3.5` 的安装方法要比低版本的 `python3.3/3.4` 麻烦。  
尽管如此，作为版本控我还是喜欢用新版。所以，经过了大量折腾之后总算找到了让 `caffe` 兼容 `python3.5` 的方法。

## 二、准备工作
我的主机环境是 `Ubuntu 14.04` + `Anaconda3-4.0`。要让 `caffe` 支持 `python3.5` 需要下面几样依赖:

- python3.5
- protobuf 3.0 或以上版本。
- libboost 1.55 或以上版本

## 三、正式配置

1）安装 `Anaconda3`

点击下载[`Anaconda3-4.0.0`](https://www.continuum.io/downloads)。运行下面的命令进行安装

```
bash Anaconda3-4.0.0-Linux-x86_64.sh
```
**注意：必须是 Anaconda3-4.0.0 以上版本，旧的 Anaconda3-2.5 版本不行!**

2）手动编译和安装 `protobuf` 3.0 以上版本。

必须是手动编译和安装，用 `apt-get` 安装的是 2.0 版本，不行。  
到 `protobuf` 的 [releases](https://github.com/google/protobuf/releases) 页面去下载两个安装包:

- protobuf-`cpp`-3.0.0-beta-2.zip 或以上版本；  
- protobuf-`python`-3.0.0-beta-2.zip 或以上版本。

**注意，仔细看 `protobuf` 的说明**。安装 `protobuf` 需要安装两样东西, 少一样都不行:  

- `the protocol compiler`;
- `the protobuf runtime`

因此, 接下来首先安装 `the protocol compiler` 和 `protobuf cpp runtime`。

```
解压第一个安装包 `protobuf-cpp-3.0.0-beta-2.zip`，并进入解压出来的目录
$ ./configure
$ make
$ make check
$ sudo make install
$ sudo ldconfig # refresh shared library cache.
```
这样 `the protocol compiler` 和 `protobuf cpp runtime` 就同时编译和安装好了。下面编译和安装 `protobuf python runtime`:

```
解压第二个安装包 `protobuf-python-3.0.0-beta-2.zip`，并进入解压出来的目录
$ cd python
$ python setup.py build
$ python setup.py test
$ python setup.py install
```
这样 `protobuf python runtime` 就编译和安装好了。注意 `protobuf python runtime` 是作为 `pip` 的包安装的。但是你可以从 `conda` 里面看到它:

```
$ conda list | grep protobuf
protobuf                  3.0.0b2                   <pip>
```
出现上面的结果就证明 `protobuf3.0` 已经安装完成了。

3）安装 `libboost`  
**注意，这一步跟官方安装教程不一样，而且是必须的**:

```
sudo apt-get install libboost1.55-all-dev
```
如果有 1.55 以上版本更好，没有就安装 1.55 版本。接下来做一个**关键步骤**:

```
sudo ln -s /usr/lib/x86_64-linux-gnu/libboost_python-py34.so.1.55.0 /usr/local/lib/libboost_python3.so
```
**注意**: 你安装完 `libboost1.55-all-dev` 之后，会在系统中存在两个版本的 `liboost_python`:

- libboost_python-`py33`.so.1.55.0
- libboost_python-`py34`.so.1.55.0

这里必须选择 `libboost_python-py34.so.1.55.0` 或 `py34` 以上的版本。如果你选择了 `py33` 版本。那么你将在得不到任何错误提示的情况下 `python kernel` 直接崩溃。就跟 `segmentation fault` 一样的令你抓狂。我折腾了一整天才发现是 `py33` 的问题。。。。如下图所示：

![img](/img/use_caffe_with_python3.5/1.jpg)

![img](/img/use_caffe_with_python3.5/2.jpg)

当你在 `python` 接口中运行任何带有输出功能的代码时，就会得到上面的错误，例如 `solver.net.forward()`。
而运行 `solver.step(1)` 这种无输出功能的代码没事。

4）编辑 `Makefile.conf` 文件，加入对 `Anaconda3` 的支持  

目前官方 `caffe` 是不支持 `Anaconda3` 的，必须自己改。所以我发了个 [`pull request #3869`](https://github.com/BVLC/caffe/pull/3869)，增加了对 `Anaconda3` 的支持。具体改动如下：

```
在 `Makefile.conf` 增加下面的代码, 详细请看 #3869
PYTHON_INCLUDE := $(ANACONDA_HOME)/include \
                       $(ANACONDA_HOME)/include/python3.5m \
                       $(ANACONDA_HOME)/lib/python3.5/site-packages/numpy/core/include
```

5) 编译 `caffe`

```
make all -j 8
make pycaffe -j 8
```

## 四、运行
在终端下运行 `jupyter notebook`。下面就 enjoy 吧。


