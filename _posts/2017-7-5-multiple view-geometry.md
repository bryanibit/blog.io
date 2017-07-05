---
layout: post
title: Multiview Geometry
date: 2017-7-5
categories: blog
tags: [理论梳理]
description: SfM中的多视图问题
---

## 奇异值分解(SVD)原理详解及推导

[转](http://blog.sina.com.cn/s/blog_3f738ee00102val0.html)

##　将相机中心转换到世界坐标系下
[转stackexchage](https://stackoverflow.com/questions/2403886/camera-translation-vector-relation-to-rotation-matrix)

I'm assuming that the R (rotation matrix) and t (the translation vector) you obtained were w.r.t a world coordinate system with (0,0,0) as the origin.

With R and t you can now move a point from the world coordinate system (WC) to the camera coordinate system (CC), i.e. Xc = RX + t where X is a 3D point in WC and Xc is X in CC (i.e. seen from the camera's point of view). This is applicable assuming we're dealing with rigid bodies so we just rotate the point and then translate it.

>Now, you need to find the coordinates of the camera center which is the origin of CC, or when Xc = 0:
>0 = RC + t where C is the 3D coordinates of the camera center in WC. By solving for C we get,
>C = -R-1t

And by the way,A correction in your documentation
Transposing and multiplying the rotation matrix does not change the rotation matrix --- a rotation matrix is orthogonal which means it's transpose is equal to its inverse and therefore, (RT)-1 = R.

