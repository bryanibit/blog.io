---
layout: post
title: Linux Small Tools
date: 2017-8-22
categories: blog
tags: [技术总结]
description: linux小工具
---

## install vim

```
sudo apt-get install vim
```

renew a file called .vimrc:

```
set nu
set confirm
set mouse=a
set tabstop=4
set cindent
syntax on
```

In insert mode, other clipboard content could add to vim with CTRL-V. At this time, the format or indent could be messed up.

Inputting **=G** in normal mode could or may solve the problem. The first view of openning vim is mormal mode.

Inputting **:** will enter command mode. The **V** can enter Visual mode, alse **i** is insert mode.

## install terminator

```
sudo apt-get install terminator
sudo apt-get install dconf-tools
gsettings set org.gnome.desktop.default-applications.terminal exec   /usr/bin/terminator
gsettings set org.gnome.desktop.default-applications.terminal exec-arg "-x"
```

快捷键使用
Ctrl+Shift+O
    Split terminals Horizontally.（上下开新窗口）
Ctrl+Shift+E
    Split terminals Vertically.（垂直开新窗口）

## Jupyter Notebook

* 安装

```
pip install --upgrade pip # upgrade pip to the newest version
pip install jupyter
```

* 配置

```
jupyter notebook --generate-config #如/home/inin下没有.jupyter file
jupyter notebook password ##设置密码
jupyter notebook ##启动notebook
```

打开浏览器 输入*127.0.0.1:8888* 输入密码

## blog native compile

准备工作，参考下列教程：

[部署自己的博客：Github+Jekyll](http://harttle.com/2013/10/18/github-homepage-tutorial.html)

[Provide Answers to Questions](https://www.zybuluo.com/wangjialin/note/456653)

Install jekyll on Ubuntu

```
sudo apt-get install ruby ruby-dev make
sudo gem install jekyll --no-rdoc --no-ri
sudo apt-get install nodejs    
```

```
jekyll build -w
jekyll serve
```

* Solve the problem that gem install jekyll error: Reference to [stack exchange](https://stackoverflow.com/questions/22460117/error-error-installing-jekyll-error-failed-to-build-gem-native-extension)

* Use mathjax: Follow the [link](http://sgeos.github.io/github/jekyll/2016/08/21/adding_mathjax_to_a_jekyll_github_pages_blog.html)

* Use table of content: I use the tutorial of the [link](https://github.com/allejo/jekyll-toc).

## ssh登录

```
被登录计算机安装
sudo apt-get install openssh-server

进行登录的计算机
ssh <user_name>@<host_ip>
```

## 网络下载工具 wget

```
wget [参数] [URL地址]
```

Advantage： 1. 支持断点续传 2. 支持**代理服务器** 3. 支持FTP和HTTP下载 4. 程序小，完全免费

下载参数：

```
–spider  测试下载速度
-c, –continue 接着下载没下载完的文件
–bind-address=ADDRESS 指定本地使用地址(主机名或IP，当本地有多个IP或名字时使用)
-Y, –proxy=on/off 打开或关闭代理
–limit-rate=RATE 限定下载输率
-r, –recursive 递归下载(real entire website)
-l, –level=NUMBER 最大递归深度 (inf 或 0 代表无穷)

```

At this time, we can use *wget* to download the entire website with command:
```
$ wget \
     --recursive \
     --no-clobber \
     --page-requisites \
     --html-extension \
     --convert-links \
     --restrict-file-names=windows \
     --domains website.org \
     --no-parent \
         www.website.org/tutorials/html/
```
The options are:

* -- recursive: download the entire Web site.
* -- domains website.org: don't follow links outside website.org.
* --no-parent: don't follow links outside the directory tutorials/html/.
* --page-requisites: get all the elements that compose the page (images, CSS and so on).
* --html-extension: save files with the .html extension.
* --convert-links: convert links so that they work locally, off-line.
* --restrict-file-names=windows: modify filenames so that they will work in Windows as well.
* --no-clobber: don't overwrite any existing files (used in case the download is interrupted and resumed).

## Docker CE install and use on Ubuntu

```
$ sudo apt-get update

$ sudo apt-get install \
    linux-image-extra-$(uname -r) \
    linux-image-extra-virtual

$ sudo apt-get update

$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common

$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

$ sudo apt-key fingerprint 0EBFCD88



$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

$ sudo apt-get update

$ sudo apt-get install docker-ce

$ sudo docker run hello-world
```

可以简单使用docker

```
docker run -d -P seqvence/static-site
docker ps
```

打开网页localhost:docker ip映射到80的端口


## virtualenv虚拟环境

virtualenv is a tool to create isolated Python environments.

### installation

```
$ [sudo] pip install virtualenv
```

### Usage

- [x] Virtualenv has one basic command:

```
virtualenv ENV // mkdir a dir called ENV in inputting command dir
```

- ENV/lib/ and ENV/include/ are created, containing supporting library files for a new virtualenv python.
- Packages installed in this environment will live under ENV/lib/pythonX.X/site-packages/.

- [x] There is a script used to activate virtualenv

On Posix systems, this resides in /ENV/bin/, so you can run:

```
$ source bin/activate
```

This will change your $PATH so its first entry is the virtualenv’s bin/ directory. (You have to use source because it changes your shell environment in-place.) This is all it does.  
To undo these changes to your path (and prompt), just run:```deactivate```.

### The --system-site-packages Option

If you build with ```virtualenv --system-site-packages ENV```, your virtual environment will inherit packages from ```/usr/lib/python2.7/site-packages``` (or wherever your global **site-packages** directory is). If you want isolation from the **global** system, do not use this flag.

## Use Systemctl to manage Systemd Services and Units

Systemctl is used on ubuntu 16.04 not ubuntu 14.04

**Introduction**: Systemd is an init system and system manager that is becoming the new standard for Linux.

### 1. Starting and Stopping services

```
sudo systemctl start <application>.service
sudo systemctl start application  //systemd knows to look for *.service* files
sudo systemctl stop <application>.service
```


### 2. Enabling and Disabling Services

*enable*命令这将使某服务在系统启动时候就被自动start

```
sudo systemctl enable <application>.service
```

以上会创建一个软连接from服务文件（/lib/systemd/system or /etc/systemd/system）into **systemd**寻找的地方（/etc/systemd/system/*some_target*.target.wants）

```
sudo systemctl disable <application>.service
```

以上将移除软连接，<application>服务将不会自动启动

\{Note\} Enalbing a service does not start in the current session.

### 3. Checking the status of Services

```
systemctl status <application>.service
systemctl is-active <app>.service //输出是否
systemctl is-enabled <app>.service //输出是否
```

## Donation

**If you think this useful for you, you can donate for me. Thank you for your support!**

![weixin](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/wx.jpg) | ![zhifubao](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/zfb.jpg)
