---
title: "更多关于主动重建的文章"
tags: 
---
## 背景

一天争取消化一篇文章。

<!--more-->

## 11-30 
- 标题：Information-Theoretic Exploration with Bayesian Optimization
- 作者单位：斯蒂文斯理工大学
- 会议: IROS 2016

### 问题定义

#### 信息增益
$$
H(m)=-\sum_{i} \sum_{j} p\left(m_{i, j}\right) \log p\left(m_{i, j}\right)
$$

其中i 表示栅格的索引，而j表示栅格所有的可能的状态的索引。对于一个没有观测到的栅格，其概率为$p(m_{i, j}) = 0.5$

对于某个特定的C-space中的一点$x_i$而言，其所能得到的信息增益为
$$
I\left(m, x_{i}\right)=H(m)-H\left(m \mid x_{i}\right)
$$

其中$H(m)$为当前地图的信息增益，$H(m|x_i)$为给定在$x_i$处的观测后所期望的地图的信息增益。因此，该问题则变为找到C-space中的某一点$x^*$，使得信息增益最大

$$
x^{*}=\underset{x_{i} \in \mathscr{G}_{\text {action}}}{\operatorname{argmax}} I\left(m, x_{i}\right)
$$

其中$\mathscr{G}_{\text{action}}$是的C-space的子集，是下一步可能去的地方。

#### Baysian Optimaztion

引用论文：《Gaussian Process Optimization in the Bandit Setting:No Regret and Experimental Design》


- Multi-armed bandit problem (多臂老虎机问题)

实际上是用高斯过程回归，取待测试的位置的一个子集来做高斯过程训练，然后用其余的位置来测试，得到剩余的位置的信息增益与协方差，然后评价函数为

$$
x_{t}=\underset{x \in \mathscr{G}_{\text {action}}}{\operatorname{argmax}} \mu(x)+\beta \sigma(x)
$$

根据这个公式来算最佳位置。

#### 代码阅读

其代码在github: [turtlebot_exploration_3d](https://github.com/RobustFieldAutonomyLab/turtlebot_exploration_3d)

我的阅读如下：
![](/pics/more_active/turtlebot_exploration_3d.png)