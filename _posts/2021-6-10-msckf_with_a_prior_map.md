---
title: "具有先验地图约束的MSCKF"
tags: SLAM 
---

## 背景

阅读Xingxing Zuo论文的过程中的一些公式的推导。

<!--more-->

## 论文

Visual-Inertial Localization With Prior LiDAR Map Constraints

## State Vector

$$
x_{k} = [^{I_{k}}_{G}\overline{q}^\top\ b_\omega ^\top\ ^Gv_{I_{k}}^\top \ b_{a}^\top\ ^Gp_{I_{k}}^\top\ ^M_G\overline{q}^\top\ ^Gp_M ^\top\ x_C ^\top]
$$ 

相对于传统MSCKF，多估计了两个状态$^M_G\overline{q}^\top$与$^Gp_{M}^\top$, 分别表示激光雷达点云自身坐标系的原点在IMU坐标系下的旋转与平移。

## State Update

除了正常的相机的每帧读入时会有个位姿估计以及相应的状态更新外，这里还多一个相机稠密重建的点云与prior map进行匹配时的一个观测。

设匹配得到相机在prior map的坐标系下的坐标为$^{C_k}_MR, ^Mp_{C_k}$，则有

$$
\begin{aligned}
^{C_{k}}_MR &= ^C_IR\ ^{I_{k}}_GR ^{G}_{M}R\ \\
^{M}p_{C_k} &= ^{C}_{M}R ^\top(^Gp_{I_k}-^Gp_{M} - ^{I_k}_{G}R ^\top\ ^{C}_{I}R ^\top\ ^C p_{I}) \\
\end{aligned}
$$ 

写成观测方程的形式，有
$$
\begin{aligned}
z &= h(x_{k})+n_k\\
&\simeq h(\overline{x}_{k}) + H_{x}\tilde{x}_k+n_k
\end{aligned}
$$ 

其中，
$$
\overline{x}_{k} = [^{C_k}_{M}\overline{q}^\top, ^Mp_{C_{k}}]
$$ 
对应的error state为
$$
\tilde{x}_{k} = [^{C_k}{\delta\theta}_M, ^M {\delta p}_{C_{k}}]
$$ 

因为
$$
\begin{aligned}
\exp([^{C_k}{\delta\theta}_M]_\times)^{C_{k}}_M{R}&= ^C_IR\exp ( [^{I_k}{\delta\theta}_G]_\times)\ ^{I_{k}}_G{R} ^{G}_{M}{R} \\
\implies \exp([^{C_k}{\delta\theta}_M]_\times) &=  ^C_IR\exp ( [^{I_k}{\delta\theta}_G]_\times)\ ^I_CR\\
\implies \exp([^{C_k}{\delta\theta}_M]_\times) &= \exp ( [^C_IR^{I_k}{\delta\theta}_G]_\times) \\
\implies ^{C_k}{\delta\theta}_M &= ^C_IR^{I_k}{\delta\theta}_G \\
\implies \frac{\partial ^{C_k}{\delta\theta}_M}{\partial ^{I_k}{\delta\theta}_G} &= \ ^{C}_{I}R
\end{aligned}
$$ 
以及
$$
\begin{aligned}
\exp([^{C_k}{\delta\theta}_M]_\times)^{C_{k}}_M{R}&= ^C_IR\ ^{I_{k}}_G{R} (\exp ( [^{M}{\delta\theta}_G]_\times)^{M}_{G}{R} )^\top\\
\implies \exp([^{C_k}{\delta\theta}_M]_\times)^{C_{k}}_M{R} &=   ^C_IR\ ^{I_{k}}_G{R}^G_MR \exp ( -[^{M}{\delta\theta}_G]_\times)\\
\implies \exp([^{C_k}{\delta\theta}_M]_\times) &= ^{C_{k}}_MR \exp ( -[^{M}{\delta\theta}_G]_\times) ^{M}_{C_{k}}R \\
\implies ^{C_k}{\delta\theta}_M  &= - ^{C_{k}}_MR ^{M}{\delta\theta}_G\\
\implies \frac{\partial ^{C_k}{\delta\theta}_M}{\partial ^{M}{\delta\theta}_G} &= -\ ^{C_k}_{M}R
\end{aligned}
$$ 
这里推得的公式与论文中不太一样。

如果要推得和论文中一样的公式，应该为如下推导：
$$
\begin{aligned}
\exp([^{C_k}{\delta\theta}_M]_\times)^{C_{k}}_M{R}&= ^C_IR\ ^{I_{k}}_G{R} \exp ( [^{M}{\delta\theta}_G]_\times)^{G}_{M}{R} \\
\implies \exp([^{C_k}{\delta\theta}_M]_\times) &=\ ^{C_{k}}_{G}R \exp ( [^{M}{\delta\theta}_G]_\times) ^{G}_{C_{k}}R\\
\implies \exp([^{C_k}{\delta\theta}_M]_\times) &= \exp ( [\ ^{C_{k}}_{G}R^{M}{\delta\theta}_G]_\times) \\
\implies ^{C_k}{\delta\theta}_M &= ^{C_{k}}_{G}R^{M}{\delta\theta}_G \\
\implies \frac{\partial ^{C_k}{\delta\theta}_M}{\partial ^{M}{\delta\theta}_G} &= \ ^{C_k}_{G}R

\end{aligned}
$$ 
