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
## .bashrc文件失效

**Problem:** .bashrc文件无论写入什么东西似乎都没有用

**Solution:**

*原理：*在linux下，如果是bash环境，用户登录时读取设置文件的顺序是/etc/profile －－> ~/.bash_profile －－> ~/.bashrc －－> /etc/bash.bashrc。注意在~/.bash_profile这一步，如果没有~/.bash_profile ，则默认读取~/.bash_login，如果没有~/.bash_login 才读取~/.profile。

所以以上问题的解决办法是由于没有.bash_profile, .bash_login and .profile,所以.bashrc文件没有生效，（本人是由于在/home目录下执行了rm -rf *)

1. 恢复.bashrc
```
/bin/cp /etc/skel/.bashrc ~/
```
2. touch .bash_profile
复制以下内容到其中

```
\# .bash_profile

\# If .bash_profile exists, bash doesn't read .profile
if [[ -f ~/.profile ]]; then
  . ~/.profile
fi

\# If the shell is interactive and .bashrc exists, get the aliases and functions
if [[ $- == *i* && -f ~/.bashrc ]]; then
    . ~/.bashrc
fi
```

重新登录对应的账号

## ubuntu can not shutdown: nouveau problem

将nouveau加入到/etc/modprobe.d/nouveau.config的blacklist中

```
blacklist nouveau
options nouveau modeset=0
```

然后更新内核

```
sudo update-initramfs –u
```

**确认更新内核成功，没有错误标示后才能重启，否则重启后无法进入系统**

## Ubuntu 14.04安装jekyll，需要ruby --version 大于2.1

```
sudo apt-get update
sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties libffi-dev

cd
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
exec $SHELL

git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
exec $SHELL

rbenv install 2.3.1
rbenv global 2.3.1
ruby -v

echo "gem: --no-ri --no-rdoc" > ~/.gemrc ##(optional) tell Rubygems to not install local documentation

sudo apt-get remove ruby1.9.1 ## watch out
```

## 移除一个ppa

```
sudo add-apt-repository --remove ppa:...
sudo apt-get udpate
```

## 在nvidia tx2 Ubuntu16.04上 ORB-SLAM编译ros版本出错1：fatal error: Eigen/Core: No such file or directory

```
sudo ln -s /usr/include/eigen3/Eigen /usr/local/include/Eigen
```

```
unlink /usr/local/include/Eigen
update-alternatives --config <file-name>
```

if Eigen file is existed, then use -fs

unlink can remove the old file, so caution is needed.

这是由于eigen在安装后在子目录中，所以可以参考这个[链接的说明](https://askubuntu.com/questions/491067/eigen-installation-seemed-to-work-but-i-still-cant-make-eigen-work)

如果不更改可能需要将头文件的目录更改。

## 在nvidia tx2 16.10编译orb-slam ros时，出现错误2：

```
/usr/bin/ld: CMakeFiles/RGBD.dir/src/ros_rgbd.cc.o: undefined reference to symbol '_ZN5boost6system15system_categoryEv'
/usr/lib/aarch64-linux-gnu/libboost_system.so: error adding symbols: DSO missing from command line
collect2: error: ld returned 1 exit status
CMakeFiles/RGBD.dir/build.make:207: recipe for target '../RGBD' failed
make[2]: *** [../RGBD] Error 1
CMakeFiles/Makefile2:67: recipe for target 'CMakeFiles/RGBD.dir/all' failed
make[1]: *** [CMakeFiles/RGBD.dir/all] Error 2
/usr/bin/ld: CMakeFiles/Stereo.dir/src/ros_stereo.cc.o: undefined reference to symbol '_ZN5boost6system15system_categoryEv'
/usr/lib/aarch64-linux-gnu/libboost_system.so: error adding symbols: DSO missing from command line
collect2: error: ld returned 1 exit status
CMakeFiles/Stereo.dir/build.make:207: recipe for target '../Stereo' failed
make[2]: *** [../Stereo] Error 1
CMakeFiles/Makefile2:104: recipe for target 'CMakeFiles/Stereo.dir/all' failed
make[1]: *** [CMakeFiles/Stereo.dir/all] Error 2
Makefile:127: recipe for target 'all' failed
make: *** [all] Error 2
```

The problem is described as libboost_system.so找不到链接目录 

解决方案为:

locate  boost_system
//查找到目录

/usr/lib/x86_64-linux-gnu/libboost_system.a
/usr/lib/x86_64-linux-gnu/libboost_system.so
/usr/lib/x86_64-linux-gnu/libboost_system.so.1.58.0
/usr/local/MATLAB/R2017a/bin/glnxa64/libboost_system.so.1.56.0


将libboost_system.so复制到ORB_SLAM2/lib下，并且将ORBSLAM2/Examples/ROS/ORBSLAM2下的Cmakelists.txt中加入库目录，具体为在

```
set(LIBS 
${OpenCV_LIBS} 
${EIGEN3_LIBS} 
${Pangolin_LIBRARIES} 
${PROJECT_SOURCE_DIR}/../../../Thirdparty/DBoW2/lib/libDBoW2.so 
${PROJECT_SOURCE_DIR}/../../../Thirdparty/g2o/lib/libg2o.so 
${PROJECT_SOURCE_DIR}/../../../lib/libORB_SLAM2.so
```
之后加入

${PROJECT_SOURCE_DIR}/../../../lib/libboost_system.so 

问题得以解决

## 在nvidia tx2 16.10编译orb-slam ros时，出现错误3：

```
/usr/lib/gcc/aarch64-linux-gnu/5/../../../aarch64-linux-gnu/libGL.so: undefined reference to `drmGetDevices2'
/usr/lib/gcc/aarch64-linux-gnu/5/../../../aarch64-linux-gnu/libGL.so: undefined reference to `drmCloseOnce'
```

Problem Solving:
```
sudo ln -fs /usr/lib/aarch64-linux-gnu/tegra/libGL.so /usr/lib/aarch64-linux-gnu/libGL.so
```

Default situation is to use libGL.so instead of tegra’s libGL. So link the tegra’s libGL to default libGL.


## Can not mount windows disk on ubuntu

```
➜  sudo mount /dev/sdb4 /media/bryan
Windows is hibernated, refused to mount.
Failed to mount '/dev/sdb4': Operation not permitted
The NTFS partition is in an unsafe state. Please resume and shutdown
Windows fully (no hibernation or fast restarting), or mount the volume
read-only with the 'ro' mount option.
```

这是由于Windows没有正常关机而休眠，所以文件系统里会有*hiberfil.sys*的文件，其他操作系统看到这个文件中的flag，导致无法以**读写模式**挂载Windows硬盘。[问题链接](https://askubuntu.com/questions/145902/unable-to-mount-windows-ntfs-filesystem-due-to-hibernation)

1. 方法之一是使用**只读模式**挂载硬盘：
```
sudo fdisk -l
mount -t ntfs-3g -o ro /dev/sdb4 /media/bryan
```

2. 方法二是进入Windows，彻底关闭后，重新进入Linux

3. 如果一定要以读写模式挂载：删除hiberfil.sys文件，但是可能会因此丢失未保存的文件，不建议使用；对于Windows8/10，关闭快速启动（Control Panel-Power option-choose what the power buttons do-change setting that are currently unavailable-Turn on fast startup(recommended)），如果重新进入Linux仍然无法读写挂载，则以管理员身份打开Command Prompt,输入```Powercfg /h off```


## ROS Error: can't not locate node

```
cannot launch node of type [voxblox_ros/tsdf_server]: can't locate node [tsdf_server] in package [voxblox_ros]
```

When you start a launch file, you may meet the above error.

```
source ./devel/setup.zsh
```

## Huawei E353/E3131 massage storage

Create file with ```sudo vi /etc/usb_modeswitch.d/12d1:1f01```.

With following content

root@fwhlin:~ # cat /etc/usb_modeswitch.d/12d1:1f01

```
# Huawei E3531s-2 - switch to modem mode instead of HiLink CDC-Ether mode
TargetVendor=0x12d1
TargetProduct=0x1f01
# switch to 12d1:1001 (modem mode, 3 virtual serial ports)
#MessageContent="55534243123456780000000000000011062000000100000000000000000000"
# switch to 12d1:14dc (default HiLink CDC-Ether mode)
MessageContent="55534243123456780000000000000a11062000000000000100000000000000"
# switch to 12d1:1442 (debug mode with 2 virtual serial ports)
# MessageContent="55534243000000000000000000000011060000000000000000000000000000"
```

To switch to Modem Mode, run ```usb_modeswitch -I -W -c /etc/usb_modeswitch.d/12d1\:1f01```.

If it worked, device will change to modem mode

```
root@fwhlin:~ # lsusb | grep Hu
Bus 009 Device 003: ID 12d1:14dc Huawei Technologies Co., Ltd. 
```

Notice it changed name, also device ID changed to 14db, or something else.
