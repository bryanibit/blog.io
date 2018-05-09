---
layout: post
title: IDS-Camera-5240-NIR
date: 2018-5-9
categories: blog
tags: [技术总结]
description: IDS camera调试技巧
---

## 安装驱动

- 下载[uEye-Linux-4.90-64.tgz](https://en.ids-imaging.com/download-ueye-lin64.html)
- 解压后得到三个文件：一个说明文件，两个.run文件，一个是适用于网口传数据的，一个是USB传输数据的，直接sudo ./*.run安装即可，安装后的Note如下：

## IDS Camera Driver Note

```
Successfully installed ueyeethd. Start (as root) with:
     /etc/init.d/ueyeethdrc start
 
By default, ueyeethd will work with all interfaces found during
installation.
To use dedicated interfaces only, edit (as root) the option:
        [Parameters] - Interfaces
at /usr/local/share/ueye/ueyeethd/ueyeethd.conf
one must stop the daemon before editing the configuration file. Alternatively,
the uEye Camera Manager may be used to configure network interfaces.
 
If ueyeethd hangs and does not terminate on rc 'stop' command, run (as root)
	/etc/init.d/ueyeethdrc stop (Normal stop)

     /etc/init.d/ueyeethdrc force-stop (Tough stop)
 
NOTE that the daemon refuses to terminate if there are clients connected!
To uninstall, run '/usr/local/share/ueye/bin/ueyed_install-eth uninstall'.
 
If a graphical desktop environment is available, one may use the
uEye Camera Manager to configure cameras and services. To start
it, type idscameramanager on the command line or in the graphical application
launcher. If idscameramanager shall be used for daemon configuration and service
control, it _must_ be started as root.
```

## Connect camera via Ethernet

- 在Ubuntu网络设置的图像界面上设置wired connection，固定ipv4地址为*192.168.11.1*，使用ids camera manager链接相机，并固定相机IP为同一个网段。子网掩码：三个255

- 此时可以打开ueyedemo（在安装驱动时一起安装了），查看官方程序得到的图像

## 自己程序读写NIR Image

[Github open source](https://github.com/ncsuarc/ids)

安装库函数后，先打开相机

```
/etc/init.d/ueyeethdrc start
```

之后可以使用以上open source的例程读取图像

