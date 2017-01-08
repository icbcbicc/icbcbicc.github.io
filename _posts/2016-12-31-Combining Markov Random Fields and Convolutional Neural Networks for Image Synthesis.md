---
layout: post
title: "Combining Markov Random Fields and Convolutional Neural Networks for Image Synthesis"
subtitle:  "论文笔记-2016-CVPR"
author:    "icbcbicc"
header-img: "img/post-bg-13.jpg"
tags: ["deep learning"]
---

## Combining Markov Random Fields and Convolutional Neural Networks for Image Synthesis

**Authors**: Li Chuan，Wand Michael

这篇文章大致思路与[Image-style-transfer-using-convolutional-neural-networks](/2016/12/30/Image-style-transfer-using-convolutional-neural-networks)相同。

区别在于style loss的表示上：

- 前面那篇文章使用Gram矩阵来表示同一层不同特征的关系，通过最小化多层的加权和来优化。

- 本文使用了马尔科夫随机场(MRF)作为风格图片的编码器，通过最小化energy function来进行优化。

还有本文增加了squared gradient norm，也就是一个smooth约束。

**定义style loss:**

$$E_s(\Phi(x),\Phi(x_s)) = \sum\|\Psi_i(\Phi(x))-\Psi_{NN(i)}(\Phi(x_s))\|_ 2^2$$

- $\Phi(x)$表示x的feature map

- $\Psi_i(x)$表示x这个feature map中的第i个neural patch

虽然看上去就是欧氏距离，但关键在于$NN(i)$:

$NN(i)$是用来为每一个patch在style中找到匹配的patch，效果好但也很慢，可以通过添加一层卷积层来实现这个匹配功能。本质上是一个normalized cross-correlation。

$$NN(i) = argmin_{j=1,...,m_s}\frac{\Psi_i(\Phi(x))\Psi_j(\Phi(x))}{|\Psi_i(\Phi(x))| |\Psi_j(\Phi(x))|}$$

下图是一个匹配patch的例子：

![patch匹配](/img/38.JPG)

**与[Image-style-transfer-using-convolutional-neural-networks](/2016/12/30/Image-style-transfer-using-convolutional-neural-networks)的效果对比：**

![效果对比1](/img/39.JPG)

<br>

![效果对比2](/img/40.JPG)

<br>

![效果对比3](/img/41.JPG)


**总结：**

- 本文的方发更多的保留style，Gatys et al的方法更多保留了content。

- 本文中所说的MRF不能找到匹配的patch时，会产生很多不自然的纹理。因此本文对输入的要求更高（相似性高）。

- 本文适合用于允许structure deformation的场合，比如人脸和车（？？），面对有对称性的物体则往往效果不好（比如建筑 ）。
