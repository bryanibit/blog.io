---
layout: post
title: Ladybug linux64 driver
date: 2018-05-14
categories: blog
tags: [技术总结]
description: ladybug driver on linux64 ubuntu
---

## Install Official deb driver
===============================================================================

```
sudo apt-get install xsdcxx
sudo dpkg -i <ladybug5driver.deb> //download from official web
```

## USB RELATED NOTES
===============================================================================

On Linux systems, image size is restricted to 2MiB or less by default. To
increase this limit so that you can make use of your imaging hardware's full
capabilities, you will need to make a minor change to your system. Steps to
make the change can be found below, or at 
<https://www.ptgrey.com/tan/10685#ConfiguringUSBFS>


1. Open the /etc/default/grub file in any text editor. Find and replace:

        GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"

with this:

        GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=1000"


2. Update grub with these settings:

         sudo update-grub


3. You may need to reboot.


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
