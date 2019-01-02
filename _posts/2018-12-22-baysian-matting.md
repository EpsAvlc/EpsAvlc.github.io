---
title: "贝叶斯估计、最大似然估计与baysian matting"
tags: compute-vision
---

## 背景

做cv project，题目是绿幕抠图，搜索到了baysian matting。于是便整理了一些有关贝叶斯公式、最大似然估计的知识，并简单介绍了baysian matting的思想。

<!--more-->

## 概念

### Likelihood(似然)

似然性与概率相近，都是指某种事件发生的可能性，但是在统计学中这两者又有明确的区分：

* **概率**用于在已知一些参数的情况下，预测接下来的观测所得到的结果。即已知参数B，求$P(A\mid B)$。
* **似然性**则是用于在已知某些观测所得到的结果时，对有关事物的性质的参数进行估计。即已知$P(A)$，根据贝叶斯定理$P(B \mid A)=\frac{P(A \mid B)P(B)}{P(A)}$，估计参数B的可能性。

在这种意义上，似然函数可以理解为条件概率的逆反。

### Likelihood function(似然函数)

似然函数是指这样一种函数，该函数的变量的概率密度取决于于某个可以观测的参数（先验知识）。写作(对于离散变量而言)：

$$ L(\theta \mid x) = p_\theta(x)=P_\theta(X=x)$$

其中$\theta$是先验知识，而$x$是随机变量**已取到的值**。$p_\theta(x)$表示已知某个参数$\theta$的情况下，$x$的概率密度。

虽然从式子上，似然函数和概率密度函数是相等的，但是它们是两个完全不同的**数学对象**。前者是关于$\theta$的函数，而后者是关于$x$的函数。所以理解这两个函数相等时，应理解为**函数值相等**。

那么如果有：

$$L(\theta_1 \mid x) = P_{\theta_1}(X=x) > P_{\theta_2}(X=x) = L(\theta_2 \mid x)$$

那么似然函数就反应出这样一个朴素推测：在参数$\theta_1$下随机向量$\textbf{X}$取到值$\textbf{x}$的可能性大于 在参数$\theta_2$下随机向量$\textbf{X}$取到值$\textbf{x}$的可能性。换句话说，我们更有理由相信，相对于$\theta_2$来说,$\theta_1$更有可能是真实值。这里的可能性由概率来刻画。

**Tips**: 在很多场合中竖线\mid符号表示条件概率或者条件分布，所以实际上似然函数的严格书写方式应该是$L(\theta \mid \textbf{x}) = f(\textbf{x} ; \theta)$，因为分号表示右侧的$\theta$只当做参数来理解。

### Maximum Likelihood Estimation-MLE(最大似然估计)

MLE是指给出上式观测值x，求使似然函数达到最大的$\theta$的值。即：

$$ \mathop{\arg\max}_{\theta}  L(\theta \mid x)$$ 

最大似然估计是似然函数最初也是最自然的应用。上文已经提到，似然函数取得最大值表示相应的参数能够使得统计模型最为合理。从这样一个想法出发，最大似然估计的做法是：首先选取似然函数（一般是概率密度函数或概率质量函数），整理之后求最大值。

实际应用中一般会取似然函数的对数作为求最大值的函数，这样求出的最大值和直接求最大值得到的结果是相同的。

## Bayesian Matting (贝叶斯抠图)

论文原址在参考[3]中。

### 关于Digital Matting



## 参考

1. [wikipedia--似然函数](https://zh.wikipedia.org/wiki/%E4%BC%BC%E7%84%B6%E5%87%BD%E6%95%B0)
2. [知乎--如何理解似然函数](https://www.zhihu.com/question/54082000)
3. [A Bayesian Approach to Digital Matting](https://grail.cs.washington.edu/projects/digital-matting/papers/cvpr2001.pdf)
