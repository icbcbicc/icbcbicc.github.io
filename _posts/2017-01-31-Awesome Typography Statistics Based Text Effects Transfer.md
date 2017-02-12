---
layout: post
title: "Awesome Typography Statistics-Based Text Effects Transfer"
subtitle:  "论文笔记-2016-arXiv"
author:    "icbcbicc"
header-img: "img/gray.png"
---
header-img: "img/post-bg-11.jpg"
# Awesome Typography Statistics-Based Text Effects Transfer

**Authors**: Shuai Yang, Jiaying Liu, Zhouhui Lian and Zongming Guo

## 1. Introduction

**风格转化** 分别与 **颜色转化** 和 **纹理转化** 有关。

1. 颜色转化是对目标图像和原图像的颜色分布进行 **局部** 或者 **全局** 的匹配。

2. 纹理转化主要分为 **非参数** 的和 **含参** 的。

字体风格转化面临的 **主要困难** 有3点：

1. 风格的种类繁多、文字的形状各不相同。

2. 风格元素很复杂，很多效果是由多种风格复合而成。

3. 原图像（普通无特效的文字）非常简单，能提供的关于如何放置特效的信息太少，因此现在比较流行的基于深度学习的风格转换方法失去了优势。同时由于原图像只是黑底白字，其纹理在各个区域不稳定，所以传统的非参纹理转换不适用（假设了原图像各个区域的纹理是稳定的）。

此文提出的算法是 **基于距离** 的。

## 2. Related Work

- 颜色转换

- 非参纹理转化

- 含参纹理转化

## 3. Proposed Method

### 3.1 Problem Formulation and Analysis Text

有一个基本的观察结论：局部块的特效与其所处的位置有很大关联。位置相近的块颜色也大多相似。

![](/img/48.jpg)

先用5种切分方法将风格图像分为 $N=16$ 个partition：

![partition mode](/img/47.jpg)

1. 随机分配像素到16个partition中

2. 按4*4的网格分

3. 按极坐标的极角分

4. 按圆环分

5. 根据到文字骨架的距离分

距离与2个因素相关：patch的 **颜色和大小**

#### 颜色：

**用可靠性来衡量这5种切分方式：**  对于某种切分方式，给出其中一个patch以及其所在的partition，使用SVM判断这个patch的颜色。

结果显示 **按距离切分** 的效果最好。

#### 大小：

- 对于按某种切分模式划分的一个分区，分别计算其中不同大小的patch的平均偏差。得到下图：

![scale](/img/49.jpg)

综上所述，按距离分割的效果最好。见下图：

![reliability](/img/50.jpg)

### 3.2. Text Effects Statistics Estimation

这一步的作用是将前一步的分析用于实际的风格转换指导。

#### 3.2.1 Optimal Patch Scale Detection

在需要确定渲染patch大小的点周围寻找复合筛选条件的点，通过筛选就进入下一集筛选，大小越小。否则用当前的大小作为最佳大小。

#### 3.2.2 Robust Normalized Distance Estimation

提取文字的骨架，计算周围点到骨架的距离，并且归一化。

#### 3.2.3 Optimal Scale Posterior Probability Estimation

根据每个块的最佳尺度$l$及其中心点到文字骨架的距离$d$，计算出条件概率 $P(l \| d)$ ，它描述了到文字骨架距离为$d$ 的块以$l$为最佳尺度的概率。

### 3.3. Text Effect Transfer

目标函数：

$$min \sum_p E_{app} + \lambda_1 E_{dist}(p, q) + \lambda_2 E_{psy}$$

- 外观$E_{app}$

$$E_{app} = \lambda_3 \|P(p)-Q(q)\|^2 + \|P^* (p)-Q^* (q) \|^2 $$

  $P$为原始文字，$Q$为目标文字，$P^* $为原始效果，$Q^* $为目标效果。

  加入前文所述的 $P(l \| d)$，多种纹理共同合成:

$$E_{app} = \lambda_3 \sum_l P(l | d)\|P(p)-Q(q)\|^2 + \sum_l P(l | d)\|P^* (p)-Q^* (q) \|^2 $$

- 分布$E_{dist}$:

$$E_{dist}(p, q) = \frac{(dist(p)-dist(q))^2}{max(1,dist^2(p))}$$

- 视觉心理$E_{psy}$

  避免同一纹理重复多次，不美观。

#### 3.3.5 Function Optimization
