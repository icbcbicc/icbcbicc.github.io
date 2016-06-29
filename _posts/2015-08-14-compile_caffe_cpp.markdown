---
layout:     post
title:      "在 Xcode 中编译和调试 Caffe 的 C++ 程序 (非 Caffe 源代码)"
subtitle:   "Compile and Debug Caffe C++ application in Xcode"
date:       2015-08-14 23:23:00
author:     "Coldmooon"
header-img: "img/home-bg.jpg"
---

## 一、前言
本文主要介绍了如何在 `IDE` 中编译和调试 `caffe` 的 `c++` 程序（ `cpp` 文件）。注意是调试应用程序，而非 `caffe` 代码本身。下一篇文章才讲如何调试 `caffe` 源代码。编译 `caffe` 的 `c++` 程序至少有三种办法:

1. 把写好的 `cpp` 文件放到 `caffe` 安装目录下的 `examples` 目录中。然后在终端下，进入 `caffe` 的安装目录。键入 `make all`， `caffe` 会自动识别新的 `example` 并编译。这种方法的优势是简单易行、行之有效。

2. 自己写一个 `makefile` 文件，放到 `cpp` 文件所在目录，再用 `make` 编译。这种方法与 1 相比，要自由许多。可以根据自己的代码，选择性的链接一些 `library`，编译出来的文件体积比 1 小。具体的 `makefile` 实例可以参见这个[链接](https://github.com/tzutalin/caffe_test)

3. 用 `IDE` 写代码、编译和调试。这种方法与 1、2相比，要做许多配置，但是可以使用单步调试。

本文主要介绍方法 3。方法 3 已经在 `OS X 10.11` 下测试成功。`Xcode` 版本是 7.0.1; `Caffe` 是 2015.10.15 的版本。对于 `Ubuntu 14.04` 下的 `eclipse`，完全可以采用相同的配置思路，只要所需的依赖库添加全了，都能编译通过。

## 二、使用 IDE 编译和调试 caffe 的 c++ 程序

[TzuTaLin](http://tzutalin.blogspot.tw/2015/05/caffe-on-ubuntu-eclipse-cc.html)在他的博客中介绍了在 `ubuntu` 下配置 `Eclipse` 来编译 `caffe c++` 代码的方法。并且在 `github` 上给出了一个实例--[链接](https://github.com/tzutalin/caffe_test)。

我在 `MAC OSX 10.10.5, Xcode 6.4` 上测试了一下[TzuTaLin](http://tzutalin.blogspot.tw/2015/05/caffe-on-ubuntu-eclipse-cc.html)的方法。发现，可以顺利编译 `TestCaffe.cpp` 这个文件。但在编译 `caffe` 自带的 example -- `classification.cpp` 时会报错:

```
Undefined symbols for architecture x86_64:
  "google::LogMessage::stream()", referenced from:
      Classifier::Classifier(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) in main.o
      Classifier::SetMean(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) in main.o
      Classifier::Preprocess(cv::Mat const&, std::__1::vector<cv::Mat, std::__1::allocator<cv::Mat> >*) in main.o
      _main in main.o
      caffe::ReadProtoFromBinaryFileOrDie(char const*, google::protobuf::Message*) in main.o
      caffe::Blob<float>::LegacyShape(int) const in main.o
      caffe::Blob<float>::CanonicalAxisIndex(int) const in main.o
      ...
  "google::LogMessageFatal::LogMessageFatal(char const*, int)", referenced from:
      Classifier::Classifier(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) in main.o
      Classifier::Preprocess(cv::Mat const&, std::__1::vector<cv::Mat, std::__1::allocator<cv::Mat> >*) in main.o
      _main in main.o
      caffe::ReadProtoFromBinaryFileOrDie(char const*, google::protobuf::Message*) in main.o
  "google::LogMessageFatal::LogMessageFatal(char const*, int, google::CheckOpString const&)", referenced from:
      Classifier::Classifier(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) in main.o
      Classifier::SetMean(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) in main.o
      caffe::Blob<float>::LegacyShape(int) const in main.o
      caffe::Blob<float>::CanonicalAxisIndex(int) const in main.o
  "google::LogMessageFatal::~LogMessageFatal()", referenced from:
      Classifier::Classifier(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) in main.o
      Classifier::SetMean(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) in main.o
      Classifier::Preprocess(cv::Mat const&, std::__1::vector<cv::Mat, std::__1::allocator<cv::Mat> >*) in main.o
      _main in main.o
      caffe::ReadProtoFromBinaryFileOrDie(char const*, google::protobuf::Message*) in main.o
      caffe::Blob<float>::LegacyShape(int) const in main.o
      caffe::Blob<float>::CanonicalAxisIndex(int) const in main.o
      ...
  "google::InitGoogleLogging(char const*)", referenced from:
      _main in main.o
  "google::base::CheckOpMessageBuilder::ForVar2()", referenced from:
      std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >* google::MakeCheckOpString<unsigned long, int>(unsigned long const&, int const&, char const*) in main.o
      std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >* google::MakeCheckOpString<int, int>(int const&, int const&, char const*) in main.o
  "google::base::CheckOpMessageBuilder::NewString()", referenced from:
      std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >* google::MakeCheckOpString<unsigned long, int>(unsigned long const&, int const&, char const*) in main.o
      std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >* google::MakeCheckOpString<int, int>(int const&, int const&, char const*) in main.o
  "google::base::CheckOpMessageBuilder::CheckOpMessageBuilder(char const*)", referenced from:
      std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >* google::MakeCheckOpString<unsigned long, int>(unsigned long const&, int const&, char const*) in main.o
      std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >* google::MakeCheckOpString<int, int>(int const&, int const&, char const*) in main.o
  "google::base::CheckOpMessageBuilder::~CheckOpMessageBuilder()", referenced from:
      std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >* google::MakeCheckOpString<unsigned long, int>(unsigned long const&, int const&, char const*) in main.o
      std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >* google::MakeCheckOpString<int, int>(int const&, int const&, char const*) in main.o
ld: symbol(s) not found for architecture x86_64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```    
这个错误是由于没有找到 `glog` 的库导致的。直接把 `glog` 的库加入到 `Xcode/Eclipse` 里即可解决。这个错误是解决了，不过除了 `glog` 之外，是不是需要加别的东西，怎么知道需要加什么？下面做个简单的分析。这对自己写 `Makefile` 是有帮助的。


### 分析

如果我把 `classification.cpp` 这个文件放到 `caffe/examples` 这个目录下，并用 `caffe`自带的 `Makefile` 编译的话，是可以顺利编译的。这说明我安装的各个依赖库是好的，出现错误只是因为 `IDE`的配置还不够完整。所以，我在[TzuTaLin](http://tzutalin.blogspot.tw/2015/05/caffe-on-ubuntu-eclipse-cc.html)的基础上做了进一步尝试。

首先，`caffe` 的安装过程需要 `glog gflags protobuf leveldb snappy` 这些依赖库。那么在其 [`Makefile`](https://github.com/BVLC/caffe/blob/master/Makefile) 中一定会存在这些依赖库的调用。在 `Makefile` 的 `172` 行中发现:

```
LIBRARIES += glog gflags protobuf leveldb snappy \
	lmdb boost_system hdf5_hl hdf5 m \
	opencv_core opencv_highgui opencv_imgproc
```
可见，`caffe` 所需的各个依赖库都存储在 `LIBRARIES` 这个变量中。 `caffe` 编译 examples 的过程必定与 `LIBRARIES` 这个变量息息相关。所以，接下来专门搜索 `LIBRARIES` 这个关键字即可。在 `364` 行就会发现:

```
LDFLAGS += $(foreach librarydir,$(LIBRARY_DIRS),-L$(librarydir)) $(PKG_CONFIG) \
		$(foreach library,$(LIBRARIES),-l$(library))
```
可见，通过 `foreach` 循环，`caffe` 在编译过程中会依次链接 `LIBRARIES` 这个变量中的内容。据此，我可以在 `Xcode/Eclipse` 中把 `LIBRARIES` 指定的各个依赖库的 `lib` 文件都加进去。然后结合[TzuTaLin](http://tzutalin.blogspot.tw/2015/05/caffe-on-ubuntu-eclipse-cc.html)的配置方法，即可顺利编译和调试。

### 完整过程 —— Xcode 配置 
1) 在 `Header_Search_Paths` 中做如下设置:

![img](/img/compile_caffe_cpp/compile_caffe_cpp_01.jpg)

2) 在 `Library_Search_Path` 中做如下设置:

![img](/img/compile_caffe_cpp/compile_caffe_cpp_02.jpg)

3) 配置 `@rpath` 变量。在 `Runpath search Paths` 中做如下设置:
![img](/img/compile_caffe_cpp/compile_caffe_cpp_06.jpg)
这一步的目的是为了避免下述错误:

```
dyld: Library not loaded: @rpath/libcaffe.so
  Referenced from: /Users/Coldmoon/ComputerVisionApps/exercises/caffe_cpp_test/Build/Products/Debug/caffe_cpp_test
  Reason: image not found
```

4) 然后右键项目名称，并选择 `Add Files to ...`，把下图中的库文件都加进去:

![img](/img/compile_caffe_cpp/compile_caffe_cpp_07.jpg)

我对 `LIBRARIES` 变量中包含的所有依赖库分别测试了下。发现只需要添加 `libopencv_core`, `libopencv_highgui`, `libopencv_imgproc`, `libcaffe.so`, `libglog` 这几个就够了，不需要把 `LIBRARIES` 变量中指定的所有依赖库都加进去。

5) 如果在编译安装 `caffe` 的时候，用的是 `libstdc++`， 那么在 `C++ Standard Library` 中选择 `libstdc++`。否则选择 `libc++`，如下图这样设置:

![img](/img/compile_caffe_cpp/compile_caffe_cpp_04.jpg)

6) 如果你的电脑上没有 GPU，那么把 `CPU_ONLY=1` 加到 `Preprocessor` 中。如下图所示:

![img](/img/compile_caffe_cpp/compile_caffe_cpp_05.jpg)

接下来编译和调试即可。
