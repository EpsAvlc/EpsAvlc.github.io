---
title: "turtlebot3代码学习"
tags: SLAM navigation 
---

## move_base

### costmap_2d 
![costmap](http://wiki.ros.org/costmap_2d?action=AttachFile&do=get&target=costmap_rviz.png)

如上图所示，红色表示障碍物，蓝色表示根据设置的机器人的直径，机器人不能走过的地方，红色的多边形方框为机器人的足迹(footprint)，要想使机器人不撞上障碍物，就要保证多边形方框不在蓝色区域范围内。

## cartographer

### imu lidar calibration 
1. 　
```cpp
POSE_GRAPH.optimization_problem.log_solver_summary = true
```

  After 3D SLAM is tuned and it converges, it will print the optimized IMU to Lidar rotation and you can copy that value to URDF.

2. When performing the global optimization, Ceres tries to improve the pose between your IMU and range finding sensors. A well chosen acquisition with lots of loop closure constraints (for instance if your robot goes on a straight line and then back) can improve the quality of those corrections and become a reliable source of pose correction. You can then use Cartographer as part of your calibration process to improve the quality of your robot’s extrinsic calibration.

### 激光雷达消息的运动畸变减轻

cartographer建议激光雷达的点云以每个UDP packet的形式发布point cloud2，用以消除激光雷达的运动畸变。

### 对点云的降采样

cartographer将点云划分为一个个方格，仅保留每个方格的重心部分的点云做SLAM用。

### 时间单位

为秒(s)

### local_slam

激光雷达匹配的两个策略：

1. CeresScanMatcher:速度快，但是鲁棒性较差，并且对传感器要求比较高。
2. RealTimeCorrelativeScanMatcher:速度比较慢，但是鲁棒性比较好，适宜在除了激光雷达外其他传感器可信度不高的情况下使用。