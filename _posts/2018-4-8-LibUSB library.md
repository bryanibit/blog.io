---
layout: post
title: LibUSB 介绍
date: 2018-4-8
categories: blog
tags: [技术总结]
description: 今天下午临时定下要出差。已抵浙江，刚刚安顿。明天一早出发，不便多写。但会在工作之余尽量写。写不是义务，写本身就是写的报酬。

---

## 什么是LibUSB Library

[PDF下载](https://github.com/bryanibit/bryanibit.github.io/blob/master/img/doc/LibUSB_-_Create_a_Solution_Without_the_Class_Struggle.pdf)

## 如何使用LibUSB

在这个[网站](http://www.dreamincode.net/forums/topic/148707-introduction-to-using-libusb-10/)上可以查看使用该库的方法

### 下面是我认为比较深入浅出的总结（usb运行步骤）

1. All USB devices support endpoint 0 when powered up. This endpoint is the target of the default pipe. After the attachment of a device has been detected, the USBD software uses endpoint 0 to initialise the device and something else.

2. Once the endpoints of a device have been identified and configured, pipes come into existence allowing the client software to communicate with the device.  A pipe has associated with it characteristics such as a claim on bus access and bandwidth, the type of transfer, the direction of transfer and the maximum data payload size.

3. USB defines four types of transfer：control transfers, interrupt transfers (keyboard), isochronous transfers (audio) and bulk transfers (big block storage).
There are also two types of pipe: message pipes and stream pipes.Control transfers are made using message pipes. In a message pipe, the data portion of each packet has some meaning to the USB system software.

### Devices and interfaces

每个打开的device就对应一个interface（接口），接口在程序中就是一个句柄（handle）