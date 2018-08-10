---
layout: post
title: Ubuntu Install Tips
date: 2017-7-5
categories: blog
tags: [技术总结]
description: Linux重装系统&软件
---

## First part for reinstall Ubuntu


### Ubuntu partition

         \boot (primary)  300M
         \
         \swap  memory * 2
         \home

如果将/boot单独分区，则/boot为主分区，而/分区不必是主分区。

分区完成后，选择/home 作为Ubuntu安装位置

device for boot loader installation:

选择 Ubuntu安装的/boot位置

         Note that: reinstall partition should have something different from before!!


### 如果需要安装系统的电脑是dell-Inspiron-7559

当安装完Windows后，确认安全启动和快速启动关闭，进入Ubuntu安装盘，如果长时间没有进入，卡在Ubuntu这几个字的紫色背景页面，请跳转到以下链接

[connorkuehl](https://connorkuehl.github.io/dell-inspiron-7559-linux-guide/index.html)

> Highlight the Install Ubuntu entry and press e on your keyboard.
> Modify the line that contains quiet splash at the end and change it to:

```
nomodeset i915_bpo.nomodeset=1 quiet splash
```
这是为了关闭显卡，使用BIOS安装功能，这是由于电脑本身的缺陷

> Press F10 to boot.

> Proceed with the installation as normal.

> Reboot.

开机后如果无法关机，请按照【Ubuntu unmet problem】显卡安装的方法，安装显卡驱动，或者参考以上链接

Now I think I should do it, write them down and log everything. I am exciting now.

### Install Sougou pinyin


Ubuntu 14.04 install sougoupinyin


> First, download .deb install package and install, then go to language support and modify keyboard input system to fcitx and apply all system.

> Go to fcitx configure (in windows and search a penguin called fcitxconfigure) and click + and cancle only show current language , at the same time find Sogou Pinyin and add.

> Reboot!

### SSH Installation

```sudo apt-get install openssh-server```后，可以使用ssh连接其他电脑，即```ssh bryan@192.168.1.254```.

### Setting github ssh

1. Install git | Sign up an account for github

2. 配置Name和Email

        命令格式：    git config --global user.name "your name"
                      git config --global user.email "your email address"

3. 成Public/Private RSA Key

        命令格式：    ssh-keygen -C "your email address" -t rsa

 注意图中红色数字标注：

        1、设置Public RSA Key的保存位置，直接回车采用默认地址；
        2、设置一个密码，并再次输入确认(这里不建议设置，方便本地使用)
        3、Public RSA Key的保存路径：c:\users\username\.ssh\id_rsa.pub(windows) /home/inin/.ssh/id_rsa.pub(linux)

4. 将Public Key告知Github

        请首先注册一个github账号，Home Page：https://github.com/ 。然后进入Account Settings页面，打开SSH Keys，
        点击“Add SSH Key”。打开c:\users\username\.ssh\id_rsa.pub，把里面的内容全部Copy到Key对应的输入框内，点击“Add Key”。

5. Now you can try push a project now:

在 github.com 上

>new repository (选add readme）

在本地文件夹下

```
git clone git@github.com:bryanibit/pySfM.git
git init
touch README.md (由于选择了readme)
git add README.md
git commit -m 'first_commit'
git remote add origin https://github.com/findingsea/myRepoForBlog.git
git push -u origin master (-u is the first time to upload)
```

### Install Cmake 3.0 +

```
sudo -E add-apt-repository -y ppa:george-edison55/cmake-3.x
sudo -E apt-get update
sudo apt-get install cmake
```

### Install meshlab

```
$ sudo add-apt-repository ppa:zarquon42/meshlab
$ sudo apt-get update
$ sudo apt-get install meshlab
```

  Optional, to remove meshlab, do:

```
$ sudo apt-get remove meshlab
```

Or, if you want to uninstall meshlab, disable the recently added PPA and downgrade all the packages that got updated via the PPA, do:

```
$ sudo apt-get install ppa-purge
$ sudo ppa-purge ppa:zarquon42/meshlab
```

### Install pycharm and crack it

Install from PPA

```
sudo add-apt-repository ppa:mystic-mirage/pycharm
sudo apt-get update
sudo apt-get install pycharm  ## professional version
```

* Download official version professional
* go to [crack](http://idea.lanyus.com/)
* add “0.0.0.0 account.jetbrains.com” to /etc/hosts file

**1. Active server:** http://www.activejetbrains.gq (2017.3.4以上紧急车道 其他车辆请避让)

**2.激活server：** http://idea.imsxm.com/

```
select 'for all users'
PATH=$PATH:/home/inin/{installation path}/bin
```

#### 配置pycharm/clion使用ros

设置快捷键，使从快捷方式启动PyCharm的同时加载ROS环境变量

```
gedit ~/.local/share/applications/jetbrains-pycharm.desktop   #如果选择安装为当前用户可用
```

或者

```
gedit /usr/share/applications/jetbrains-pycharm.desktop       #如果选择为全部用户可用
```

修改Exec变量一行，在=后添加 bash -i -c 即改为（若为zsh，则改为zsh)

```
Exec= bash -i -c "/home/ubu/tools/pycharm-professional-2016.2.3/bin/pycharm.sh" %f
```

在clion中 Opening catkin packages (remember to source devel/setup.bash)

File > Open Select the src/CMakeLists.txt (```catkin_init_workspace``` produces cmakelists.txt and cp it into your catkin_ws) 
and open as Project (or open the folder containing CMakeLists.txt)

在clion调试中include经常找不到生成的自定义message，在clion-file-settings-build-CMake中，在CMake options内填入

```
-DCATKIN_DEVEL_PREFIX=/home/bryan/catkin_ws/devel
-DCMAKE_INSTALL_PREFIX=/home/bryan/catkin_ws/install
```

选择build目录为*../build*.

#### 使用pycharm时import第三方库

Pycharm 和 Clion 2018.1 版本下载地址 (链接: https://pan.baidu.com/s/1mniZLPgLqr9ViwfM7QfBGA 密码: bqwc)

```
settings--project interpreter--more(the bottom of add local)--show path for the selected interpreter-add+--/home/inin/OpenDroneMap/Superbuild/install/python2.7/dist-package
```

### install zsh

```
sudo apt-get install zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
chsh -s /bin/zsh
sudo reboot
```
Copy ros content in .bashrc to .zshrc

### install vim

```
sudo apt-get install vim
```

Renew a file called .vimrc:

```
set nu
set confirm
set mouse=a
set tabstop=4
set cindent
syntax on
```

## 安装NVIDIA驱动方法

卸载可能存在的旧版本 nvidia 驱动（对没有安装过 nvidia 驱动的主机，这步可以省略，但推荐执行，无害）

         sudo apt-get remove --purge nvidia*

安装驱动可能需要的依赖(可选)

         sudo apt-get update

         sudo apt-get install dkms build-essential linux-headers-generic

把 nouveau 驱动加入黑名单

         sudo vim /etc/modprobe.d/blacklist-nouveau.conf

  在文件 blacklist-nouveau.conf 中加入如下内容：
  
```
  blacklist nouveau
  options nouveau modeset=0
  blacklist lbm-nouveau
  alias nouveau off
  alias lbm-nouveau off
```

```sudo update-initramfs -u```重启后再次进入字符终端界面，并关闭图形界面

          sudo service lightdm stop

###(Thinkpad T480/T470P)

安装上面的教程将nouveau关闭,然后按照following:

```
$ sudo add-apt-repository ppa:graphics-drivers/ppa
$ sudo apt update (re-run if any warning/error messages)
$ sudo apt-get install nvidia- (press tab to see latest). 384 (do not use 378, may cause login loops) ## 查找对应的驱动型号
```

###(Dell 7557)

1. 去官网下载驱动后，给驱动run文件赋予执行权限：

        sudo chmod +x NVIDIA-Linux-x86_64-384.59.run

2. 后面的参数非常重要，不可省略：

        sudo ./NVIDIA-Linux-x86_64-384.59.run –no-opengl-files 
        //no-opengl-files表示只安装驱动文件，不安装OpenGL文件，避免login loop。

3. 安装cuda

        sudo ./cuda_8.0.61_375.26_linux.run --no-opengl-libs

