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

常见的3种sketch：

1. line sketch

2. grayscale sketch

3. color sketch

使用【3】所述的算法进行color和grayscale的sketch风格化。
