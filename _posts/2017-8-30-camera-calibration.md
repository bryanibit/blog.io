---
layout: post
title: Camera Calibration
date: 2017-8-30
categories: blog
tags: [技术总结]
description: 相机标定
---

# 相机标定 ==目前使用的最好的方法是matlab自带的标定工具== ==最关键的一步是在拍摄图片的时候尽可能的让标定板覆盖相机所有的视角，同时标定板也需要多个自由度的旋转(特别是长焦镜头！)==

双目标定：

有两个选择，使用matlab自带的标定软件或者运行标定脚本。

一、使用matlab自带的标定软件

   1.在命令行窗口中输入 stereoCameraCalibrator 。然后点击左上角的Add Images，添加左右相机的图片，并且确定好board square，即棋盘格的一个格子长度，单位为mm
![](./image/image1.png)

   2.点击确定以后程序会自动开始检测角点，等到其运行完成开始标定。    标定之前可以点击Optimization Options填进去初始化参数，然后点击Calibrate 即可开始标定，得到如下图所示：
![](./image/image2.png)

   3.将误差大的图片去掉重新标定。拖拽红线到可以接受的误差值，然后在左侧选定的图片上右击执行remove and recalibrate。    在 Overall Mean Error 0.5以下的时候，标定基本上是成功的，点击Export Camera Parameters即可把参数导入到matlab的工作区中。    随后在工作区中保存stereoParams到本地，即生成了参数的mat文件。

二、运行标定脚本

  执行stereocalibrate.m
  命令：

              stereocalibrate [图片地址] [board size]

  PS: 脚本没有第一种方法的交互式方便。不建议使用

三、标定注意点

  1、标定使用的图片和需要校正的图片的分辨率一定要一致   2、初始化参数最好加上,distort设为0即可
单目标定

  在命令行窗口输入cameraCalibrator   后续操作基本和上面一致

# 相机校正

一、参数校正

  使用标定得到的相机参数进行校正。

                 python  rectify.py  [待校正图片]  [相机参数.mat]

二、特征点校正。

  如果直接校正出来的图片有问题，可以使用特征点进行自动校正

 ./autorectify  [待校正图片.list]

# TO DO by qingyu.sui  完善代码


# 一个简单易用的矫正图像及获得内参矩阵的开源代码

[OpenDroneMap](https://github.com/bryanibit/CameraCalibration)

这个项目包含两部分，无需安装exiftool，运行opencv_calibrate.py，得到两个文件，一个是内参矩阵，一个是相机畸变矩阵参数

运行distortion可以将图像去畸变
------------------

