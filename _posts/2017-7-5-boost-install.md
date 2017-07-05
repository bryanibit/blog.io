---
layout: post
title: Boost re-install
date: 2017-7-5
categories: blog
tags: [总结,知识管理]
description: boost安装总结
---

## Ubuntu中下载boost包安装boost

移除原来的boost方法：

        sudo apt-get --purge remove libboost-dev 
        sudo apt-get --purge remove libboost-all-dev

安装boost 1.57依赖项
        sudo apt-get install mpi-default-dev ##（不知道有没有用）
        sudo apt-get install libbz2-dev
        sudo apt-get install libicu-dev
        sudo apt-get install python-dev


查看boost版本：

        cat /usr/include/boost/version.hpp | grep "BOOST_LIB_VERSION”


The way you are reinstalling the package might install Boost on your (/home) as opposed to on system (/usr..). 

Use the --prefix option of b2 to set the installation location.

Depending on where it installs, at the completion of ./b2

## Windows中下载boost安装包

### 在visual studio中使用boost
失败的原因
Boost has a few libraries that are not standalone and require installation of 3rd party libraries. These are:
Boost.IOStreams Bzip2 filters
Boost.IOStreams Zlib filters
Boost.MPI
Boost.Python
Boost.Regex ICU support
If you do not install these dependencies, Boost.Build will skip them which explains your build message. If you want the optional components, then you have more work to do. This is explained in great detail in this [answer](https://stackoverflow.com/questions/2629421/how-to-use-boost-in-visual-studio-2010/2655683#2655683).

