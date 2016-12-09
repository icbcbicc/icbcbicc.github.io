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



#### 2.1 Bottom-up vs. Top-down Models



#### 2.2 Spatial vs. Spatio-temporal Models



#### 2.3 Overt vs. Covert attention



#### 2.4 Space-based vs. Object-based Models



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



### 5. SUMMARY AND CONCLUSION