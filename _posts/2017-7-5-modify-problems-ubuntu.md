---
layout: post
title: Ubuntu unmet problems
date: 2017-7-5
categories: blog
tags: [技术总结]
description: Linux中的一些问题，总结一下
---


## build-essential

### problem

- sudo apt-get install build-essential
- Reading package lists... Done
- Building dependency tree       
- Reading state information... Done
- Some packages could not be installed. This may mean that you have
- requested an impossible situation or if you are using the unstable
- distribution that some required packages have not yet been created
- or been moved out of Incoming.
- The following information may help to resolve the situation:

- The following packages have unmet dependencies:
-  build-essential : Depends: dpkg-dev (>= 1.13.5) but it is not going to be installed
- E: Unable to correct problems, you have held broken packages.

### Solution

* Run sudo apt-get install libdpkg-perl=1.17.5ubuntu5 to downgrade libdpkg-perl.
* You somehow installed a newer version of the package than what is in the repositories, which is causing problems with other packages.

## clean apt-get cache

        sudo apt-get clean
        cd /var/lib/apt
        sudo mv lists lists.old
        sudo mkdir -p lists/partial
        sudo apt-get clean 
        sudo apt-get update

## uninstall sublime from terminal
```
sudo rm -r /opt/Sublime\ Text\ 2
sudo rm /usr/bin/sublime
sudo rm /usr/share/applications/sublime.desktop
sudo sed -i 's/sublime\.desktop/gedit.desktop/g' /usr/share/applications/defaults.list
```

## Firefox reminder: running and not responding

Firefox is already running, but is not responding. To open a new window, you must first close the existing Firefox process, or restart your system.

**solution:** 
           pkill firefox
           cd /home/inin/.mozilla/firefox/×××××.default
           rm .parentlock
## Upgrade to Ubuntu 16.04 /boot空间不足问题

1. df -h （查看Ubuntu的文件系统 使用情况） 
2. uname -a (查看当前使用的内核版本) 
3. sudo apt-get remove linux-image- （按两次tab键） 
4. sudo apt-get remove linux-image-4.4.0-31-generic（再重复两次删除36,38）(删除多余内核) 
5. 再查看下内核和磁盘容量，发现释放了很多空间。 

- 最后如果出现了这个警告：
- The link /vmlinuz.old is a damaged link
- Removing symbolic link vmlinuz.old
- you may need to re-run your boot loader[grub]
- The link /initrd.img.old is a damaged link
- Removing symbolic link initrd.img.old 
**solution:**
         sudo /usr/sbin/update-grub

## install ros an error code
dpkg: error processing package ros-kinetic-desktop-full (--configure):
 dependency problems - leaving unconfigured
No apport report written because MaxReports is reached already
                                                              Processing triggers for libc-bin (2.23-0ubuntu9) ...
Errors were encountered while processing:
 libopenni0
 libopenni-dev
 openni-utils
 libopenni-sensor-pointclouds0
 libpcl-io1.7:amd64
 libpcl-visualization1.7:amd64
 libpcl1.7
 libpcl-dev
 ros-kinetic-pcl-conversions
 ros-kinetic-pcl-ros
 ros-kinetic-perception-pcl
 ros-kinetic-perception
 ros-kinetic-desktop-full
E: Sub-process /usr/bin/dpkg returned an error code (1)

**solution:**

          cd /var/lib/dpkg
	  sudo mv info info.bak
	  sudo mkdir info

重新sudo apt-get install ××××××

## Apport错误提示，要求发送错误报告

```
ls /var/crash
sudo rm -rf /var/crash/*
sudo reboot

如果还有该错误提示

sudo /etc/default/apport
找到 enabled=1 这一行，并改变到0(zero)
```


