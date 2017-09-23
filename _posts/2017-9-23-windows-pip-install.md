---
layout: post
title: Pip install on windows
date: 2017-9-23
categories: blog
tags: [技术总结]
description: Install pip and python on wins
---

## install python

- Download python.exe and install, at the same time, add C:\Python27 (python install dir) to system env
- Go to [pip download](https://pypi.python.org/pypi/pip#downloads), 解压到随便一个文件夹下，在cmd中找个该解压目录，输入

```
python setup.py install
```

- 添加C:\Python27\Scripts到Windows系统环境变量(need to reboot)
- 安装正确会在Python安装目录中多一个Scripts文件夹，此时cmd中找个Scripts文件夹，然后

```
easy_install.exe pip
```

打开cmd后，键入 pip install numpy

enjoy!
