---
layout: post
title: "State-of-the-art in Visual Attention Modeling"
subtitle:  "论文笔记-2013-TPAMI-701"
author:    "icbcbicc"
header-img: "img/post-bg-23.jpg"
tags: ["saliency"]
---

## State-of-the-art in Visual Attention Modeling


Authors: Borji, Ali, Itti, Laurent


[PDF](http://ieeexplore.ieee.org/document/6180177/?arnumber=6180177&tag=1)

### 1. INTRODUCTION

#### 1.1 Definition

#### 1.2 Origins

#### 1.3 Empirical Foundations

#### 1.4 Applications

### 2. CATEGORIZATION FACTORS

#### 2.1 Bottom-up（f1） vs. Top-down Models（f2）

模型一般分为这2类，一种流行的说法是这两者共同作用引导了我们的注意力。

- bottom-up一般是基于场景的特征，也就是（stimulus-driven）。

  能吸引注意力的特征一般都是能够和周围环境明显区分开的。这种模型速度快，一般是feed-forward的，不需feedback。然而这种模型只能解释一部分眼动的现象，因为很多的眼动都是task-driven的。

- top-down一般是基于认知的，比如知识、预期、目标、回报等，也就是（goal-driven）的。

  这种模型速度慢，需要feedback和训练，一般针对某种特定的问题。给定不同的任务去观察同一幅图像，眼动数据差别很大。

  那么在top-down模型下，我们怎么决定眼动呢？一般有一下3种特征我们需要关注。

  1. Object Features

    我们想要寻找的目标的特征，这就是target-driven attention guidance。Guided search theory 指出对不同的目标的attention是有区别的，这可以通过调整不同特征对attention的贡献的权重来实现。

  2. Scene context

    人通过很短时间（少于80ms）观察一幅图像可以获得一些很模糊的基本信息（gist）。尽管不能观察到很多细节，但这些信息可用于比较粗糙的场景区分（不是语义（semantic）上的分类）。这种方式对于大规模的场景搜索很有用。

  3. Task demands

    任务和眼动的关系非常紧密。

#### 2.2 Spatial vs. Spatio-temporal Models （f3）

#### 2.3 Overt vs. Covert attention

#### 2.4 Space-based vs. Object-based Models （f9）

#### 2.5 Features

显著性的计算模型主要使用了3种特征：

- intensity（intensity contrast，luminance contrast）

  通常用3个颜色通道的平均值表示，并使用center-surround处理。这种处理模仿了lateral geniculate nucleus（LGN）和V1 cortex中神经元的反馈。

- color

  用红-绿通道、蓝-黄通道实现，或者用其他色彩空间也行，比如HSV或者Lab。这模仿了V1 cortex中color-opponent neurons的行为。

- orientation

  通常用oriented Gabor filters实现的卷积来实现，或者用oriented masks来实现。这个特征的灵感源于从MT和MST区域中的对方向和动作有选择作用的神经元。

#### 2.6 Stimuli and Task Type

- 刺激有2种分类方式：


  - 静态的和动态的

  - 人造的和自然的


- 任务通常可以分为3类

  - Free viewing tasks

  - Visual search tasks

  - Interactive tasks

#### 2.7 Evaluation Measures

将算法输出的saliency map（S）与眼动数据（G）比对。

可以将评估方式划分为3类：

- 基于点

- 基于区域

- 主观评价（好、可接受、坏）

以下是几种常见的评估方式：

- 概率分布模型（**Kullback-Leibler (KL) Divergence**）

  KL通常用于测量2个概率分布之间的距离。对于saliency评价，一般将预测出的saliency map与随机眼动数据进行比较，KL偏差越大，则表示模型有更好的预测性能。因为人一般关注图像中的很小的局部，这部分有较高的反馈值，而对于大多数区域，反馈值较低。

  这种评价方式有以下几种好处：

  1. KL对概率分布的各种区别都比较敏感。

  2. KL对reparameterizations有不变性，比如对分布使用连续单调的非线性函数而不影响评价。

  当然KL也有劣势：KL没有一个上界，当2个分布完全不重合时，KL偏差将为无穷大。

- 2分类模型（**Area Under Curve (AUC)**）

  当作2分类问题，针对saliency map中的每个像素，设置不同的threshold画出ROC曲线，用AUC来评估分类器性能。对reparameterizations有不变性。

- 随机变量模型

  将S和G当作随机变量

  - **Normalized Scanpath Saliency (NSS)**

  对reparameterizations有不变性

  - **Linear Correlation Coefficient (CC)**

  此方法计算2个变量之间的线性关系的强度，常用与评估2张图像之间的关联。当CC值为+1或-1时，2个变量有完美的线性关系。

- **String Editing Distance**

#### 2.8 Datasets

眼动数据包含了图像（用于研究静止注意力）和视频（用于研究动态注意力）。有时候可以用鼠标的追踪来预测眼动，因为这2者有一定的关联，尽管鼠标的追踪有较多的噪声。

### 3. ATTENTION MODELS

我们的重点在于saliency的模型，也就是解释attention behavior。而不是saliency detection，也就是检测分割最显著的区域，尽管这类问题在最初的阶段也使用了saliency operator。

以下模型按事件先后顺序列出。

#### 3.1 Cognitive Models

Cognitive模型是基于生物原理的，，几乎所有的模型都直接或者间接地受到了它的影响。最初的模型（Itti et al.）使用了颜色、强度、方向作为3个基本的特征通道。这个模型后来也成为了benchmark。此模型主要由以下几个步骤组成：

将原图像下采样为Gaussian pyramid，其中每一层都由Red($$R$$)\Green($$G$$),Blue($$B$$)\Yellow($$Y$$),Intensity($$I$$),local orientations($$O_\theta) 这些通道组成。
对patch的每一个通道计算特征并normalize，得出feature map。
将patch的feature map按照不同的feature分别相加，normalize，得出conspicuity maps。
将不同feature的conspicuity maps相加，得到saliency map。

#### 3.2 Bayesian Models

基本原理是基于贝叶斯概率模型，将sensory evidence和prior knowledge结合。

#### 3.3 Decision Theoretic Models

#### 3.4 Information Theoretic Models

有点像检测不常见的特征。

#### 3.5 Graphical Models

概率图模型

#### 3.6 Spectral Analysis Models

频率分析模型。暂时缺乏生物学上的解释。

#### 3.7 Pattern Classification Models

使用机器学习。

#### 3.8 Other Models

### 4. Discussion

此部分就现有模型都应该关注的几个问题进行了讨论。

#### 1. 判断模型是否有生物学理论的支撑

历史上还没有能够直接判断的依据，不过一般来说有生物学理论支撑的模型都有更好的效果，比如Decision theoretic 和 AWS model。因此建立一个用于判断 模型在生物学上的可解释性、合理性 的标准是很重要的。

#### 2. 模型的评价标准

- 不同模型对图像边缘的不同的处理会对最终结果造成很大的影响，因此应该尽可能消除这种影响。

- 一般来说人的注意力都是偏向中心的（很多数据集也是），因此一些有中心偏向（center-bias）的模型一定会比其他模型效果好，比如说trivail Gaussian blob model。因此消除这种不平等的因素也很重要。

#### 3. covert attention研究的缺失。

目前的数据集只能显示出overt attention，比如说眼动数据，然而人眼没有特意去关注的物体也有可能被人covertly施加了关注。

#### 4. 在interactive 环境下 multimodal 数据集的缺失

#### 5. top-down模型的缺失

尽管top-down的特征对于注意力很重要，但现在大部分的模型都是bottom-up的。前向（feed-forward）bottom-up模型一般不需要训练，比较简单。而top-down模型普遍需要回馈（feedback），也需要进行训练来适应某种特定的任务，比较复杂。

#### 6. 不同模型需要的参数数量差别很大

基于Gabor 或者 DOG filters的模型需要大量参数。

spectral saliency 模型需要很少的参数。

### 5. SUMMARY AND CONCLUSION