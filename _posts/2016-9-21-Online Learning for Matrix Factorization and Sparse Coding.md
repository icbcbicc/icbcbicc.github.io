---
layout:     post
title:      "Online Learning for Matrix Factorization and Sparse Coding"
subtitle:   "论文笔记-2001-JMLR-1339"
date:       2016-09-21
author:     "icbcbicc"
header-img: "img/post-bg-20.jpg"
tags: []
---

# Online Learning for Matrix Factorization and Sparse Coding
Authors: *Julien Mairal, Francis Bach, Jean Ponce, Guillermo Sapiro*  
[PDF](http://www.jmlr.org/papers/volume11/mairal10a/mairal10a.pdf)

<br>

![mindmap](/img/b5dfacb7-b178-4002-823e-fc5dcfd2d641.png)
	
<br>

## Introduction

<br>

Linear decompositon of a matrix using learned dictionary instead of pre-defined one

***Usage:***

- low-level: image denoising, texture synthesis, audio processing

- high-level: image classification

***Differences between maxtrix factorization and PCA:*** 

- It doesn't impose that the basis vector to be orthogonal

- Allowing more flexibility to adapt the representation to the data

***Variants matrix factorization problem:***

- Non-negtive matrix factorization

- Sparse PCA

<br>

## Problem Statement

- Optimize the ***empirical cost function:***

	$$f_n(D) = \frac{1}{n}\sum_{i=1}^n l (x_i,D)$$

	- $D\in R^{m * k}$ :dictionary

	- $m < < n$: feature nums less than sample nums

	- $k < < n$: atom nums of dictoinary less than sample nums

	- $k > m$: ***Overcomplete dictionary***: atoms more than feature nums



- $l_1$ sparse coding problem (***basis pursuit***):

	$$l_1(x,D) = min\frac{1}{2}{\|x-D\alpha\|}_2^2+\lambda\|\alpha\|_1 \quad s.t. \quad \alpha \in R^k$$

	- **Disadvantage:**

		There is no direct analytic link between the value of $\lambda$ and the corresponding effective sparsity $\|\alpha\|_0$

	- **Solution:**

		To prevent $D$ from having arbitrarily large values (which would lead to arbitrarily small values of $\alpha$), it is common to ***constrain its columns $d_1, . . . ,d_k$ to have an $l_2-norm$*** less than or equal to 1

		$$C = \{D \in R^{m * k}\quad s.t. \quad \forall j=1, ... ,k \quad d_j^Td_j \le 1\}$$

		It's a joint optimization problem with respet to $D$ and $\alpha=[\alpha_1, ..., \alpha_k] \in R^{k * n}$, which is ***not jointly convex*** but ***convex with respect to each of the two variables*** $D$ and $\alpha$ when the other one is fixed

	- **Optimization method:**

		- ***Alternate between the two variables***, minimizing over one while keeping the other one fixed

		- Since the computation of $\alpha$ dominates the cost of each iteration in this ***block-coordinate descent*** approach, a ***second-order optimization*** technique can be used to accurately estimate $D$ at each step when $\alpha$ is fixed

	- ***Stochastic gradient***, whose rate of convergence is very poor in conventional optimization terms, may in fact in certain settings ***to be faster in reaching a solution with low expected cost than second-order batch***

	- **Dictionary learning:**

		The classical ***projected first-order projected stochastic gradient descent*** consists of a sequence of updates of $D$
			
		$$D_t = \prod [{D_{t-1}-\delta_t \nabla_C l(x_t,D_{t-1})}]$$

		1. $\nabla_C: \quad$ Orthogonal projector onto $C$

		2. $x_t: \quad$ i.i.d samples obtained by cycling on a randomly permuted training set

		3. $\delta_t: \quad$ learning rate(good results are obtained using  $\delta_t = \frac{a}{t+b}$ where $a$ and $b$ are chosen depended on the training data)

		4. 

