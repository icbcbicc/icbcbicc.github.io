---
layout:     post
title:      "Sparse and Redundant Modeling of Image Content Using an Image-Signature-Dictionary"
subtitle:   "论文笔记-2008-SIAM-109"
date:       2016-09-16
author:     "icbcbicc"
header-img: "img/post-bg-02.jpg"
tags: []
---

# Sparse and Redundant Modeling of Image Content Using an Image-Signature-Dictionary  
Authors: *Michal Aharon and Michael Elad*  
[PDF](http://epubs.siam.org/doi/pdf/10.1137/07070156X)

<br>

## Introduction of Sparse Representation

#### Background

- In sparse and redundant modeling, a signal $y \in R^n$ is represented as a linear combination of some atoms taken from a dictionary $D \in R^{n*m}$ which contains $m$ atoms $d_i \in R^n$. A representation of the signal $y$ is then any vector $x \in R^n$ satisfying $y = Dx$

- $m > n$: the representation is ***redundant***.

- The solutions to $y = Dx$ are infinite and we prefer the sparest one, i.e, ***the one with the smallest ${\|\|x\|\|}_0$***

- The task of ***computing a representation*** of a signal can be formly described by  

$$min_x{\|x\|}_0 \qquad s.t. \qquad y=Dx$$

- However, the ***constrait above is often relaxed*** and replaced by ${\|\|y-Dx\|\|}_2 \le \epsilon$. This allows for additive noise and model deviations

<br>

#### Problem

- Solving this problem was proved to be an **NP-hard** problem

- **Two approximation techniques :**

	- Matching-pursuit (MP) methods that ﬁnd the solution one entry at a time in a greedy way

	-  Basis-pursuit (BP) algorithm that replaces the $L_0$ norm by the $L_1$ norm

- **Two types of dictionary**

	- Prespeciﬁed dictionaries

	- Dictionaries obtained by learning procedure

<br>

#### This paper

- The **image-signature-dictionary** (ISD), is an 2D image in which each patch can serve as a representing atom  

- A near shift-invariant property is obtained, due to the **overlap between atoms** extracted from the ISD in nearby locations  

- By **taking patches of varying sizes**, near scale-invariance is potentially obtained and exploited  

<br>

## The Structure of ISD

$$y = \sum_{k=1}^{\sqrt{m}}\sum_{l=1}^{\sqrt{m}}x_{[k,l]}C_{[k,l]}d_s = Dx$$

- $D_s \in R^{\sqrt{m}*\sqrt{m}} : $ signature dictionary

- $y' \in R^{\sqrt{n}*\sqrt{n}} : $ imasge patch and its single column form $y \in R^n$

- $d_s \in R^m : $  a vector obtained by a column lexicographic ordering of the ISD

- $C_{[k,l]} : $ the linear operator that extract a patch of size $\sqrt{n}*\sqrt{n}$ from the $D_s$ in location $[k,l]$

- $C_{[k,l]}d_s : $ the extracted patch as a column vector

-  $x$ is the concatenation of the $m$ coeﬃcients in the array $x_{[k,l]}$

<br>

## ISD Training

<br>

#### Energy Function  

We assume that each patch is represented by a **ﬁxed number of atoms**, L. The **energy function** that the ISD is expected to minimize :

$$\hat{d_s} = Arg min_{d_s} \sum_{i=1}^N \epsilon_i^2(d_s)$$

s.t.

$$\epsilon_i^2(d_s) = min_x {\|y_i-\sum_{k=1}^{\sqrt{m}}\sum_{l=1}^{\sqrt{m}}x_{[k,l]}C_{[k,l]}d_s\|}_2^2$$

s.t. 

$${\|x\|}_0 \le L,\qquad 1 \le i \le N$$

#### Two stages in training

- **The sparse coding stage: find $\hat{x_i}$**

	Assuming that $d_s$ is known and ﬁxed, we can solve the **inner optimization problem** and ﬁnd the sparsest representation $\hat{x_i}$ for each example $y_i$

	$$\hat{x_i} = Argmin_x {\|y_i-\sum_{k=1}^{\sqrt{m}}\sum_{l=1}^{\sqrt{m}}x_{[k,l]}C_{[k,l]}d_s\|}_2^2 \qquad s.t. \qquad \|x_o\| \le L$$

	This problem can be solved by any **pursuit algorithm**, such as a greedy method——**the orthogonal matching pursuit** (OMP)

	The OMP selects at each stage an atom from the dictionary that best resembles the residual. After each such selection, the signal is back-projected onto the set of chosen atoms, and the new residual signal is calculated ***(Don't understand)***

- **Dictionary update stage: $\hat{d_s}$**

	 Assuming that the sparse representation vectors $\hat{x_i}$ have been computed, we seek the best ISD $d_s$ to minimize: 

	 $$E(d_s) = \sum_{i=1}^N {\|y_i-\sum_{k=1}^{\sqrt{m}}\sum_{l=1}^{\sqrt{m}}x_{[k,l]}^iC_{[k,l]}d_s\|}_2^2$$

	 The **gradient of the error expression**:

	 $$\frac{\partial E(d_s)}{\partial d_s} = Rd_s - p$$

	 where

	 $$R = \sum_{i=1}^N {\lgroup \sum_{k=1}^{\sqrt{m}}\sum_{l=1}^{\sqrt{m}}x_{[k,l]}^iC_{[k,l]} \rgroup}^T * {\lgroup \sum_{k=1}^{\sqrt{m}}\sum_{l=1}^{\sqrt{m}}x_{[k,l]}^iC_{[k,l]} \rgroup} \in R^{m*m}$$

	 $$p = \sum_{i=1}^N {\lgroup \sum_{k=1}^{\sqrt{m}}\sum_{l=1}^{\sqrt{m}}x_{[k,l]}^iC_{[k,l]} \rgroup}^T*y_i \in R^m$$

	 Thus, the **optimal ISD** is obtained simply by:

	 $$\hat{d_s} = R^{-1}p$$

- **Stochastic gradient appoach (SG)**

	- The algorithm above updates the $x_i$ for all examples {y_i}_{i=1}^N and then updates the ISD

	- Another way is to update the ISD after the computation of each $x_i$ (SG):  

	$$\hat{d_s^{new}} = \hat{d_s^{lod}} - \mu \frac{\partial E(d_s^{lod})}{\partial d_s^{lod}} = \hat{d_s^{lod}} - \mu {\lgroup \sum_{k=1}^{\sqrt{m}}\sum_{l=1}^{\sqrt{m}}x_{[k,l]}^iC_{[k,l]} \rgroup}^T {\lgroup \sum_{k=1}^{\sqrt{m}}\sum_{l=1}^{\sqrt{m}}x_{[k,l]}^iC_{[k,l]}\hat{d_s^{old}}-y_i \rgroup}$$

	The step-size parameter $\mu$ is typically set as $\mu = \frac{\mu_0}{Iteration}$


#### Treatment of the mean

- Remove the mean in training and testing data

- Redefine the $C_{[k,l]}$ to also remove the mean of the extrated patch

