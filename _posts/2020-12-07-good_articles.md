---
title: "《一文读懂系列》汇总"
tags: 
---
## 背景

搜集了自己在读论文过程中发现的很好的科普性论文。

<!--more-->

## 文章

### 协方差矩阵的理解
[A geometric interpretation of the covariance matrix](https://www.visiondummy.com/2014/04/geometric-interpretation-covariance-matrix/)

我的协方差矩阵理解入门。之后在点云的地面提取等多个方面用到了协方差矩阵。

### 高斯过程

[Understanding Gaussian Process, the Socratic Way](https://towardsdatascience.com/understanding-gaussian-process-the-socratic-way-ba02369d804)

事无巨细的介绍了高斯过程的原理。其中我认为比较重要的一点是

> Gaussian Process does not find a function body that only needs a new x and returns a y in the traditional sense of $f(x)=ax+b$, like what linear regression gives you. Instead, it gives you a mapping between every test location $x_*$ to a function mean value (together with its variance of course). This mapping involves not only the test location x_* but also all the training data X and Y.

[A Visual Exploration of Gaussian Processes](https://www.jgoertler.com/visual-exploration-gaussian-processes/)