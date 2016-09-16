---
layout:     post
title:      "Sparse and Redundant Modeling of Image Content Using an Image-Signature-Dictionary"
subtitle:   "论文笔记"
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

- We assume that each patch is represented by a **ﬁxed number of atoms**, L

-  The **energy function** that the ISD is expected to minimize :

$$\hat{d_s} = Arg min_{d_s} \sum_{i=1}^N \epsilon_i^2(d_s) $$

s.t.

$$\epsilon_i^2(d_s) = min_x {\|y_i-\sum_{k=1}^{\sqrt{m}}\sum_{l=1}^{\sqrt{m}}x_{[k,l]}C_{[k,l]}d_s\|}_2^2$$

s.t. 

$${\|x\|}_0 \le L,\qquad 1 \le i \le N$$

- ***Two stages in training***

	- **The sparse coding stage**

	- **Dictionary update stage**
