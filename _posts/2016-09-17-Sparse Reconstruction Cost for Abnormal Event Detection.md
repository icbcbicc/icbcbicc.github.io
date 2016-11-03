---
layout:     post
title:      "Sparse Reconstruction Cost for Abnormal Event Detection"
subtitle:   "论文笔记-2011-CVPR-225"
date:       2016-10-23
author:     "icbcbicc"
header-img: "img/post-bg-20.jpg"
tags: []
---

# Sparse Reconstruction Cost for Abnormal Event Detection
Authors: *Yang Cong, Junsong Yuan, Ji Liu*  
[PDF](http://www.cs.rochester.edu/~jliu/paper/Cong-Yuan-CVPR11.pdf)

<br>

## Introduction

<br>

#### Conventional Algorithms

- ***Probability Model***

	Detect testing sample with lower probability as anormaly by fitting a probability model to the training data. However, the required number of training data increases exponentially with the feature dimension, and it's unrealistic to collect enough data for density estimation in practice

#### Sparse Reconstruction Cost (SRC)

	We propose SRC based on the weighted $l_1$ minimization.

- Normal event: generate sparse reconstruction coefficients with a **small SRC**

- Abnormal events: generate a dense representation with a **large SRC**

#### 2 types of abnormal events:

- Local abnormal event (LAE)

	The local behavior is different from its spatio temporal neighborhoods

- Global abnormal event (GAE)

	The whole scene is abnormal, even though any individual lcoal behavior can be normal

- New dictionary selection method

	Reduce the size of the basis set $\phi$ for an efficient reconstruction