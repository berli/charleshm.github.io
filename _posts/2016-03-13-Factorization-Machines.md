---
published: true
author: Charles
layout: post
title:  "Factorization Machines"
date:   2016-03-13 9:10
categories: 机器学习 推荐系统
---

**Factorization Machines**, 简单点理解可以看做是在线性回归的基础上，考虑特征之间的相互联系 (interaction)。

$$\hat{y}(x) = w_0 + \sum_{i=1}^{n} w_ix_i + \sum_{i=1}^{n-1}\sum_{j=i+1}^{n}w_{ij}x_ix_j  \tag{1}$$

和我们在SVD++里面提到的trick一样，$w_{ij}$使得我们的**参数过多**，在数据稀疏的情况下很容易发生过拟合。我们利用矩阵分解来替换它：

$$\hat{w}_{ij} = v_i^Tv_j:=\sum_{l=1}^{k}v_{il}v_{jl}$$

现在，我们的表达式变为：

$$\hat{y}(x) = w_0 + \sum_{i=1}^{n} w_ix_i + \sum_{i=1}^{n-1}\sum_{j=i+1}^{n}(v_i^Tv_j)x_ix_j  \tag{2}$$