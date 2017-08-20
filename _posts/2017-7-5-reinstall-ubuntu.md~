---
layout: post
title: Linux Install Tips
date: 2017-7-5
categories: blog
tags: [技术总结]
description: Linux重装系统&软件
---

##我们开始吧

Now I think I should do it, write them down and log everything. I am exciting now.

## First part for reinstall Ubuntu


### Ubuntu partition 
  
         \boot (primary)  200M
         \
         \swap  memory * 2
         \home

如果将/boot单独分区，则/boot为主分区，而/分区不必是主分区。
 
分区完成后，选择/home 作为Ubuntu安装位置
 
device for boot loader installation:
 
选择 Ubuntu安装的/boot位置

         Note that: reinstall partition should have something different from before!!

### Install Sougou pinyin


Ubuntu 14.04 install sougoupinyin


>first, download .deb install package and install, then go to language support and modify keyboard input system to fcitx and apply all system.

>go to fcitx configure (in windows and search a penguin called fcitxconfigure) and click + and cancle only show current language , at the same time find Sogou Pinyin and add.

>reboot!

### Setting github ssh

1. install git  sign up an account for github

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

>git clone git@github.com:bryanibit/pySfM.git

>git init

>touch README.md (由于选择了readme)

>git add README.md

>git commit -m 'first_commit'

>git remote add origin https://github.com/findingsea/myRepoForBlog.git

>git push -u origin master (-u is the first time to upload)

### Install Cmake 3.0 +

>sudo -E add-apt-repository -y ppa:george-edison55/cmake-3.x

>sudo -E apt-get update

>sudo apt-get install cmake

### Install meshlab

>$ sudo add-apt-repository ppa:zarquon42/meshlab

>$ sudo apt-get update

>$ sudo apt-get install meshlab

  Optional, to remove meshlab, do:

>$ sudo apt-get remove meshlab

  Or, if you want to uninstall meshlab, disable the recently added PPA and downgrade all the packages that got updated via the PPA, do:

>$ sudo apt-get install ppa-purge

>$ sudo ppa-purge ppa:zarquon42/meshlab

### Install pycharm and crack it

```
Download official version professional
go to  
- [crack](idea.qinxi1992.cn)
add “0.0.0.0 account.jetbrains.com” to /etc/hosts file
```

#### 配置pycharm使用ros

* 设置快捷键，使从快捷方式启动PyCharm的同时加载ROS环境变量

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

#### 使用pycharm时import第三方库

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
copy ros content in .bashrc to .zshrc

