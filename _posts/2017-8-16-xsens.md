---
layout: post
title: Xsens-710
date: 2017-8-16
categories: blog
tags: [技术总结]
description: xsens调试技巧
---

## Xsens wire

使用的线缆名称为CA-MP2-MTi，9pin--9根线，连接电源到Vin和GND，连接USB+和USB-/电源地到USB接头。

serial RS232 有问题，无论连接tx/rx/电源地或者tx/rx或者交换tx/rx都无法与Windows上官方suite软件中的MT Manager通信，所以放弃使用串口，使用自制的USB接口

## Xsens Prerequisites

1. Install the MTi USB Serial Driver

```
$ git clone https://github.com/xsens/xsens_mt.git
$ cd ~/xsens_mt
$ make
$ sudo modprobe usbserial
$ sudo insmod ./xsens_mt.ko # install module to /lib/modules/$(uname -r)/driver/usb/serial
$ sudo reboot
```

2. Run mtsdk_linux_4.8.sh in the "MT_Software_Suite_Linux_4.8"

> Download official software "MT_Software_Suite_Linux_4.8".
> Test hardware connection and communication. Enter the installation directory you like.
> To test hardware, go to installed directory "MT_software/examples/mtsdk", type "make src_cpp"; 
> run "./example", enter COM port name:/dev/ttyUSB0, baud rate: 115200
The result shows the following:

![](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/xsens_test_example.png)

3. Install gps_common

        $ sudo apt-get install ros-distro-gps-common

## Xsens driver in Ros

```
sudo chmod 777 /dev/ttyUSB0
rosrun xsens_driver mtdevice.py --configure=oe50,aa50,wr50,vv50,pl20 #publish sensor data,set as 50Hz
roslaunch xsens_driver xsens_driver.launch
```
说明：iu UTMtime， oe Euler angle， aa acceleration， wr rate of turn， pl latitudeLongitude， vv velocity

## Xsens message 格式

[MT Low Level Communication Protocol documentation (LLCP)](https://xsens.com/download/usermanual/ISM/MT_LowLevelCommunicationProtocol_Documentation.pdf)

以上文档中的page10注明了Xsens发出的信息格式详细情况

## Xsens driver程序大致流程

* 二进制流根据以上的message格式解释为对应的各种物理意义的量，并存储在一个dict中，这是在函数parse_MTData2(self, data)中实现的
* 通过函数locals()[find_handler_name(n)](o)，找到对应的函数的句柄，解析刚刚得到的数据，结果放在msgs中，最后publish出去


## 补充

- 文件的属主、属组可以通过chown命令更改。文件的权限可以通过chmod命令修改。

```
groups #查看所有group名称
users  #查看所有user名称
groupadd <group_name> #add new group
gpasswd -a <user_name> <group_name> #将用户加到组中，重启电脑生效
gpasswd -d <user_name> <group_name> #将用户从组中移除
```

* 使用groups命令查看用户所在组的名称： 

```
groups inin
```
* make sure that your $USER is in dialout group, you can use following commands:

```
sudo usermod -G dialout -a $USER
```

## Calibration (in windows)

Mti-710 needs sufficient velocity to do good heading estimation-- search north

Since the yaw of the MTi-G-700/710 is referenced by comparing GNSS acceleration with the on-board accelerometers, more movement (when GNSS is available) will result in a better yaw.

The minimal acceleration and movement for the yaw to be estimated is above 7 m/s.

In general, the more accelerations and movement will result in a better yaw. At lower speeds the yaw might be less accurate and take longer. If the velocity is lower, a good option might be to select the GeneralMag filter profile which also uses the magnetometer to stabilize the yaw.

## Xsens与差分Novatel对比

If you want to see result in detail, click [here](http://nbviewer.jupyter.org/github/bryanibit/bryanibit.github.io/blob/master/_posts/jupyter/visual.ipynb)

## Donation

**If you think this useful for you, you can donate for me. Thank you for your support!**

![weixin](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/wx.jpg) | ![zhifubao](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/zfb.jpg)


