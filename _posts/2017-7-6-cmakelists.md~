---
layout: post
title: CMakeLists
date: 2017-7-5
categories: blog
tags: [技术总结]
description: Linux中cmakelists问题
---

## Use OpenCV install not in default location

...
  set(OpenCV "/home/inin/OpenDroneMap/Superbuild/install/share/OpenCV")
  ""中的内容应该包含OpenCVConfig.cmake

## CMake:How To Find Libraries

If your software uses external libraries (i.e. libraries not coming with your software), you don't know in advance where its headers and libraries will be located on the system where your software will be compiled. Depending on the location appropriate include directories and library search paths will have to be added to the compile commands.

Depending on the location appropriate include directories and library search paths will have to be added to the compile commands.

CMake helps you with this by providing the find_package command.

This article briefly discusses how to use external libraries in a CMake project and then moves on to how to write your own find modules for libraries that don't already have one.

### Using external libraries


