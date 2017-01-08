---
layout: post
title: "Precomputed real-time texture synthesis with markovian generative adversarial networks"
subtitle:  "论文笔记-2016-arxiv"
author:    "icbcbicc"
header-img: "img/post-bg-08.jpg"
tags: ["deep learning"]
---

## Precomputed real-time texture synthesis with markovian generative adversarial networks

**Authors**: Chuan Li, Michael Wand

### 1. Introduction

最传统的纹理合成方法：用马尔科夫随机场（MRF）来测量图像局部patch的统计特征。

现在常采用**深度模型**，大致有2类：

- **Full image models**

	Employ specially trained auto-encoders as generative networks.

	Limited to small images(typically around 64×64 pixels) with limited fidelity in details.

- **Markovian models**

	此文的方法属于此类。只获取局部patch的统计信息，并用他们合成高分辨率图像。保真度较好，但需要额外的guidance。与传统方法相比虽然都使用MRF，但效果要好得多（速度也慢很多，此文的目的就是提升效率）。

**核心思想:**  

1. 使用strided conv（将poolig替换为subsampled conv filter）提前算出网络的inversion，这是一个纯粹的feed-forward过程

2. 使用对抗网络训练

### 2. Related Work

之前用神经网络模型做风格转换的主要是基于deconvoutional，也就是卷积的反过程。这个方法常用于神经网络中间结果的可视化（将中间层的特征还原成与输入图片类似的图像）

#### 2.1 高斯模型

*<u>Gatys, L.A., Ecker, A.S., Bethge, M.: Texture synthesis and the controlled generation of natural stimuli using convolutional neural networks</u>* pioneer this approach by modeling patch statistics with a global Gaussian models of the higher-level feature vectors.

这个基于统计的模型有一些问题: 

Enforcing per-feature-vector statistics permits a mixing of feature patterns that never appear in actual images and limit plausibility of the learned texture.
 

**加速改进版**（employ precomputed decoders trained with a perceptual texture loss）：

- *<u>Ulyanov, D., Lebedev, V., Vedaldi, A., Lempitsky, V.: Texture networks: Feed- forward synthesis of textures and stylized images.</u>*

- *<u>Johnson, J., Alahi, A., Li, F.F.: Perceptual losses for real-time style transfer and super-resolution</u>*

#### 2.1 马尔科夫模型

*<u>Li, C., Wand, M.: Combining markov random fields and convolutional neural networks for image synthesis</u>* utilize dictionaries of extended local patches of neural activation. 

In this work, the previous problem can be partially addressed by replacing point-wise feature statistics by **statistics of spatial patches** of feature activations. This permits photo-realistic synthesis in some cases, but also reduces invariance because the simplistic dictionary of patches introduces rigidity.

Adversarial training can recognize such manifold with its discriminative network, and strengthen its generative power with a projection on the manifold.

**加速改进版**：此文

### 3. Model

We improve adversarial training with contextually corresponding Markovian patches. This allows the learning to focus on the mapping between different depictions of the same context, rather than the mixture of context and depictions.

#### 3.1 Discriminative network D

Replace patch dictionary (including the iterative nearest-neighbor search) with a continuous discriminative network D (green blocks) that learns to distinguish between actual feature patches (on VGG 19 layer Relu3 1, purple block) sampled from the example image and neural patches sampled from the synthesized image. 

对每一个patch输出：classification score s = ±1, 体现了neural patch 与example patch 的相似度。

优化目标：
$$ x = arg \min E_t(\Phi(x),\Phi(x_t))+\alpha_1E_c(\Phi(x),\Phi(x_c))+\alpha_2\gamma(x) $$

Texture loss: $ E_t(\phi(x),\phi(x_t)) = \frac{1}{N} \max(0, 1−1 \times s_i)$

$\phi(x)$ : $x$'s feature map output from relu3_1

$\gamma(x)$ : Smoothness regularizer

优化方法：The deconvolution process back-propagates this loss to pixels with ADAM. The parameters of thr discriminative network are updated after each deconvolution.

使用$E_t$和$\gamma(x)$可以生成随机的纹理，加上content loss $E_c$ 就可以合成与guidance image $x_c$ 相关的图像。

Content loss: $E_c(x)$ (Mean Squared Error between $\phi(x)$ and $\phi(x_c)$)

If we run deconvolution on the VGG networks (from the discriminator and optionally from the guidance content), we obtain deconvolutional image synthesizer, which we call Markovian Deconvolutional Adversarial Networks (MDANs).

#### 3.2 Generative network G

因为MDANs生成合成的图片很慢，所以训练一个variational auto-encoder(VAE）来将feature map直接解码为pixels.

加入generative network G（blue blocks），它以relu4_1为输入，通过普通的卷积和一个 cascade of fractional-strided convolutions (FS Conv) 将图片解码。

We denote the overall architecture by Markovian Generative Adversarial Networks (MGANs).

#### 3.3 MDANs与MGANs的区别:

- MDANs比MGANs保留了更多的内容信息，MGANs则更加风格化。这是因为MGANs是由很多的图片训练成的，因此学习到了最常出现的特征。

- MDNAs可以产生更加自然的背景。

- MGNAs的速度是MDNAs的25000倍。

### 4. Experimental Analysis

Run unguided texture synthesis with discriminator D


- **Input layes in D: layer relu2_1, relu3_1, and relu4_1**

	Lower layers (relu2_1): produce sharper appearances but at the cost of losing form and structure of the texture. 

	Higher layer (relu4_1): preserves coarse structure better (such as regularity) but at the risk of being too rigid for guided scenarios. 

	Middle layer (relu3_1): offers a good balance between quality and flexibility.


- **Patche size in D: 4,8,16**

	Smaller patches: increase the flexibility.

	Larger patches: preserve better structure.


- **Conv layers in D**

	减少卷积层: 不平滑

	增加卷积层：没有明显的提升

- **Channels in D**
 
	减少通道数量会导致更差的结果（比如4和64）

	但当通道数足够多时，在增加没有明显变化（比如64和128）

- **G中的参数**

	删除第一个卷积层或者减少所有层的通道数量会导致更差的结果

	但是增加网络的复杂度只带来了很有限的提升

	解释：This is likely because the networks are all driven by the same discriminative network, there are some non-trivial information from the deconvolutional process that can not be recovered by a feed forward process.

### 5. Results(与Global statistics的对比)

与Global statistics（比如*Gatys, L.A., Ecker, A.S., Bethge, M.: A neural algorithm of artistic style 2015* 中的高斯模型）相比：

1. **纹理的流畅性**：本文的方法能够更加流畅的转换纹理，比如头发的纹理的转换就需要流畅性。而高斯模型则没有这种连贯性。

	（作者前一篇文章中所用的local patch based approach有比本文更好的连贯性，因为它使用了non-parametric sampling。然而这种方法因为需要匹配不同的块而非常慢。）

2. **色彩分布**：Global statistics产生的色彩分布与style image更加接近。

3. **背景**：Global statistics产生的背景也比本文好。

4. **应对复杂内容**: Global statistics在转换复杂的图像时能力比较差，在复杂区域会产生过多或者过少的纹理。主要是因为高斯模型的限制。

5. **视频**：显然此文的速度更快，因此也适用与视频的实时风格转化。同时，本文的方法在时间上的连贯性也比deconvolutional methods好。

### 6. Limitation

1. 在没有纹理的区域本文的效果不好，比如前面说的背景。在转化2张人脸图片的面部特征时，效果也很差。主要是因为面部特征并不能当做纹理来处理（why？），而需要semantic understanding。一种解决方法是将the learning of object class *[Radford, A., Metz, L., Chintala, S.: Unsupervised representation learning with deep convolutional generative adversarial networks.]* 与本文结合。

2. 在生成照片而不是绘画的纹理时，作者的前一篇文章效果更好，因为non-parametric sampling prohibits data distortion。但是那篇文章的模型的适用范围也很窄。

3. 本文的模型比较偏向于content image，而Global statistics模型则偏向于texture image。

### 7. Conclusion

**未来的方向**：Study the broader framework in a big-data scenario to learn not only Markovian models but also include coarse-scale structure models.

**最终目标**: A directly decoding, generative image model of large classes of real-world images.