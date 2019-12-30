---
title: "激光雷达SLAM重定位：SegMatch与SegＭap"
tags: SLAM
---

挖的坑要及时填呀。

## 背景
户外大尺度环境下做SLAM一般是用激光雷达传感器与相机。如果直接保存点云地图，则由于户外机器人运动的范围比较大，地图的占用内存会过大。因此ETHZ-ASL实验室提出了一种基于segment的地图，能够将点云地图很好的抽象，适合于多机器人户外大范围环境的探索与建图。
<!--more-->

## 相关论文
一篇是发表在ICRA 2017的SegMatch[^1]，另一篇是基于此18年发表在RSS上的SegMap[^2]。

## SegMatch

SegMatch 解决的是激光雷达SLAM问题中的位置识别（place recognition）问题。位置识别问题常出现在回环检测中，主要是确认这个场景是否来过。

对于视觉SLAM，一般来说位置识别就用提取特征点与词袋模型来做了。不过视觉SLAM会有视角(View point)的问题。举例来说，小车从A开到B，与从B开到A，视觉看到的景象是不同的，自然也就形成不了回环。

然而激光SLAM就没有这个问题。激光雷达360扫描，视场角很好的覆盖了所有的区域。在这篇文章中，作者认为激光的位置识别主要有两种提特征的方式： local feature 与global feature。

local feature主要还是提取特征点与匹配。这样提的特征做位置识别，由于特征是局部的，因此匹配的成功率会比较低。

global feature是指对于输入的整个点云，分析点云的一些特征并抽象出来，比如得到一个关于点云的直方图，再拿直方图去匹配。这样做的缺点是，机器人的位置稍作变化，输入点云的global feature就可能不一样。即global feature的一致性比较差。

因此作者从中取舍，对点云进行分割，得到许多关于三维物体的点云，再用这些点云去完成place recognition问题。

SegMatch的流程图如下所示。

![segmatch_diagram](/assets/images/post_images/19-12-30-seg/segmatch_diagram.png)

我将详细介绍每个部分的作用。

### Segmentation


[^1]: [*SegMatch: Segment based loop-closure for 3D point clouds*](https://arxiv.org/abs/1609.07720)
[^2]: [*SegMap: 3D Segment Mapping using Data-Driven Descriptors*](https://arxiv.org/abs/1804.09557)
