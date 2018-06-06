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

In insert mode, other clipboard content could add to vim with CTRL-V. At this time, the format or indent could be messed up.

Inputting **=G** in normal mode can solve the problem. The first view of openning vim is mormal mode.

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

# Linux shell脚本入门之文本处理

## 列出目录项

以时间顺序列表式列出目录项
```
ls -lr
```

## 文件查找

find是实时查找，如果需要更快的查询，可试试locate；与find不同，locate并不是实时查找。

locate会为文件系统建立索引数据库，如果有文件更新，需要定期执行更新命令来更新索引库:

```
locate string
```

## 查看文件内容

显示文件第一行:

```
$head -1 filename
```

显示文件倒数第五行:

```
$tail -5 filename
```

查看两个文件间的差别:

```
$diff file1 file2
```

## 给文件增加别名

创建符号链接/硬链接:

```
ln cc ccAgain :硬连接；删除一个，将仍能找到；
ln -s cc ccTo :符号链接(软链接)；删除源，另一个无法使用；（后面一个ccTo 为新建的文件）
```

## 重定向与管道

前面成功，则执行后面一条，否则，不执行:&&
前面失败，则后一条执行: ||

```
ls /proc && echo  suss! || echo failed.
```

## find命令

```
find . ! -name "*.txt" -exec rm {} //查找不是txt的文件 并删除所有文件
find . -regex "*.txt" //不忽略大小写  -iregex 忽略大小写
find . -type d //只列出所有目录  -type f 文件 / l 符号链接 / d 目录
find . -atime 7 -type f //最近第7天被访问过的所有文件
find . -atime -7 -type f //最近7天内被访问过的所有文件
find . -cmin -60 //最近60mins更改的文件
find . -atime +7 type f //最近7天前被访问过的所有文件
find . -type f -size +2k //w字 k M G 寻找大于2k的文件

```

## -exec命令

如果要执行多个命令，可以将多个命令写成一个脚本，然后-exec调用执行脚本即可

```
-exec ./commands.sh {}
```

## *grep*文本搜索

```
grep [option] <match_patten> <file> // 默认访问匹配行

```

option:
```
    -o 只输出匹配的文本行 VS -v 只输出没有匹配的文本行

    -c 统计文件中包含文本的次数
        grep -c “text” filename

    -n 打印匹配的行号
    -i 搜索时忽略大小写
    -l 只打印文件名
```

E.g:

```
grep -R -n "class" <file>/ //在多级目录中对文本**递归**搜索(程序员搜代码的最爱）
grep -e "class" -e "vitural" <file> //匹配**多个**模式
```

## xargs 命令行参数转换

xargs 能够将输入数据转化为特定命令的命令行参数；这样，可以配合很多命令来组合使用。

有点像给其他命令加上更多的-a 或者-n 意义或者通过管道加到其他命令上的这种感觉

## wc统计行和字符的工具

```
$wc -l file // 统计行数
$wc -w file // 统计单词数
$wc -c file // 统计字符数
```

# Linux shell脚本入门之磁盘管理

## 查看磁盘空间

```
 df -h
```

## 查询当前目录的大小

```
du -sh
```

1. 打包和压缩：

```
tar -cvf etc.tar /etc //将/etc文件夹打包归并到一个文件但不压缩
    -c :打包选项
    -v :显示打包进度
    -f :使用档案文件
gzip etc.tar //压缩文件
```

2. 解包:
```
gunzip demo.tar.gz //解压后缀为 .tar.gz的文件 1. 先解压缩，生成**.tar:
tar -xvf demo.tar
```

```
bzip2 -d  demo.tar.bz2
tar -xvf  demo.tar
```

# 网络下载工具 wget

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


## virtualenv

virtualenv is a tool to create isolated Python environments.

### installation 

```
$ [sudo] pip install virtualenv
```

### Usage

1. Virtualenv has one basic command:

```
virtualenv ENV // mkdir a dir called ENV in inputting command dir
```

- ENV/lib/ and ENV/include/ are created, containing supporting library files for a new virtualenv python. 
- Packages installed in this environment will live under ENV/lib/pythonX.X/site-packages/.

2. There is a script used to activate virtualenv

On Posix systems, this resides in /ENV/bin/, so you can run:

```
$ source bin/activate
```

This will change your $PATH so its first entry is the virtualenv’s bin/ directory. (You have to use source because it changes your shell environment in-place.) This is all it does.

To undo these changes to your path (and prompt), just run:
```
deactivate
```

## Use Systemctl to manage Systemd Services and Units

Systemctl is used on ubuntu 16.04 not ubuntu 14.04

Introduction:

Systemd is an init system and system manager that is becoming the new standard for Linux. 

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

<Note> Enalbing a service does not start in the current session.

### 3. Checking the status of Services

```
systemctl status <application>.service
systemctl is-active <app>.service //输出是否
systemctl is-enabled <app>.service //输出是否
```


