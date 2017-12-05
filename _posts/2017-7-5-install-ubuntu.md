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

Install from PPA

```
sudo add-apt-repository ppa:mystic-mirage/pycharm
sudo apt-get update
sudo apt-get install pycharm  ## professional version
```

```
Download official version professional
go to  
- [crack](idea.qinxi1992.cn)
add “0.0.0.0 account.jetbrains.com” to /etc/hosts file
```

```
select 'for all users'
PATH=$PATH:/home/inin/{installation path}/bin
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
**1. Active code 激活码**

```
CNEKJPQZEX-eyJsaWNlbnNlSWQiOiJDTkVLSlBRWkVYIiwibGljZW5zZWVOYW1lIjoibGFuIHl1IiwiYXNzaWduZWVOYW1lIjoiIiwiYXNzaWduZWVFbWFpbCI6IiIsImxpY2Vuc2VSZXN0cmljdGlvbiI6IkZvciBlZHVjYXRpb25hbCB1c2Ugb25seSIsImNoZWNrQ29uY3VycmVudFVzZSI6ZmFsc2UsInByb2R1Y3RzIjpbeyJjb2RlIjoiQUMiLCJwYWlkVXBUbyI6IjIwMTgtMDEtMzAifSx7ImNvZGUiOiJETSIsInBhaWRVcFRvIjoiMjAxOC0wMS0zMCJ9LHsiY29kZSI6IklJIiwicGFpZFVwVG8iOiIyMDE4LTAxLTMwIn0seyJjb2RlIjoiUlMwIiwicGFpZFVwVG8iOiIyMDE4LTAxLTMwIn0seyJjb2RlIjoiV1MiLCJwYWlkVXBUbyI6IjIwMTgtMDEtMzAifSx7ImNvZGUiOiJEUE4iLCJwYWlkVXBUbyI6IjIwMTgtMDEtMzAifSx7ImNvZGUiOiJSQyIsInBhaWRVcFRvIjoiMjAxOC0wMS0zMCJ9LHsiY29kZSI6IlBTIiwicGFpZFVwVG8iOiIyMDE4LTAxLTMwIn0seyJjb2RlIjoiREMiLCJwYWlkVXBUbyI6IjIwMTgtMDEtMzAifSx7ImNvZGUiOiJEQiIsInBhaWRVcFRvIjoiMjAxOC0wMS0zMCJ9LHsiY29kZSI6IlJNIiwicGFpZFVwVG8iOiIyMDE4LTAxLTMwIn0seyJjb2RlIjoiUEMiLCJwYWlkVXBUbyI6IjIwMTgtMDEtMzAifSx7ImNvZGUiOiJDTCIsInBhaWRVcFRvIjoiMjAxOC0wMS0zMCJ9XSwiaGFzaCI6IjUxOTU1OTMvMCIsImdyYWNlUGVyaW9kRGF5cyI6MCwiYXV0b1Byb2xvbmdhdGVkIjpmYWxzZSwiaXNBdXRvUHJvbG9uZ2F0ZWQiOmZhbHNlfQ==-QOxwjWvRwJz6vo6J6adC3CJ4ukQHosbPYZ94URUVFna/Rbew8xK/M5gP3kAaPh6ZDveFdtMR1UBoumq3eCwXtXM3U3ls5noB4LIr+QplVlCj2pK5uNq7g/feyNyQcHpSXtvhIOnXDBLOecB05DOsxzm0p7ulGGJoAInmHeb9mc0eYjqc4RPpUQfh6HSYBnvEnKMlLF5bz4KEtzmsvvgA55CwzwQ3gRitm5Q/wUT7AQCBdjmBfNUjKVQL6TSjSDPp56FUdEs4Aab8LqstA2DIMbxocO64rvytmcUeIwu8Mi5uq87KQP5AQMSMYb59Inbd+dmVfx5cJo3fRS4/5s3/Hg==-MIIEPjCCAiagAwIBAgIBBTANBgkqhkiG9w0BAQsFADAYMRYwFAYDVQQDDA1KZXRQcm9maWxlIENBMB4XDTE1MTEwMjA4MjE0OFoXDTE4MTEwMTA4MjE0OFowETEPMA0GA1UEAwwGcHJvZDN5MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAxcQkq+zdxlR2mmRYBPzGbUNdMN6OaXiXzxIWtMEkrJMO/5oUfQJbLLuMSMK0QHFmaI37WShyxZcfRCidwXjot4zmNBKnlyHodDij/78TmVqFl8nOeD5+07B8VEaIu7c3E1N+e1doC6wht4I4+IEmtsPAdoaj5WCQVQbrI8KeT8M9VcBIWX7fD0fhexfg3ZRt0xqwMcXGNp3DdJHiO0rCdU+Itv7EmtnSVq9jBG1usMSFvMowR25mju2JcPFp1+I4ZI+FqgR8gyG8oiNDyNEoAbsR3lOpI7grUYSvkB/xVy/VoklPCK2h0f0GJxFjnye8NT1PAywoyl7RmiAVRE/EKwIDAQABo4GZMIGWMAkGA1UdEwQCMAAwHQYDVR0OBBYEFGEpG9oZGcfLMGNBkY7SgHiMGgTcMEgGA1UdIwRBMD+AFKOetkhnQhI2Qb1t4Lm0oFKLl/GzoRykGjAYMRYwFAYDVQQDDA1KZXRQcm9maWxlIENBggkA0myxg7KDeeEwEwYDVR0lBAwwCgYIKwYBBQUHAwEwCwYDVR0PBAQDAgWgMA0GCSqGSIb3DQEBCwUAA4ICAQC9WZuYgQedSuOc5TOUSrRigMw4/+wuC5EtZBfvdl4HT/8vzMW/oUlIP4YCvA0XKyBaCJ2iX+ZCDKoPfiYXiaSiH+HxAPV6J79vvouxKrWg2XV6ShFtPLP+0gPdGq3x9R3+kJbmAm8w+FOdlWqAfJrLvpzMGNeDU14YGXiZ9bVzmIQbwrBA+c/F4tlK/DV07dsNExihqFoibnqDiVNTGombaU2dDup2gwKdL81ua8EIcGNExHe82kjF4zwfadHk3bQVvbfdAwxcDy4xBjs3L4raPLU3yenSzr/OEur1+jfOxnQSmEcMXKXgrAQ9U55gwjcOFKrgOxEdek/Sk1VfOjvS+nuM4eyEruFMfaZHzoQiuw4IqgGc45ohFH0UUyjYcuFxxDSU9lMCv8qdHKm+wnPRb0l9l5vXsCBDuhAGYD6ss+Ga+aDY6f/qXZuUCEUOH3QUNbbCUlviSz6+GiRnt1kA9N2Qachl+2yBfaqUqr8h7Z2gsx5LcIf5kYNsqJ0GavXTVyWh7PYiKX4bs354ZQLUwwa/cG++2+wNWP+HtBhVxMRNTdVhSm38AknZlD+PTAsWGu9GyLmhti2EnVwGybSD2Dxmhxk3IPCkhKAK+pl0eWYGZWG3tJ9mZ7SowcXLWDFAk0lRJnKGFMTggrWjV8GYpw5bq23VmIqqDLgkNzuoog==
```

**2.激活server：**http://idea.imsxm.com/

### install zsh

```
sudo apt-get install zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
chsh -s /bin/zsh
sudo reboot
```
copy ros content in .bashrc to .zshrc

### install vim

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
