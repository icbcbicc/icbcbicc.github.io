---
layout: post
title: "Deep Perceptual Mapping for Thermal to Visible Face Recognition"
subtitle:  "论文笔记-2015-arXiv（BMVC ）"
author:    "icbcbicc"
header-img: "img/gray.png"
---
header-img: "img/post-bg-12.jpg"

# Deep Perceptual Mapping for Thermal to Visible Face Recognition

**Authors**: Sarfraz M. Saquib， Stiefelhagen Rainer

## 1. Introduction

- 反射（这种人脸与可见光的多模型人脸识别已经比较成熟）

  - NIR

  - SWIR

- 自发

  - MWIR

  - LWIR

  ![IR](/img/53.jpg)

## 2. Related Work ( *important!* )

1, 3, 7, 8, 10, 12, 18, 21

facial feature detection in thermal images: 15

## 3. Deep Perceptual Mapping (DPM)

使用多层的全连接网络来拟合从热红外转到可见光的函数。

### 3.1 The DPM Model

$N+1$层的网络，第$k$层有$m^{(k)}$个单元。

两层之间：
$$h^{(k)} = g(W^{(k)} h^{(k−1)} + b^{(k)} )$$

倒数第二层：
$$H(x)=h^{(N)} =g(W^{(N)} h^{(N−1)} + b^{(N)} )$$

最后一层：
$$\bar{x} =s(W^{(N+1)} H(x))$$

目标函数：

$$ arg min_{W, b} \quad J = \frac{1}{M} \sum_{i=1}{M} (\bar{x_i} - t_i)^2 + \frac{\lambda}{N} \sum_{k=1}^N (\|W^{(k)}\|^2_F + \| b^{(k)}\|^2_2)$$

第一项是平方误差，第二项是约束项。其中，$t_i$是训练数据中的红外图像。$x_i$是神经网络输出。

激活函数$g(x)$使用tanh，实际效果比ReLU和sigmoid好。

### 3.2 Thermal-Visible Face Matching

将经神经网络转化后的可见光图像的像素排成一个向量，同样将图库中拍摄的每张红外图像排列成一个向量。并将这些向量进行$L2$规范化。

然后将转化的向量与拍摄的向量进行逐一对比。

Cosine similarity：

$$d(\bar{x_i}, t_i) = \bar{x_i} \cdot t_i$$

同时进行比对：

$$d(\bar{x_i}, T) = \bar{x_i} \cdot T$$

## 4. Experimental Results

Dataset：UND Collection X1

![dataset](/img/51.jpg)

- visible images：1600×1200

- LWIR thermal image： 312×239

此数据库包含82个人的4584张照片，包含了不同的表情和光照环境。热红外与可见光一一对应。

  文中按照以往使用此数据库的做法，将一半的人（A）用于训练，另一半（B）测试。在测试时使用所有人（A+B）的红外照片。

  **所有的图片通过给出的眼睛和嘴巴坐标对齐。为裁剪110×150大小。**

### Implementation Details

1. 用median filtering去除红外照片中的dead pixels

2. zero-mean normalization

3. 用Difference of Gaussian（DoG） filtering强化线条，消除光照的影响。

4. 对图像有重叠的块提取SIFT或者HOG特征，得到一个向量。

5. PCA降维到64维。

6. 在降维后的特征向量加入该块的位置信息$(x, y)$。因此输入为66维。

## 4.2 Results

Thermal-Visible：55.36%

![result1](/img/52.jpg)

Thermal-Thermal：89.7%
