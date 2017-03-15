---
layout: post
title: "Understanding deep image representations by inverting them"
subtitle:  "论文笔记-2015-CVPR"
author:    "icbcbicc"
header-img: "img/post-bg-12.jpg"
tags: ["deep learning"]
---

## Understanding deep image representations by inverting them

**Authors**: Mahendran Aravindh，Vedaldi Andrea

**简介：**

- CNN是一个编码器，将输入图片$x$编码为一系列特征$\phi(x)=\phi_0$。

- 本文要做的就是给出一个CNN输出的特征$\phi_0$，反向得到网络输入$x$。

**优化目标：**

$$x^* = argmin_x \quad l(\phi(x),\phi_0)+\lambda R(x)$$

- 其中loss函数$l$衡量了$\phi(x)$和CNN输出的特征$\phi_0$之间的差距，我们的目标是找到一个$x$使$\phi(x)=\phi_0$。文中$l$使用欧氏距离。

- 其中$R(x)$是一个约束项，可以使生成的图像更接近自然图像。也就是它将优化问题的解空间范围缩小了。比如$R(x)={\|\|x\|\|}_{\alpha}^{\alpha} $，当$\alpha$比较大（比如6）时，能将$x$限制在一个区间。

**初始化x：**

- 注意一点，在优化过程中使用不同的起始值可能导致不同的最优点，也就是生成的图像是不唯一的。

- 此文使用随机噪声进行初始化。在一些风格转化的文章中，用到了此文的reconstruction方法，一般用内容图片进行初始化，这样可以收敛的更快。

**优化：使用带momentum的梯度下降算法**

$$\mu_{t+1}  :=  m \mu_t - \eta_t \bigtriangledown E(x) \quad x_{t+1} := x_t+\mu_t$$

$m=0.9$: decaying factor

$\eta$: learning rate，前期固定，后期逐步减小

- 求导

loss是$\|\|\phi(x)-\phi_0\|\| _ 2 ^ 2$，因为欧拉函数很好求导，内部的CNN也可以用反向传播求导，因此对loss整体求导不难。

**实验展示**

用CNN不同层得到的$\phi_0$作为输入：

![实验展示](/img/34.JPG)
