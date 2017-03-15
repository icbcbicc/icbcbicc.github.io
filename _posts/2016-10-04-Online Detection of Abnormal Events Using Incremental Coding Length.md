---
layout:     post
title:      "Online Detection of Abnormal Events Using Incremental Coding Length"
subtitle:   "论文笔记-2015-AAAI-3"
date:       2016-10-04
author:     "icbcbicc"
header-img: "img/post-bg-12.jpg"
tags: []

---

# Online Detection of Abnormal Events Using Incremental Coding Length

Authors: *Jatanta K.Dutta, Bonny Banerjee*

[PDF](https://pdfs.semanticscholar.org/83f6/d84389dcdc25a4a1462044d7b1cbc2e75eac.pdf)

<br>

本文使用稀疏编码进行异常事件检测。数据预处理、特征提取、稀疏编码、字典生成都使用传统方法。

**创新之处在于引进了 Incremental Coding Length (ICL)作为稀疏表达的评价，它代表了每个特征的熵的增加量。异常事件可以由特征的rarity来定义（罕见的特征意味着异常）。最终，将所有特征的energy按权重相加就可以判断是否有异常了。文中将每一个特征的energy作为rarity， 它是关于ICL的函数。ICL的计算不需要任何参数，也不需要关于数据的先验假设**

<br>

## 1. Abstract

**事件的异常主要是由2个因素同时决定的:**

- 在稀疏表达事件的过程中，每个特征使用的频率

- 稀疏表达中特征的系数

<br>

## 2. Introduction

**异常事件的检测面临3个难点：**

- 无监督的2分类

- 数据量大，无法全部存储

- 数据的分布不恒定

<br>

## 3. Related Work

**异常事件检测通常由以下几个步骤组成（以及其常用方法）：**

- *数据预处理，获得低级表达*
    - Histogram of optical flow (HOF)
    - Muti-scale histogram of optical flow (MHOF)
    - Histogram of optical flow orientation (HOFO)
    - Spatio-temporal gradient
    - 3D Spatio-temporal foreground mask
    - Binary features
    - Backgroud segregation (Foreground detection)
    - 
    - Mixure of optical flow (MPPCA, temporal features)
    - MPPCA+SF
    - Mixtures of dynamic textures (MDT, spatial and temporal features)
    - Chaotic invariant
    - Social force model (SF, spatial features)

- 对低级表达进行抽象，得到中级表达
    - Sparse coding
	    1. Low rank dictoinary (SparseLR)
		2. Compact regularization (SparseCR)
		3. LR+CR
		4. Weighted sparse representation (SparseW)
		5. Sparse combination learning (SCL, 150fps matlab)
		6. Large scale dictionary selection (LSDS)
	- 
    - Hiden Markov model (HMM)
    - Markov random field (MRF, Saligrama)
    - Mixture of probabilistic principle component analysis(MPPCA)
	- Dimensionality reduction(PCA, ICA, clustering)
    - Gaussian mixture model
    - Latent Dirichlet allocation
    - Deep learning

- 对这些表达进行评估，检测出异常（本文所关注的重点）
    - Sparse rconstruction error (SRC)
    - Incremental coding lenth (ICL, entropy gain)
    - 
    - Prediction error
    - Rarity index
    - Information content
    - Density-based scoring


**按使用场景分类 (TODO)**

- Crowded scenario
	- Binary features based on back ground model
	- 3D Spatio-temporal foreground mask fusing Markov Random Field
	- Trajectory-based approaches

- Uncrowded scenario
	- Local abnormal event
	- Global abnormal event

<br>

## 4. Proposed Framework

### 4.1 Video Representation

Spatiotemporal interest point detector

### 4.3 Online Sparse Dictionary Learning

Batch Orthogonal Matching Pursuit （Batch OMP）

### 4.3 Abnoraml Event Detection

假设对某一时刻的输入数据$X(t) = [x_1(t), ..., x_n(t)]$，都有稀疏系数 $\Gamma = [\gamma_1, ... , \gamma_n] \in R^{k*n}$

那么在时刻$t$，对于第 $j$ 个特征的 activity ratio 定义为：

$$p_j(t) = \frac{\sum_{h=1}^n|\Gamma_{j,h}(t)|}{\sum_{i=1}^k\sum_{h=1}^n|\Gamma_{i,h}(t)|}$$

$t$ 时刻的 summary activity ratio 将按照以下方式更新（初始值为$1/k$）：

$$q(t) = (1-\alpha(t))q(t-1)+\alpha(t)p(t)$$

其中，$\alpha(t)$ 是一个时间的函数。当 $\alpha(t) = 1/t$ , $q(t)$ 就是从开始到现在的平均 activity ratio。当$\alpha(t) = 1/t_1$时， （$t_1$ 是一个正的常数）, $q(t)$ 就是前 $t_1$ 个 activity ratio 的平均值。$\alpha(t) = 1/t_1$ 对于数据分布不稳定的情况很有用。

$$ICL(q_j) = \frac{\partial {H(q)} }{\partial {q_j} } = -H(q) - q_j - log q_j - q_j log q_j$$

在计算完 ICL 后，就得到了任意 $t$ 时刻的显著特征集（salient feature set）

$$S(t) = \{j | ICL(q_j(t)) > 0\}$$

$S(t)$ 中的每一个显著特征的energy表示为：

$$\theta_j(t) = \frac{ICL(q_j(t))}{\sum_{i \in S(t)}ICL(q_i(t))} \quad s.t. \quad j \in S(t)$$

对于不属于$S(t)$的特征：

$$\theta_j(t) = 0 \quad s.t. \quad j \notin S(t)$$

$\theta_j(t)$ 表示了用$j$特征来表示输入的罕见程度

最终，每一个cube的 anomaly score 定义为：

$$g = |\gamma|^T\theta$$

将连续几帧的所有 cube 的 anomaly score 经过 gaussian filter 处理作为每一帧的 anomaly map

<br>

## 5. Experimental Result

略
