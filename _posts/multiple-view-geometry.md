---
layout: post
title: Multiview Geometry
date: 2017-7-5
categories: blog
tags: [理论梳理]
description: SfM中的多视图原理
---

# 奇异值分解(SVD)原理详解及推导

[转](http://blog.sina.com.cn/s/blog_3f738ee00102val0.html)

#　将相机中心转换到世界坐标系下
[转stackexchage](https://stackoverflow.com/questions/2403886/camera-translation-vector-relation-to-rotation-matrix)

I'm assuming that the R (rotation matrix) and t (the translation vector) you obtained were w.r.t a world coordinate system with (0,0,0) as the origin.

With R and t you can now move a point from the world coordinate system (WC) to the camera coordinate system (CC), i.e. Xc = RX + t where X is a 3D point in WC and Xc is X in CC (i.e. seen from the camera's point of view). This is applicable assuming we're dealing with rigid bodies so we just rotate the point and then translate it.

>Now, you need to find the coordinates of the camera center which is the origin of CC, or when Xc = 0:
>0 = RC + t where C is the 3D coordinates of the camera center in WC. By solving for C we get,
>C = -R-1t

And by the way,A correction in your documentation
Transposing and multiplying the rotation matrix does not change the rotation matrix --- a rotation matrix is orthogonal which means it's transpose is equal to its inverse and therefore, (RT)-1 = R.


# Local Feature and Global Feature

DoG: Difference of Gaussian
HoG: Histogram of oriented gradient

- Global descriptors generally used in image retrieval, object detection and classification,
- while the local descriptors used for object recognition/identification.
- contour representations, shape descriptors, and texture features and local features represent the texture in an image patch.
- Shape Matrices, Invariant Moments (Hu, Zerinke), Histogram Oriented Gradients (HOG) and Co-HOG are some examples of global descriptors.
- SIFT, SURF and FREAK are some examples of local descriptors.

**SIFT**: 图像在不同scale上，（不同的scale就是不同的octave，每个octave中图像的长宽是相同的，但是所用的高斯核大小不同）与不同大小的高斯核函数卷积，这样得到高斯模糊图像，相邻的高斯模糊图像做差，得到DoG空间下的结果，查找极值即可。

# SfM中的关键计算总结

## 恢复运动中的R，t

如果要从2D特征点之间的匹配来恢复运动，需要用到本质矩阵E。一开始分解E求Rt用的是8点算法，就是用8个点的方程把每个E矩阵中的值都算出来。

虽然8点算法可以处理标定或者没标定的相机，但是无法处理8点共面的情况。后来Nister放了大招研发出了5点算法，可以解决共面问题，

但是相机必须是标定的，因为它用到归一化的特征坐标。分解E能得到四个Rt,根据圣经multiple view geometry，只有一个解能保证三角定位后的特征点都在两个相机前面，

据此可以去掉三个解。最后要注意保持尺度的一致性，可以三角定位得到3D点，然后保证它们之间的距离是一样的。不过单目相机算出来的尺度不确定，

就是说不知道它的单位是cm，mm还是m。

## 3D-3D匹配

使用双目相机可以得到每一张图像中场景的深度信息，这样直接优化Xk-TkXk-1就可以了，Xk和Xk-1是两坨3D点，Tk是它们之间的运动。最少需要3个点就可以算出Tk，而且尺度已经是真实尺度了，以米为单位。

## 3D-2D匹配

Nister大牛曾经说过，**3D-2D匹配比3D-3D匹配更精确，因为它优化了反投影误差，而不是3D位置的误差，三角定位得到的3D点深度信息是很不精确的**。

换句话说就是优化Pk - Pk-1，Pk-1是Xk-1根据Tk投影回图像中得到的，这个问题就是传说中的PnP问题。最少只要3个点就能算出姿态，就是所谓的P3P。P3P比5点算法更高效，

因为它用的点数更少。总结来说，对于第一第二张图像，首先找2D点的匹配，然后进行运动估计，之后三角定位出3D点。这些2D特征被跟踪到新的图像中，

直接用已知3D点和对应2D点算出新的姿态，然后把新来的3D点加入到地图中。这里有个很重要的问题就是三角定位的准确性。三角定位依靠两条线的交点，

如果两张图像的视角很近，那定位就不准，因为一条线稍微动一下交点就不知道跑到哪里去了。所以可以等一等，等到运动足够大的时候再定位，这就是所谓的关键帧选取。

# Feature检测

SIFT用的是difference of Gaussian。第二步是抑制极大值，只选取那些极值局部的最大值。为了保持尺度不变，就要在缩放的图像中检测特征点。

一个很好的特征点检测器是SIFT，**它把缩放过的图像与DoG算子做卷积，**然后在每个DoG图像中，以及不同尺度中寻找极值。

SURF用box filter来近似Gaussian，用integral image来加快计算,所以比SIFT更快。

检测除了特征点，就可以根据它周围的区域生成一个特征点描述子。最简单粗暴的方法就是用像素，然后用SSD或者NCC来比较图像块的差异。

稍微高大上一点的就是做个census transform，把每个像素和中间像素的值比较形成一个二值描述子，然后用Hamming距离计算差异。

通常局部的区域不能很好的表达特征点，因为一旦视角或者尺度变化了之后局部特征也会跟着改变。

相对于SSD和NCC，SIFT描述子的过人之处在于它其实就是局部梯度方向的直方图。特征点周围的图像块被分成几个小格子，

在每个格子中用直方图统计梯度方向。把这些直方图怼在一起就形成了128维度的描述子。

mutual consistency check第一幅图像的一个特征点对应第二幅图像的哪个特征点。

**只有当最佳匹配的描述子距离/第二好的匹配描述子距离＜一个值的时候**，才认为最佳匹配是对的，因为第二好的匹配距离非常大。

# 回环检测

特征匹配也可以用在回环检测当中。常用的方法是构建很多visual word来表示一个图像，然后对比两个图像的visual word的直方图来判断它们之间的距离。

具体来说，首先要把描述子，比如SIFT，用k means分成很多聚类，每个类就是一个visual vocabulary。一个类里面所有的描述子都有同一个visual word值，就是一个索引值。

# 过滤特征点

一般就是计算点到epipolar line的距离，或者它的近似值**Sampson distance( a first-order approximation to the geometric error)**。把E分解成R, t需要的点越少，RANSAC需要的循环次数就越少。

E的分解常用的有8点算法和5点算法。8点算法要求所有点不能都共面，5点算法可以处理共面的情况。所以5点算法更适合RANSAC。5点算法最少只需要5个点来计算相机运动。

# ORB-SLAM

http://shanmo.github.io/2016/05/ORB-SLAM

# Donation

**If you think this useful for you, you can donate for me. Thank you for your support!**

![weixin](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/wx.jpg) | ![zhifubao](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/zfb.jpg)


