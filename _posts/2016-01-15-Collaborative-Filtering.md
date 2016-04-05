---
published: true
author: Charles
layout: post
title:  "推荐系统之协同过滤"
date:   2016-01-15 23:22
categories: 推荐系统 机器学习
---

### 协同过滤简介
关于协同过滤的一个最经典的例子就是看电影，有时候不知道哪一部电影是我们喜欢的或者评分比较高的，那么通常的做法就是问问周围的朋友，看看最近有什么好的电影推荐。在问的时候，都习惯于问跟自己口味差不多的朋友，这就是协同过滤的核心思想。

总结来说，协同过滤就是利用“**集体智慧**（wisdom of the crowd）”对信息进行筛选，其基本假设是具有相同或者相似兴趣偏好的用户信息需求也是相似的。

----------

#### 马太效应和长尾理论（Long Tail phenomenon）
**马太效应**（Mattnew Effect）是指强者愈强、弱者愈弱的现象，在互联网中引申为热门的产品受到更多的关注，冷门内容则愈发的会被遗忘的现象。

搜索引擎就非常充分的体现了马太效应——如下面的Google点击热图，越红的部分表示点击多和热，越偏紫色的部分表示点击少而冷，绝大部分用户的点击都集中在顶部少量的结果上，下面的结果以及翻页后的结果获得的关注非常少。这也解释了Google和百度的广告为什么这么赚钱，企业客户为什么要花大力气做SEM或SEO来提升排名——因为只有排到搜索结果的前面才有机会。

![此处输入图片的描述][1]

**长尾理论**（The Long Tail）是网络时代兴起的一种新理论，由美国人克里斯·安德森提出。长尾理论认为，由于成本和效率的因素，当商品储存流通展示的场地和渠道足够宽广，商品生产成本急剧下降以至于个人都可以进行生产，并且商品的销售成本急剧降低时，几乎任何以前看似需求极低的产品，只要有卖，都会有人买。这些需求和销量不高的产品所占据的共同市场份额，可以和主流产品的市场份额相比，甚至更大。

Cutoff point左侧是流行的商品，右侧是不流行的，也称作长尾。长尾下面的面积一般比左侧还大，有太多的items没有被用户发现，这就需要推荐系统帮助人们寻找他们想要的items。

![此处输入图片的描述][2]

----------

#### 推荐系统核心问题
![此处输入图片的描述][3]

常见推荐系统分类：

![此处输入图片的描述][4]

----------

#### 数据收集
用户行为数据可以分为显性反馈和隐性反馈，比如：

![此处输入图片的描述][5]

要实现协同过滤，需要以下几个步骤:[^1]

1. 收集用户偏好
2. 找到相似的用户或物品
3. 计算推荐

![此处输入图片的描述][6]


通过数据收集，并进行适当的预处理后，我们可以得到一个用户偏好的二维矩阵，一维是用户列表，另一维是物品列表，值是用户对物品的偏好，一般是 [0,1] 或者 [-1, 1] 的浮点数值。
 
![此处输入图片的描述][7]

当已经对用户行为进行分析得到用户喜好后，我们可以根据用户喜好计算相似用户和物品，然后基于相似用户或者物品进行推荐，这就是最典型的 CF 的两个分支：基于用户的 CF 和基于物品的 CF。

----------

#### 基于用户的协同过滤推荐 —— User CF[^2]
原理：基于用户对物品的喜好找到相似邻居用户，然后将邻居用户喜欢的物品推荐给目标用户。

![此处输入图片的描述][8]

用户A喜欢物品A和物品C，用户B喜欢物品B，用户C喜欢物品A、物品C和物品D；从这些用户的历史喜好信息中，我们可以发现用户A和用户C的口味和偏好是比较类似的，同时用户C还喜欢物品D，那么我们可以推断用户A可能也喜欢物品D，因此可以将物品D推荐给用户A。

> 实现：将一个用户对所有物品的偏好作为一个向量（Vector）来计算用户之间的相似度，找到K-邻居后，根据邻居的相似度权重以及他们对物品的喜好，为目标用户生成一个排序的物品列表作为推荐，列表里面都是目标用户为涉及的物品。

----------

基本框架：
![此处输入图片的描述][9]

----------

#### 基于物品的协同过滤推荐 —— Item CF
原理：基于用户对物品的喜好找到相似的物品，然后根据用户的历史喜好，推荐相似的物品给目标用户。与User CF类似，只是关注的视角变成了Item。

![此处输入图片的描述][10]

用户A喜欢物品A和物品C，用户B喜欢物品A、物品B和物品C，用户C喜欢物品A，从这些用户的历史喜好可以分析出物品A和物品C是比较类似的，**喜欢物品A的人都喜欢物品C**，基于这个数据可以推断用户C 很有可能也喜欢物品C，所以系统会将物品C推荐给用户C。

> 实现：将所有用户对某一个物品的喜好作为一个向量来计算物品之间的相似度，得到物品的相似物品后，根据用户历史的喜好预测目标用户还没有涉及的物品，计算得到一个排序的物品列表作为推荐。

对比一下，上述两种模型的优缺点[^4]：

![此处输入图片的描述][11]{: width=100%}

----------

#### 基于模型的协同过滤推荐
原理：这是一个典型的机器学习的问题，可以将已有的用户喜好信息作为训练样本，训练出一个预测用户喜好的模型，这样以后用户在进入系统，可以基于此模型计算推荐。

这种方法的问题在于如何将用户实时或者近期的喜好信息反馈给训练好的模型，从而提高推荐的准确度。

主流的基于模型的方法包括：基于聚类的方法，基于矩阵分解的方法，隐语义概率模型，贝叶斯网络等。

----------

#### 相似度计算

**Jaccard Similarity**

$$sim(A,B) = \frac{|r_A \bigcap r_B|}{|r_A \bigcup r_B|}$$

![此处输入图片的描述][12]

----------

**Cosine Similarity**

$$sim(u,v) = \frac{\mathbf{r}_u\cdot \mathbf{r}_v}{\lVert \mathbf{r}_u \rVert_2 \lVert \mathbf{r}_v \rVert_2}$$

![此处输入图片的描述][13]

----------

**Pearson Correla1on**

$$ \rho_{X,Y}= \frac{\operatorname{cov}(X,Y)}{\sigma_X \sigma_Y} =\frac{\sum ^n _{i=1}(x_i - \bar{x})(y_i - \bar{y})}{\sqrt{\sum ^n _{i=1}(x_i - \bar{x})^2} \sqrt{\sum ^n _{i=1}(y_i - \bar{y})^2}}$$

![此处输入图片的描述][14]

----------

#### 推荐系统的评测指标[^3]

 - **准确率**: 准确率就是最终的推荐列表中有多少是推荐对了的。
 - **召回率**: 召回率就是推荐对了的占全集的多少。
 - **覆盖率**: 覆盖率表示推荐的物品占了物品全集空间的多大比例。
 - **新颖度**: 新颖度是为了推荐长尾区间的物品。用推荐列表中物品的平均流行度度量推荐结果的新颖度。如果推荐出的物品都很热门，说明推荐的新颖度较低，否则说明推荐结果比较新颖。

![此处输入图片的描述][15]

----------


### 推荐系统中的难点
数据的稀疏性问题是协同过滤算法所面临的最大挑战。

![此处输入图片的描述][16]

----------

待整理：

  1. [Collaborative Filtering Recommender Systems](http://files.grouplens.org/papers/FnT%20CF%20Recsys%20Survey.pdf)
  2. [Mining Massive Datasets(MMDs) -Jure Leskovec ](http://blog.csdn.net/pipisorry/article/details/49205589)
  3. [推荐引擎初探](http://www.ibm.com/developerworks/cn/web/1103_zhaoct_recommstudy1/index.html)

----------

  [^1]: [深入推荐引擎相关算法：协同过滤](http://www.ibm.com/developerworks/cn/web/1103_zhaoct_recommstudy2/index.html)
  [^2]: [协同过滤推荐及相似性度量](http://www.codeweblog.com/%E5%8D%8F%E5%90%8C%E8%BF%87%E6%BB%A4%E6%8E%A8%E8%8D%90%E5%8F%8A%E7%9B%B8%E4%BC%BC%E6%80%A7%E5%BA%A6%E9%87%8F/)
  [^3]: [推荐系统学习：协同过滤实现](http://wuchong.me/blog/2014/04/19/recsys-cf-study/)
  [^4]: 推荐系统实战


  [1]: http://7xjbdi.com1.z0.glb.clouddn.com/search-click-1.jpg
  [2]: http://7xjbdi.com1.z0.glb.clouddn.com/conceptual.jpg
  [3]: http://7xjbdi.com1.z0.glb.clouddn.com/recomend_problems.png
  [4]: http://7xjbdi.com1.z0.glb.clouddn.com/2016-03-09_123501.png
  [5]: http://7xjbdi.com1.z0.glb.clouddn.com/im_ex.png
  [6]: http://7xjbdi.com1.z0.glb.clouddn.com/FileteringCollaborative_inv_web400x270.png
  [7]: http://7xjbdi.com1.z0.glb.clouddn.com/p1fig1.png
  [8]: http://7xjbdi.com1.z0.glb.clouddn.com/usercf.png
  [9]: http://7xjbdi.com1.z0.glb.clouddn.com/user_user.png
  [10]: http://7xjbdi.com1.z0.glb.clouddn.com/itemcf.png
  [11]: http://7xjbdi.com1.z0.glb.clouddn.com/2016-03-09_202242.png
  [12]: http://7xjbdi.com1.z0.glb.clouddn.com/2016-03-09_192911.png
  [13]: http://7xjbdi.com1.z0.glb.clouddn.com/2016-03-09_194150.png
  [14]: http://7xjbdi.com1.z0.glb.clouddn.com/psm_2002.png
  [15]: http://7xjbdi.com1.z0.glb.clouddn.com/81b78497jw1efj1yg6uywj20kg0cm778.jpg
  [16]: http://7xjbdi.com1.z0.glb.clouddn.com/2016-03-09_122652.png