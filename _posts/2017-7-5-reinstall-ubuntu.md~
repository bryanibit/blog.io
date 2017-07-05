---
layout: post
title: Linux Tips
date: 2017-7-5
categories: blog
tags: [总结,知识管理]
description: Linux中的一些问题，总结一下
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

first, download .deb install package and install, then go to language support and modify keyboard input system to fcitx and apply all system.

go to fcitx configure (in windows and search a penguin called fcitxconfigure) and click + and cancle only show current language , at the same time find Sogou Pinyin and add.

reboot!

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

sudo -E add-apt-repository -y ppa:george-edison55/cmake-3.x

sudo -E apt-get update

sudo apt-get install cmake






