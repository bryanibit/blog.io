---
layout: post
title: Cross Obstables
date: 2018-09-19
categories: blog
tags: [个人总结]
description: Thoughts about the game in Sep. 2018
---

**浮生如梦**

## All in all

In words, control of the car is not accurate, planning of the car is not complete, and perspection of the car is not applicable. 

First of all, the control algorithm or excutive agencies may have latency problems which result from vehicle classis. And then, planning module lacks global planning, contains only local planning. The current methods of planning is solely suitable for closed road environment and not sparse RNDF. Both of them are missing in this game, so the game has not started but has finished. The failure is destined. Finally, the behavior of hitting trees is abnormal for UVCs, for which perspection of the car is responbible. It tells us that it is not sensible to depend on sole sensor giving up multi-sensor fusion. For detecting accessible areas of cars, which is so important that the car moving relys heavily on, at least two different sensors produce accessible areas. At the same time, different algorithms are used to the same sensors to realize different results. Multi-source sensors fusion results in sophisticated system integration. That makes us need more time to consume others' algorithm. It proves that system integration is not a simple problem of "1+1". 

When results of multi-source sensors conflict with each other, how to fuse them to unified and wonderful result better than unique sensor. Or we can do fusion to a deeper degree, for example, fuse lasers and cameras data and then do clustering or segmentation.

## In future

1. Add global planning module. Replace current text saving RNDF with GUI and third part API. The tool needs to manipulate map data quickly. Especially, it needs contain map mathcing for GPS points, such as QGIS.

2. I should be familar with Kalman Filter from functions in OpenCV. KF is useful in multi-frame detection and tracking.

3. The final problem is Scene Understanding for UVCs. It may come true via fusing multi-cameras even add laser data. I have no thoughts for now.
