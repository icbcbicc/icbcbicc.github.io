---
layout: post
title: "对抗网络"
subtitle:  "读书笔记"
author:    "icbcbicc"
header-img: "img/post-bg-07.jpg"
tags: ["deep learning"]
---

## 对抗网络

本文给出了生成式对抗网络的基本介绍。

机器学习的方法可大致分为2类

- 生成方法(generative approach)

- 判别方法(discriminative approach)

最近流行的生成式模型：**生成对抗网络(GANs)，变分自编码器(VAE)，自回归模型**

### 1. 生成对抗网络(GAN：Generative Adversarial Networks)

#### 1.1 基本模型

GAN启发自博弈论中的二人零和博弈，由 *<u>Generative Adversarial Networks，by Ian Goodfellow, et al</u>*  开创性地提出，包含一个生成模型（generative model G）和一个判别模型(discriminative model D)。

生成模型捕捉样本数据的分布，判别模型是一个二分类器。生成器G需要让D把自己产生的伪造图片全部判断成真实的图片，判别器 D 需要判断输入数据是真实的图片还是伪造的图片。

**优化过程**: 训练时固定一方，更新另一个模型的参数，交替迭代，使得对方的错误最大化。最终，G能估测出样本数据的分布。

**损失函数** : $$\text {Loss} = \frac{1}{m}\sum_{i=1}^m[\log D(x^i) + log(1-D(G(z^i)))]$$

其中$x^i, z^i$分别是真实的图片数据以及噪声数据。

**优化目标**：$$\min_G\max_D \text{Loss}$$

通过G最小化Loss，D最大化Loss。更新D时用梯度上升，更新G时用梯度下降，也就是G需要获取D输入处的负梯度。  

训练首先要保证判别器足够好然后才能开始训练生成器，否则对应的生成器也没有什么作用。


#### 1.2 针对难以收敛的改进

但是实践时发现这个网络**很难收敛**，于是 *<u>Unsupervised representation learning with deep convolutional generative adversarial networks，by Radford，et al</u>* 提到几个非常关键的模型训练要点：

- 把判别器D中的所有Pooling层更换成strided convolutions，把生成器G中的所有Pooling层换成fractional-strided convolutions  
- 同时在判别器和生成器中使用Batch Normalization  
- 不要使用全连接层  
- 生成器中除了输出那层用tanh作为激活函数，其他都用ReLU  
- 判别器中所有激活函数都用Leaky ReLU

<br>

#### 1.3 带条件约束的GAN (CGAN)

对于较大的图片，基于简单GAN的方式就不太可控了。为了解决GAN太过自由这个问题，一个很自然的想法是给GAN加一些约束，于是便有了**带条件约束的GAN(CGAN)** *<u>Conditional Generative Adversarial Nets，by Mirza M，et al</u>*。

CGNN在生成模型(D)和判别模型(G)的建模中均引入条件变量y(conditional variable)，使用额外信息y对模型增加条件，可以指导数据生成过程。这些条件变量y可以基于多种信息。如果条件变量y是类别标签，可以看做 CGAN是把纯无监督的GAN变成有监督的模型的一种改进。这个简单直接的改进被证明非常有效,并广泛用于后续的相关工作中。

受此启发，*<u>Deep Generative Image Models using a Laplacian Pyramid of Adversarial Networks， by Emily Denton, et al</u>* 通过为拉普拉斯金字塔中的每一个尺度建立一个生成模型，使得最终生成的图片与自然图片达到肉眼无法区分的地步，代码参见项目eyescream 。

进一步，*<u>Conditional generative adversarial nets for convolutional face generation，by Jon Gauthier</u>* 提出了条件生成对抗网络, 文中给出了一个将人脸老化并加上笑容的例子。

### 2. 变分自编码器(VAE: Variational Autoencoders)

相对于传统 AutoEncoder，其优点是改变了 AutoEncoder 中很容易过拟合的 reconstruction error based learning 的方法，将学习的目标变成去尽可能满足某个预设的先验分布的性质。

### 3. 自回归模型(Autoregressive models)
