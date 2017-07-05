---
layout: post
title: OpenCV 3.0 + Install
date: 2017-7-5
categories: blog
tags: [技术总结]
description: Install OpenCV in ubuntu
---

以下方法是编译opencv3.2+contrib部分 使用同样版本的opencv and
opencv_contrib，其中（）中的内容一般没有用,可以去掉, () use CMake's BUILD_opencv_* options.
 
        cmake -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=/usr/local -DBUILD_NEW_PYTHON_SUPPORT=ON -DBUILD_EXAMPLES=ON -DWITH_CUDA=OFF -DWITH_GTK=ON -DOPENCV_EXTRA_MODULES_PATH=<opencv_contrib>/modules (-DBUILD_opencv_legacy=OFF) <opencv_source_directory>


