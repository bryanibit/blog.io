---
layout: post
title: Linux small tools
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

[部署自己的博客：Github+Jekyll](http://harttle.com/2013/10/18/github-homepage-tutorial.html)

1. install jekyll

- solve the problem that gem install jekyll error:
[stack exchange](https://stackoverflow.com/questions/22460117/error-error-installing-jekyll-error-failed-to-build-gem-native-extension)

```
sudo apt-get install ruby ruby-dev make
gem install jekyll --no-rdoc --no-ri
sudo apt-get install nodejs    
```

```
jekyll build -w
jekyll serve
```

## ssh登录

```
被登录计算机安装
sudo apt-get install openssh-server

进行登录的计算机
ssh <user_name>@<host_ip>
```

## find命令

find <指定目录> <指定条件> <指定动作>

$ find . -name 'my*'

搜索当前目录（含子目录，以下同）中，所有文件名以my开头的文件。

## 正则表达式

[正则表达式是一种用来识别文本模式的符号表示法](https://billie66.github.io/TLCL/book/zh/chap20.html)。

1. grep 程序以这样的方式来接受选项和参数：

```
grep [options] regex [file...]
```

2. 正则表达式元字符由以下字符组成： 元字符相对于原意字符

```
^ $ . [ ] { } - ? * + ( ) | \
```

- 注意正则表达式‘^$’（行首和行尾之间没有字符）会匹配空行

- linux自带一本英文字典在/usr/share/dict中的word中。

3. 否定意义表达

如果在正则表示式中的**第一个字符**是一个插入字符，则剩余的字符被看作是不会在给定的字符位置出现的 字符集合。e.g.

```
grep [^bg]zip file.txt #file.txt中包含zip不包含bzip、gzip的段落
```

4. 缩写

表达式就匹配了所有以字母和数字开头的文件名：

```
grep -h '[A-Za-z0-9]' word*.txt # -h hide filename
```

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
