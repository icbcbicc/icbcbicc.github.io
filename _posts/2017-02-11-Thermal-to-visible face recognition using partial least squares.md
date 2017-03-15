---
layout: post
title: "Thermal-to-visible face recognition using partial least squares"
subtitle:  "论文笔记-2015-JOSA"
author:    "icbcbicc"
header-img: "img/gray.png"
---
header-img: "img/post-bg-15.jpg"

# Thermal-to-visible face recognition using partial least squares

**Authors**: Hu Shuowen, Choi Jonghyun

- 预处理：

  1. Median filtering去除dead pixels
  2. Geometric normalization，根据人工标注的4个特征点对齐脸部并裁剪。
  3. DoG filtering
  4. Contrast enhancement

  ![preprocess](/img/55.jpg)

- 特征提取：

    HOG

- Partial least squares(PLS)-based Recognition

  此分类模型是属于 **one-vs-all** 的，也就是对于每一个人，将其所有光学图像作为正样本。将其他人的光学图像作为负样本。还有一部分不属于上述范围的人的红外图像作为负样本的扩充（cross examples）。最终对每一个人都产生一个模型。

  测试时将待匹配的红外图像提取的特征用每一个模型打分，取分高者为匹配对象。

  ![1-vs-all](/img/54.jpg)
