---
published: true
author: Charles
layout: post
title:  "Regularized Regression: A Bayesian point of view"
date:   2016-03-09 15:21
categories: 机器学习
---

#### 过拟合
谈正则化之前，我们先来看一看过拟合问题。

![此处输入图片的描述][1]

以一维的回归分析为例，如上图，如果用高阶多项式去拟合数据的话，可以使得训练误差$E_{in}$很小，但是在测试集上的误差就可能很大。

造成这种现象的原因就是因为我们使用的模型过于复杂，根据VC维理论：**VC维**很高的时候，就容易发生$E_{in}$（Bias）很低，但$E_{out}$(Variance)[^1]很高的情形.

![此处输入图片的描述][2]

### 贝叶斯角度谈正则化
解决 overfitting 最常用的办法就是 regularization，我们常用的有：$L_1$正则，$L_2$正则等。

通常我们知道$L_1$正则会使得参数稀疏化，$L_2$正则可以起到平滑的作用，关于这方面的讲解已经很多[^3]。今天我们从贝叶斯理论的角度来审视下正则化。

![此处输入图片的描述][3]

> 我们先抛给大家一个结论：从贝叶斯的角度来看，正则化等价于对模型参数引入**先验约束**。（不添加正则化约束，相当于参数的高斯先验分布有着无穷大的协方差，可以想象一下）

#### Linear Regression
我们先看下最原始的Linear Regression[^5]:

![此处输入图片的描述][4]

$$\begin{align*}
 & p(\epsilon^{(i)})  = \frac{1}{\sqrt{2\pi}\delta}exp\left(  -\frac{(\epsilon^{(i)})^2}{2\delta^2} \right)\\
 \Rightarrow & p(y^{(i)}|x^{(i)};\theta) = \frac{1}{\sqrt{2\pi}\delta}exp\left( -\frac{(y^{(i)} - w^Tx^{(i)})^2}{2\delta^2}  \right)
\end{align*}$$

由最大似然估计(MLE):

$$\begin{align*}
L(w) & = p(\vec{y}|X;w)\\
& = \prod_{i=1}^{m} p(y^{(i)}|x^{(i)};\theta)\\
& = \prod_{i=1}^{m} \frac{1}{\sqrt{2\pi}\delta}exp\left( -\frac{(y^{(i)} - w^Tx^{(i)})^2}{2\delta^2}  \right)
\end{align*}$$

取对数：

$$\begin{align*}
l(w) & = \log L(w)\\
& =\log \prod_{i=1}^{m} \frac{1}{\sqrt{2\pi}\delta}exp\left( -\frac{(y^{(i)} - w^Tx^{(i)})}{2\delta^2}  \right)\\
& = \sum_{i=1}^{m} \log \frac{1}{\sqrt{2\pi}\delta}exp\left( -\frac{(y^{(i)} - w^Tx^{(i)})^2}{2\delta^2}  \right)\\
& = m \log \frac{1}{\sqrt{2\pi}\delta} - \frac{1}{\delta^2}\cdot \frac{1}{2} \sum_{i=1}^{m} (y^{(i)} - w^Tx^{(i)})^2
\end{align*}$$

即：

$$w_{MLE} = \arg \underset{w}{\min} \sum_{i=1}^{m} (y^{(i)} - w^Tx^{(i)})^2 \tag{1}$$

这就导出了我们原始的least-squares损失函数，但这是在我们对参数 $w$ 没有加入任何先验约束的情况下。在数据维度很高的情况下，我们的模型参数很多，模型复杂度高，容易发生过拟合。

这个时候，我们可以对参数 $w$ 引入先验分布，让参数稀疏化。

####  Ridge Regression
我们对参数 $w$ 引入协方差为 $\alpha$ 的零均值高斯分布先验。

![此处输入图片的描述][5]

由于引入了先验分布，我们用最大后验估计(MAP)：
$$\begin{align*}
L(w) & = p(\vec{y}|X;w)p(w)\\
& = \prod_{i=1}^{m} p(y^{(i)}|x^{(i)};\theta)p(w)\\
& = \prod_{i=1}^{m} \frac{1}{\sqrt{2\pi}\delta}exp\left( -\frac{(y^{(i)} - w^Tx^{(i)})^2}{2\delta^2}  \right)\prod_{j=1}^{n}\frac{1}{\sqrt{2\pi\alpha}}exp\left( -\frac{(w^{(j)})^2}{2\alpha}  \right)\\
& = \prod_{i=1}^{m} \frac{1}{\sqrt{2\pi}\delta}exp\left( -\frac{(y^{(i)} - w^Tx^{(i)})^2}{2\delta^2}  \right)\frac{1}{\sqrt{2\pi\alpha}}exp\left( -\frac{w^Tw}{2\alpha}  \right)
\end{align*}$$

$$\begin{align*}
l(w) & = \log L(w)\\
& = m \log \frac{1}{\sqrt{2\pi}\delta}+ n \log \frac{1}{\sqrt{2\pi\alpha}} - \frac{1}{\delta^2}\cdot \frac{1}{2} \sum_{i=1}^{m} (y^{(i)} - w^Tx^{(i)})^2 - \frac{1}{\alpha}\cdot \frac{1}{2} w^Tw\\
 \Rightarrow & w_{MAP_{Guassian}} = \arg \underset{w}{\min} \left( \frac{1}{\delta^2}\cdot \frac{1}{2} \sum_{i=1}^{m} (y^{(i)} - w^Tx^{(i)})^2 + \frac{1}{\alpha}\cdot \frac{1}{2} w^Tw \right)
\end{align*}$$

等价于：

$$J_R(w) = \frac{1}{n}\lVert y- w^TX \rVert^2 + \lambda \lVert w \rVert^2$$

这不就是 **Ridge Regression** 吗？

![此处输入图片的描述][6]

看我们得到的参数，在零附近是不是很密集，老实说 ridge regression 并不具有产生稀疏解的能力，也就是说参数并不会真出现很多零。假设我们的预测结果与两个特征相关，$L_2$正则倾向于综合两者的影响，给影响大的特征赋予高的权重；而$L_1$正则倾向于选择影响较大的参数，而舍弃掉影响较小的那个。实际应用中 $L_2$ 正则表现往往会优于 $L_1$正则，但 $L_1$ 正则会大大降低我们的计算量。

> Typically ridge or ℓ2 penalties are much better for minimizing prediction error rather than ℓ1 penalties. The reason for this is that when two predictors are highly correlated, ℓ1 regularizer will simply pick one of the two predictors. In contrast, the ℓ2 regularizer will keep both of them and jointly shrink the corresponding coefficients a little bit. Thus, while the ℓ1 penalty can certainly reduce overfitting, you may also experience a loss in predictive power. 

http://www.unicog.org/pmwiki/uploads/Main/PresentationMM_02_10.pdf
Bayesian linear Regression
http://freemind.pluskid.org/machine-learning/sparsity-and-some-basics-of-l1-regularization/


  [^1]: [Bias 和 Variance](http://charlesx.top/2016/03/Bias-Variance/)
  [^2]: [《A Few useful things to Know About machine Learning》读后感](http://blog.csdn.net/danameng/article/details/21563093)
  [^3]: [What is the difference between L1 and L2 regularization?](https://www.quora.com/What-is-the-difference-between-L1-and-L2-regularization)
  [^4]: [ Bayesian Linear Regression](http://web.cse.ohio-state.edu/~kulis/teaching/788_sp12/scribe_notes/lecture5.pdf)
  [^5]: [Bayesian statistics and regularization](http://cs229.stanford.edu/notes/cs229-notes5.pdf)


  [1]: http://7xjbdi.com1.z0.glb.clouddn.com/2016-03-10_170512.png
  [2]: http://7xjbdi.com1.z0.glb.clouddn.com/2016-03-10_171146.png?imageView2/2/w/400
  [3]: http://7xjbdi.com1.z0.glb.clouddn.com/117ec65eb609d8ea9f05c227130724a6_b.png?imageView2/2/w/400
  [4]: http://7xjbdi.com1.z0.glb.clouddn.com/2016-03-10_180932.png
  [5]: http://7xjbdi.com1.z0.glb.clouddn.com/ridge_re.png
  [6]: http://7xjbdi.com1.z0.glb.clouddn.com/2016-03-10_195835.png?imageView2/2/w/400