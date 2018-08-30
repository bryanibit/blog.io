---
layout: post
title: Ladybug Driver on Ubuntu16.04
date: 2018-05-14
categories: blog
tags: [技术总结]
description: ladybug driver on linux64 ubuntu 16.04
---

## Install Official deb driver
===============================================================================

```
sudo apt-get install xsdcxx
sudo dpkg -i <ladybug5driver.deb> //download from official web
```

Keep in mind that PTGrey SDK Flycapture **CANNOT** be installed simultaneously with Ladybug SDK.

**需要修改grub图像大小，否则可以检测到设备但无法采集到图像！**

## USB RELATED NOTES
===============================================================================

On Linux systems, image size is restricted to 2MiB or less by default. To
increase this limit so that you can make use of your imaging hardware's full
capabilities, you will need to make a minor change to your system. Steps to
make the change can be found below, or at [ptgrey](https://www.ptgrey.com/tan/10685#ConfiguringUSBFS).

The USBFS buffer size is too small for 16 MB by default.

* To *temporarily* set the maximum usbfs memory limit, run this command:

        sudo modprobe usbcore usbfs_memory_mb=1000

* To *permanently* set the maximum usbfs memory limit: Open the */etc/default/grub* file in any text editor. 

Find and replace:

        GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"

with this:

        GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=1000"


* Update grub with these settings:

         sudo update-grub


* You may need to reboot.


If this method fails to set the memory limit, run the following command:

        sudo sh -c 'echo 1000 > /sys/module/usbcore/parameters/usbfs_memory_mb'


To confirm that you have successfully updated the memory limit, run the following command:

        cat /sys/module/usbcore/parameters/usbfs_memory_mb


## LADYBUG REMOVAL
===============================================================================
To uninstall:

        sudo dpkg -r ladybug

To uninstall & remove installation config files after uninstall:

        sudo dpkg -P ladybug

## 可选的安装

```
//Ubuntu 16.04
user$: sudo apt-get install libraw1394-11 libgtkmm-2.4-1v5 libglademm-2.4-1v5 libgtkglextmm-x11-1.2-dev libgtkglextmm-x11-1.2 libusb-1.0-0
```

## Driver file

[AutoWare Point Grey Camera](https://github.com/CPFL/Autoware/tree/master/ros/src/sensing/drivers/camera/packages/pointgrey)


|Parameter| Type| Description|
----------|-----|--------
|`SCALE`|*float*|Defines the downscale ratio (between 0.1 and 1.0).|
|`CalibrationFile`|*string*|Path to an Autoware-compatible calibration file to be published in the `camera_info` topic related to this camera.|


## 由驱动所想到的

LKMs(Loadable Kernal Modules) are not user space programs. They are part of the kernel and different from the base kernel module in the interest of booting. Base moudule is loaded by computer booting, otherwise, LKM is not.

* LKMs are used for six main things: 
* Device drivers. 
* Filesystem drivers(interpret the contents of a filesystem as files and dir and such. For example, there's a filesystem driver for the ext2 filesystem type used almost universally on Linux disk drives.).
* System call. 
* Network drivers. 
* TTY line disciplines(These are essentially augmentations of device drivers for terminal devices.).
* Executable interpreters. 

Loaded modules are shown in file called */proc/modules*. *lsmod* lists the currently loaded LKMs, but all it does is dump the contents of */proc/modules*.





