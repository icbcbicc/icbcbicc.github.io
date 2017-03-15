---
layout: post
title: "Convolutional Sketch Inversion"
subtitle:  "论文笔记-2016-ECCV"
author: "icbcbicc"
header-img: "img/gray.png"
---

# Convolutional Sketch Inversion

**Authors**: B Yannis Kalantidis, Mellina Clayton, Osindero Simon

## 2. Related Work

本文所述的方法适用于在 **多种光照环境和拍摄角度下** ，将sketch转化成真实彩色图片。

相关sketch inversion的文章及其所使用的方法：

- 较简单的情况

  - 【7】：Sparse representations

  - 【20】：Bayesian tensor inference

  - 【30】：Transductive learning with a probabilistic graph model

  - 【31】：Multiscale Markov random field model

  - 【33】：Embedded hidden Markov model

- 较复杂的情况

  - 【18】：Lighting variation

  - 【36】：Lighting and pose variations

  - 【35】：

还有一些文章使用DNN处理sketch，比如简化sketch（【24】）。或者将照片转化为sketch（【34】）。

也可以用DNN给黑白照片上色。

## 3. Semi-simulated Datasets

先将彩色照片转化为sketch，拓展数据库。

### 3.1 Preprocessing

裁剪、更改大小、对齐。

### 3.2 Sketching

常见的3种sketch（下图从左到右）：

1. line sketch

2. grayscale sketch

3. color sketch

![LFW dataset](/img/57.jpg)

使用【8】所述的算法进行color和grayscale的sketch风格化。

使用【2】所述的算法进行line的sketch风格化。

从color和grayscale的sketch复原真实图片比从line sketch复原难，主要是因为line sketch保留了更多信息。

line sketch生成的图片保留了更多的信息，生成的效果普遍好于另外2种。特别是对头发颜色和细节的生成。

## 4. Models

对这3种sketch，利用【15】分别建立一个DNN（当使用color sketch时，输入为3通道）。DNN结构如下：

![DNN](/img/56.jpg)

### 4.1 Estimation

（TODO）Loss function and optimization algorithm

Loss function由pixel loss，feature loss和total variation loss组成。

【15】提出使用feature loss替代pixel loss，可以得到更美观的效果。本文发现同时利用2者效果更好。对比见4.2。

### 4.2 Validation

度量模型效果【32】：

- Peak signal to noise ratio ($PSNR$)：physical quality

- Structural similarity ($SSIM$)：perceptual quality

- Standard Pearson product-moment correlation coefficient $R$

![assessment](/img/58.jpg)

可以看出line效果最好，grayscale效果最差。

当只使用loss function中的feature loss，不用其他项，效果如下：

![feature loss only](/img/60.jpg)

最后，不再使用机器提取的sketch，而是使用手工绘制的line sketch进行生成测试。

![CUFS database](/img/59.jpg)
