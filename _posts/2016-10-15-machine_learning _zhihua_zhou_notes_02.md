---
layout: post
title: "Machine Learning (Zhihua Zhou) Notes 02"
subtitle:   "读书笔记"
author:     "icbcbicc"
header-img: "img/post-bg-19.jpg"
tags: ["machine learning"]
---

<<<<<<< HEAD
## 3. 线性模型

### 3.2 线性回归
- 基本概念

    - 线性回归：用线性模型拟合目标函数。

    - 均方误差：线性回归中常用的性能度量，它的几何意义是欧几里得距离(Euclidean distance)。

    - 最小二乘法(least square method)：基于均方误差最小化来求解模型的方法。几何意义是在目标空间中找到一个超平面，使样本点到该平面的欧式距离之和最小。

- 多元线性回归(multivariate linear regression)

    求最优$\hat{\omega}$使目标函数$E_\hat{\omega}$最小:

    $$E_\hat{\omega} = (y-X\hat{\omega})^T(y-X\hat{\omega})$$

	对$\hat{\omega}$求导：

    $$\frac{\partial E_{\hat{\omega}}}{\partial \hat{\omega}} = 2X^T(X\hat{\omega}-y)$$

    当$X^TX$是满秩矩阵(full-rank matrix)或正定矩阵(positive definite matrix)时，可直接求得最优解：

    $$\hat{\omega} = (X^TX)^{-1}X^Ty$$

	最终模型：

    $$f(x_i) = x_i^T(X^TX)^{-1}X^Ty$$

    然而当$X^TX$不是满秩矩阵时，$X$的列数多于行数，也就是特征数比样本数多。此时方程有多个最优解，需要引入正则化(regularization)来选择一个$\hat{\omega}$。

- 广义线性模型(generalized linear model)

    $$y = g^{-1}(\omega^Tx+b)$$

    其中，$g(.)$是单调可微函数。称之为联系函数(link function)

    较长用的联系函数是$ln(.)$，此时称之为对数线性回归(log-linear regression)

- 对数几率回归(logistic regression)

	用于分类任务，将输出映射到$\{0,1\}$的集合上。

    $$y = \frac{1}{1+e^{-(\omega^Tx+b)}}$$
    $\to$
    $$ln\frac{y}{1-y} = \omega^Tx+b$$

    若将$y$视为取正例的概率，则$1-y$为取反例的概率。

    $\frac{y}{1-y}$称为**几率(odds)**，反映了相对可能性。

    $ln\frac{y}{1-y}$称为**对数几率(logit)**

    可通过**极大似然法(maximum likeihood method)**估计最优解：

    $$l(\omega,b) = \sum_{i=1}^{m}ln\{p(y_i|x_i;\omega;b)\}$$

    $=$

    $$\sum_{i=1}^{m}ln\{y_ip(y=1|x_i;\omega;b)+(1-y_i)p(y=0|x_i;\omega;b)\}$$

    $=$

    $$\sum_{i=1}^{m}(-y_i(\omega;b)^Tx_i+ln(1+e^{(\omega;b)^T}x_i))$$

    此式为关于$(\omega;b)$高阶可导连续凸函数。可用**梯度下降、牛顿法**进行求解。

### 3.4 线性判别分析(Linear Discriminant Analysis，LDA，亦称Fisher判别分析)

- 主要思想：将共有$N$类的样本投影到一个$N-1$维的空间，使同类的投影点接近，不同类的投影点相互远离。

- 指标

	- $X_i,\mu_i,\Sigma_i$分别为第$i$类的样本点、均值向量、协方差矩阵。$\mu$为所有样本的均值向量。假设共$N$类，第$i$类有$m_i$个样本，共$m$个样本。

	- 全局散度矩阵：

	$$S_t = S_b+S_w = \sum_{i=1}^m (x_i-\mu)(x_i-\mu)^T$$

    - 类内散度矩阵：

	$$S_w = \sum_{i=1}^N S_{w_i} = \sum_{i=1}^N \sum_{x\in X_i}(x-\mu)(x-\mu)^T$$

	- 类间散度矩阵：

	$$S_b = S_t-S_w = \sum_{i=1}^N m_i(\mu_i-\mu)(\mu_i-\mu)^T$$

- 优化：

	- 可使用$S_t,S_w,S_b$中的任意2个指标进行优化。常用的优化目标是：

	$$max\frac{tr(W^TS_bW)}{tr(W^TS_wW)} \quad s.t. \quad W \in R^{d \times (N-1)}$$

	- 此式可以通过**拉格朗日乘子法**转化为广义特征值问题：

    $$S_bW = \lambda S_wW$$

    - $W$的闭式解：**$S_w^{-1}S_b$的$N-1$个最大广义特征值所对应的特征向量**组成的矩阵。

### 3.5 多分类学习

将多分类任务拆分为多个2分类任务

**拆分策略**

- 一对一(One vs. One, OvO)：共$N(N-1)/2$个分类器

- 一对多(One vs. Rest, OvR)：共$N$个分类器。

- 多对多(Many vs. Many, MvM)

	用、纠错输出码(Error Correcting Output Codes, EOOC)

    - 对数据进行$M$次划分，得到$M$组{训练集，测试集}，从而得到$M$个分类器。

	- 对每个样本，分别用$M$个分类器分类，这些预测标记组成一个编码序列。

	- 将预测编码与每个类别自己的编码(One-hot码)比较，距离(海明距离，欧氏距离)最近的为最终预测结果。

	- EOOC对与分类器的错误有一定容忍和修正能力。

### 3.6 类别不平衡问题

**解决方案**：

- 再缩放(rescaling)：$\frac{y}{1-y}>\frac{m^+}{m+-}$：预测为正例。其中$m^+,m^-$分别为正例、反例的数目。

- 欠采样(undersampling):去除一部分样本使得样本平衡

    将类别较多的样本划分为几个集合，形成多组{训练集，测试集}分别学习。虽然每组是欠采样，但全局上却没有欠采样，充分利用了数据。

- 过采样(oversampling):增加一部分样本使得样本平衡

	不能直接对原有样本进行重复采样，否则将会出现严重的过拟合。

## 4.决策树

TODO：信息增益、增益率、基尼指数、剪枝、连续值处理、多变量决策树（斜划分）
=======
## 3. 线性模型
>>>>>>> 4990ff15cf7e3e837ccb48ebafa06fe3db13542f
