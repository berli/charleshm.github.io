---
published: true
author: Charles
layout: post
title:  "Feature Discretization"
date:   2016-04-02 15:30
categories: 机器学习
---

#### 特征离散化
**连续特征离散化**就是采取各种方法将连续的区间划分为小的区间，并将这连续的小区间与相应的离散值关联起来。

> 比方说年龄特征是一个连续的特征，把年龄层分成5－18岁（中小学生），19－23岁（大学生），24－29岁（工作前几年），30－40岁（成家立业），40－60岁（中年人）,从某些层面来说比连续的年龄数据更容易理解不同年龄层人的特性。

但最优离散化问题已经被证明是一个 NP-hard 问题。


----------


#### 为什么要做特征离散化

 - 很多机器学习算法只能处理**离散值**
 - 离散特征相对于连续特征来说更接近于**知识层面**的表示
 - 存储方便，同时减少计算量
 - **可理解性强**
 - 提高**计算精度** 
 
> Discrete values have important roles in data mining and knowledge discovery. They are about intervals
of numbers which are more concise to represent and specify, easier to use and comprehend as they are closer to a **knowledge-level representation** than continuous values. Many studies show induction tasks can benefit from discretization: rules with discrete values are normally shorter and more **understandable** and discretization can lead to **improved predictive accuracy**. Furthermore, many induction algorithms found in the literature **require discrete features**[^1].


----------


### 常用方法
![此处输入图片的描述][1]

----------

#### 分类[^2]
- **分裂式的和合并式的**
- 
分裂的离散化方法起始的分裂点列表是**空的**，通过离散化过程逐渐往列表中**加入分裂点**，而合并的离散化方法则是将所有的连续值**都看作**可能的分裂点，再逐渐**合并**相邻区域的值形成区间。

- **有监督的和无监督**
- 
根据离散化方法是否使用数据集的类信息，离散化方法可以分为有监督的和无监督的。

- **动态和静态**
- 
**动态**的离散化方法就是在建立分类模型的同时对连续特征进行离散化，如有名的 C4.5。**静态**的离散化方法就是在进行分类之前完成离散化处理。

- **全局的和局部的**
- 
根据离散化过程是否是针对整个训练数据空间的，离散化方法又可分为全局的和局部的。


----------


#### 处理流程

![此处输入图片的描述][2]


----------

#### 举个栗子
**无监督离散化**，
![此处输入图片的描述][3]

**监督离散化**，
![此处输入图片的描述][4]

----------

#### 目前比较火的方法

----------

资料传送门：

- [特征离散化 R package](https://cran.r-project.org/web/packages/discretization/discretization.pdf)
- [离散化方法层次框架图](https://www.processon.com/diagraming/570791a1e4b0bf3d8ff9f82b)

----------


[^1]: [Discretization: An Enabling Technique](http://sci2s.ugr.es/keel/pdf/algorithm/articulo/liu1-2.pdf)
[^2]: [连续特征离散化方法综述](http://www.go-gddq.com/down/2012-03/12031520159066.pdf)

  [1]: http://7xjbdi.com1.z0.glb.clouddn.com/Feature%20Discretization1.png
  [2]: http://7xjbdi.com1.z0.glb.clouddn.com/2016-04-08_194451.png
  [3]: http://7xjbdi.com1.z0.glb.clouddn.com/binning.png?imageView2/2/w/500
  [4]: http://7xjbdi.com1.z0.glb.clouddn.com/entropy.png?imageView2/2/w/500