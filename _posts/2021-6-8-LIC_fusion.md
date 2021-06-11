---
title: "LIC-fusion 详细推导"
tags: SLAM
---

## 背景

阅读LIC-fusion论文的过程中的一些公式的推导。

<!--more-->

## State Vector

$$
x = [x_I^\top, x_{\text{calib\_C}}^\top, x_{\text{calib\_L}}^\top, x_C^\top, x_L^\top]   
$$ 
where
$$
\begin{aligned}
x_{I} &= [^{I_{k}}_G \overline{q}^\top,b_{g}^\top,^G v_{I_{k}}^\top,b_{a}^\top,^Gp_{I_{k}}^\top] \\
x_C &= [^{I_{a_{1}}}_G\overline{q}^\top, ^Gp_{I_{a_{1}}}^\top \cdots ^{I_{a_{m}}}_G\overline{q}^\top, ^Gp_{I_{a_{m}}}^\top] \\
x_L &= [^{I_{b_{1}}}_G\overline{q}^\top, ^Gp_{I_{b_{1}}}^\top \cdots ^{I_{b_{n}}}_G\overline{q}^\top, ^Gp_{I_{b_{n}}}^\top] \\
\end{aligned}
$$ 

注意到，在LIC Fusion的状态向量的定义中，相机与激光雷达的状态不再是相机与激光雷达的旋转与平移，而是其自身时间戳下的IMU的旋转与平移。

## State Propagation

以新来一帧激光雷达的数据为例：
New cloned LiDAR state estimate:

$$
\hat{x}_{L_k}(\hat{t}_{I_k}) = [^{I_k}_G \hat{\overline{q}}(\hat{t}_{I_K})^\top, ^G\hat{p}_{I_k}(\hat{t}_{I_k})^\top]
$$ 

The augmented covariance matrix is:

$$
\begin{bmatrix} P(\hat{t}_{I_{k}}) && P(\hat{t}_{I_{k}})J_{I_{k}}(\hat{t}_{I_{k}})^\top \\
                J_{I_{k}}(\hat{t}_{I_{k}})P(\hat{t}_{I_{k}}) && J_{I_{k}}(\hat{t}_{I_{k}})P(\hat{t}_{I_{k}})J_{I_{k}}(\hat{t}_{I_{k}})^\top)
\end{bmatrix}
$$ 

where $J_{I_{k}}(\hat{t}_{I_{k}})$ is the jacobian of the new cloned $\hat{x}_{L_{k}}(\hat{t}_{I_{k}})$ with respect to the current state.

$$
J_{I_{k}} (\hat{t}_{I_{k}}) = \frac{\partial \delta x_{L_{k}} (\hat{t}_{I_{k}})}{\partial \delta x} =[J_I, J_{\text{calib\_C}}, J_{\text{calib\_L}}, J_{C}, J_L]
$$ 

### derivation of $J_I$ 

因为Lidar的state vector就是自身时间戳的IMU的位姿，显然有:

$$
J_{I} = \begin{bmatrix} I_{3\times3} && 0_{3\times 9} && 0_{3\times 3} \\
0_{3\times 3} && 0_{3\times_9} && I_{3\times 3}
\end{bmatrix} 
$$ 

### derivation of $J_{\text{calib\_C}}$,$J_C$,$J_L$

Lidar 的state vector相对于相机的外参以及其他时刻的相机与雷达的state无关，故都设为0。

### derivation of $J_{\text{calib\_L}}$
$$
\begin{aligned}
q(t_{L} + \hat{t}_{dL} + \tilde{t}_{dL}) &=  \Omega(\hat{\omega}({t_L+\hat{t}_{dL}})\tilde{t}_{dL}) \oplus q(t_{L} + \hat{t}_{dL})\\

\end{aligned}
$$

$$
\begin{aligned}
p(t_{L} + \hat{t}_{dL} + \tilde{t}_{dL}) &= p(t_{L} + \hat{t}_{dL}) +\hat{v}({t_L+\hat{t}_{dL}})\tilde{t}_{dL} \\
\implies \hat{p} + \tilde{p} &=   \hat{p} + \hat{v}({t_L+\hat{t}_{dL}})\tilde{t}_{dL} \\
\implies \tilde{p} &=  \hat{v}({t_L+\hat{t}_{dL}})\tilde{t}_{dL}\\
\end{aligned}
$$ 

therefore




