---
layout:     post
title:      "在 Xcode 中调试和研究 Caffe"
subtitle:   "Debug and Explorer Caffe in Xcode"
date:       2016-03-18 13:15:00
author:     "Coldmooon"
header-img: "img/home-bg.jpg"
---

## 一、前言
本文主要介绍了如何在 `Xcode` 中调试 `caffe` 的源码。这在开发 `caffe` 新功能的时候非常有用。与上一篇[《在 Xcode 中编译和调试 Caffe 的 C++ 程序 (非 Caffe 源代码)》](http://coldmooon.github.io/2015/08/14/compile_caffe_cpp/)不同，上一篇是在 Xcode 中调用 `caffe`，只是对 `caffe` 的应用，而本篇是调试 `caffe` 本身。

## 二、准备工作
1）首先安装好各种依赖库，要确保能够按照官方教程，在 `OSX` 的终端下成功编译 `caffe`。这点做不到的话，在 `Xcode` 里搞 `caffe` 就是天方夜谭。

2）我现在使用的系统版本是 `OS X EI Capitan 10.11.4`; `Xcode` 版本是 7.3 (7D175)。在此之前的版本我也试过，都没问题。后续版本也应该适用。

## 三、正式配置

### 新建项目

先新建一个项目
![img](/img/debug_and_learn_caffe_code/2.jpg)

完成之后，`Xcode` 会显示如下目录结构:
![img](/img/debug_and_learn_caffe_code/3.jpg)

下图是硬盘里的目录结构
![img](/img/debug_and_learn_caffe_code/4.jpg)

### 整理 `caffe` 源码

将 `include` 下的 `caffe` 目录拷贝到项目中的 `CaffeLearning` 目录里，保持跟 `main.cpp` 处于同一级目录。

![img](/img/debug_and_learn_caffe_code/5.jpg)

再将 `src/caffe` 和 `src/gtest` 同样拷贝到 `CaffeLearning` 目录里，保持跟 `main.cpp` 处于同一级目录。这时会提示 `caffe` 目录已经存在，然后选择 `Merge`。

![img](/img/debug_and_learn_caffe_code/6.jpg)

![img](/img/debug_and_learn_caffe_code/7.jpg)

将 `tools/caffe.cpp` 拷贝到 `CaffeLearning` 目录里，保持跟 `main.cpp` 处于同一级目录。

将 `examples/cpp_classification/classification.cpp` 拷贝到 `CaffeLearning` 目录里，保持跟 `main.cpp` 处于同一级目录。

用 `protobuf` 编译 `caffe.proto` 文件，生成 `caffe.pb.cc` 和 `caffe.pb.h` 两个文件。然后将
 `caffe.pb.cc` 和 `caffe.pb.h` 拷贝到 `CaffeLearning/caffe/proto/` 里。

生成 `caffe.pb.cc` 和 `caffe.pb.h` 的方法有两种:

1) 在终端下进入 `caffe` 根目录中，然后输入 `make proto`。

```
$ make proto
PROTOC src/caffe/proto/caffe.proto
```
然后进入 `src/caffe/proto/` 下就会看到 `caffe.pb.cc` 和 `caffe.pb.h` 已经出现了。

2) 直接采用 `protobuf` 编译出 `caffe.pb.cc` 和 `caffe.pb.h`:

```
进入 caffe 根目录
protoc --proto_path=src/caffe/proto --cpp_out=.build_release/src/caffe/proto src/caffe/proto/caffe.proto

其中:
--proto_path: caffe.proto 所在目录
--cpp_out   : 输出目录
```

完成上面的工作后，`caffe` 的源代码都已经在 `Xcode` 目录中了。接下来要删掉几个无用的文件，进入`CaffeLearning` 目录里:

1. 删掉 `CaffeLearning/gtest/gtest_main.cc`
2. 删掉 `CaffeLearning/caffe/test/test_caffe_main.cpp`
这两个文件都带有 `main()` 函数入口，会跟 `caffe.cpp`, `classification.cpp` 产生冲突，导致编译失败。
3. 删掉 `CaffeLearning/caffe/proto/caffe.proto`， 这个文件已经没用了！


现在所有所需的文件都已经准备好了，整体的目录结构如下:

![img](/img/debug_and_learn_caffe_code/8.jpg)

硬盘中的目录结构: 
![img](/img/debug_and_learn_caffe_code/9.jpg)


### 添加源码到 `xcode`

![img](/img/debug_and_learn_caffe_code/10.jpg)

![img](/img/debug_and_learn_caffe_code/11.jpg)

先选上面这三个，classification.cpp 这个是测试阶段的代码，不用的话可以不添加； caffe.cpp 是训练+测试阶段的代码，我先选这个。然后把项目自带的 `main.cpp` 给删了。添加完之后，`Xcode` 应该有如下姿势:

![img](/img/debug_and_learn_caffe_code/12.jpg)
 
### 项目设置

进入 `Build Settings`，找到 `Other Linker Flags` 选项，键入下面的配置:

```
-lopencv_core -lopencv_highgui -lopencv_imgproc -lglog -lhdf5 -lgflags -lprotobuf -lboost_system -lopenblas -lhdf5_hl -lleveldb -llmdb -lboost_filesystem -lm -lsnappy -lboost_thread-mt
```

![img](/img/debug_and_learn_caffe_code/13.jpg)

然后找到 `Header Search Paths`, 键入:

```
/Users/Coldmoon/Developer/CaffeLearning/CaffeLearning /usr/local/include
```
下面的 `Library Search Paths` 键入:

```
/usr/local/lib
```

![img](/img/debug_and_learn_caffe_code/14.jpg)

注意: 上面的两个 `Search Paths` 具体填入什么取决于你怎么安装的依赖库。我用 `Homebrew` 装的依赖库，所有的依赖库都会软连接到 `/usr/local/lib` 和 `/usr/local/include` 里面。

还有，注意到 `caffe` 源代码里都是直接 `#include "caffe/..."`，所以在 `Header Search Paths` 里要把路径填到

```
/Users/Coldmoon/Developer/CaffeLearning/CaffeLearning
```
而不是

```
/Users/Coldmoon/Developer/CaffeLearning/CaffeLearning/caffe
```

接下来，继续找到 `Other C++ Flags`，在里面键入:

```
-DGTEST_USE_OWN_TR1_TUPLE=1
```
![img](/img/debug_and_learn_caffe_code/15.jpg)

最后，在 `Preprocessor Micros` 里分别填入:

```
DEBUG=1 CPU_ONLY USE_OPENCV USE_LEVELDB USE_LMDB
```
![img](/img/debug_and_learn_caffe_code/16.jpg)

## 开始调试 Caffe

1）训练阶段，就用 Cifar-10 作为例子吧
进入官方的 cifar10 例子, [链接](http://caffe.berkeleyvision.org/gathered/examples/cifar10.html)。 然后按照操作一步步来。运行下面两个脚本生成数据库:

```
./data/cifar10/get_cifar10.sh
./examples/cifar10/create_cifar10.sh
```

为了清晰起见，在 `CaffeLearning` 新建一个目录 `data`。将 `cifar10_quick_solver.prototxt`, `cifar10_quick_train_test.prototxt`, `cifar10_train_lmdb`, `cifar10_test_lmdb` 都拷贝到 `data` 目录下，如下图:

![img](/img/debug_and_learn_caffe_code/17.jpg)

修改 `cifar10_quick_solver.prototxt`, `cifar10_quick_train_test.prototxt` 里的各个字段，使其对应到正确的路径。

然后在 `Product` 菜单里的 `Scheme` 里选择 `Edit Scheme`，在 `Arguments Passed On Launch` 里填入:

```
train --solver /Users/Coldmoon/Developer/CaffeLearning/CaffeLearning/data/cifar10_quick_solver.prototxt
```

接下来在 `relu.cpp` 里设个断点，开始编译运行，就会发现，程序能够停在断点上了:

![img](/img/debug_and_learn_caffe_code/18.jpg)


