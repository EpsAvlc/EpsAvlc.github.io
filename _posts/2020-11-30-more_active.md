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

## 12-10

- 标题：Learn-to-Score: Efficient 3D Scene Exploration by Predicting View Utility
- 作者单位：ETHZ/Microsoft
- 会议: ECCV 2018

### 内容
#### World model

地图中的每个voxel存储两个值$M=(M^o, M^u)$，其中$M^o(v)$是该voxel的占据概率，$M^u(v)$是该voxel的不确定性。

当移动到一个新的地点$p$后，某个voxel的不确定性被更新为

$$
M^u|p(v) = exp(-\eta)M^u(v)
$$

其中$\eta \in \mathbb{R} > 0$为 在p点处观测所带来的信息量。

#### Oracle utility function

用表面栅格(surface voxels)的不确定性来刻画地图：

$$
ObsSurf(M) = \sum_{v\in Surf}(1-M^u(v))
$$

则某个位置$p$的得分函数可以刻画为

$s(M, p) = ObsSurf(M|p)-ObsSurf(M) \\
= \sum_{v\in surf}(1-exp(-\eta))M^u(v)
$

#### Learning the utility function

##### Multi-scale map representation

![](/pics/more_active/multi-scale.png)
首先截取$D_x\times D_y \times D_z$的栅格。根据八叉树地图的性质，可以很容易的这些栅格按照$2^l \times r, l\in(1, ...,L) $的分辨率降采样。然后将这些三维栅格旋转与平移到p的位置，并且依靠三次插值来将三维栅格压缩成二维栅格。因此一个多尺度的representation为$x(M, p)\in \mathbb{R}^{D_x\times D_y \times D_z \times 2L}$。其中2是表示每个尺度都有一个occupancy与一个uncertainty图。

##### ConvNet Architecture
![](/pics/more_active/Selection_001.png)

#### 总结

利用深度学习，学习nbv的评估函数。

## 12-14
- 标题：Learned Map Prediction for Enhanced Mobile Robot Exploration
- 作者单位：Simon Fraser University
- 会议: ICRA 2019

### System Overview

![](/pics/more_active/12-14-workflow.png)

### Map Completion Network

使用变分自编码器(VAE)。
![](/pics/more_active/12-14-netwokr.png)

**Encoder**使用的是resnet结构。输出一个均值$\mu$与log 方差$log(\sigma)$，尺寸均为$8\times 8\times 512$。

**Decoder**与Encoder采用的是类似的结构，但是用转置卷积。输出是一个障碍物的概率图。用阈值0.5来决定是障碍物还是free space.

损失函数为
1. 预测损失。二值交叉熵损失。
$$
l_{n}=-\left[y_{n} \cdot \log x_{n}+\left(1-y_{n}\right) \cdot \log \left(1-x_{n}\right)\right]
$$
其中$y_n$是groundtruth label, $x_n$是输出label。
则总体损失为：
$$
L_{\text {prediction}}=\sum l_{n}
$$
2. KL 散度损失。
$$
L_{k l d}=\sigma^{2}+\mu^{2}-\log (\sigma)-1
$$
该损失使得训练得到的分布接近于正态分布。
3. 总体损失
$$
L_{\text {final}}=\gamma L_{\text {prediction}}+(1-\gamma) L_{\text {kld}}
$$
其中$\gamma$在本文中取0.99。

### Map Completion Augmented Exploration

#### Information Gain Computation

首先通过dfs找到所有的边缘点聚类。则每个边缘点聚类的信息增益可以定义为
$$
\boldsymbol{I}_{t}^{i}\left(\boldsymbol{M}, x_{i}\right)=\boldsymbol{H}(\boldsymbol{M})-\boldsymbol{H}\left(\boldsymbol{M} \mid x_{i}\right)
$$
（这个很常见就不解释了）。
对于一个占据栅格地图，香农信息熵定义为：
$$
\boldsymbol{H}(\boldsymbol{M})=-\sum p\left(m_{j}\right) \log p\left(m_{j}\right)+\left(1-p\left(m_{j}\right)\right) \log \left(1-p\left(m_{j}\right)\right)
$$
（这个也很常见，也不解释了）

#### Augmenting Exploration Planners

基于[Hector exploration](http://opensource-robotics.tokyo.jp/ros.org/wiki.ros.org/hector_exploration_planner.html)与cost-utility function based planner。

1. Augmented Hector Planner: 传统的Hector Planner有两个损失，路径到frontier的距离损失与障碍物的损失。增强 的Hector Planner的损失可以表示为：
$$
\boldsymbol{C}(m)=\min _{\mathbf{F}_{i}^{i} \subseteq \mathcal{F}_{t}}\left\{\min _{f \in \mathbf{F}_{i}^{i}} \boldsymbol{C}_{p}(f)-\alpha \sqrt{\boldsymbol{I}_{t}^{i}}\right\}+\beta \boldsymbol{C}_{o}
$$

其中$C_p(f)$为当前栅格m到frontier栅格f的距离。$I_t^i$是frontier 聚类$F_t^i$的信息增益，$C_o$为当前cell m的碰撞损失。$\alpha \beta $是两个认为给定的参数。

2. Augmented Cost-Utility Planner: 
$$
\boldsymbol{C}(f)=\boldsymbol{C}_{p}(f)-\lambda \sqrt{\boldsymbol{I}(f)}
$$
其中$C_p(f)$为当前栅格m到frontier栅格f的距离。$I_t^i$是frontier 聚类$F_t^i$的信息增益。