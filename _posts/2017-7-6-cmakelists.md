---
layout: post
title: CMakeLists
date: 2017-7-5
categories: blog
tags: [技术总结]
description: Linux中cmakelists问题
---

[CMake Docs Link](https://cmake.org/Wiki/CMake_2.4.6_Docs)

## Use OpenCV install not in default location
```
  set(OpenCV "/home/inin/OpenDroneMap/Superbuild/install/share/OpenCV")
  ""中的内容应该包含OpenCVConfig.cmake
```

## Using external libraries (CMake:How To Find Libraries)

* Cmake help you with 'find_package' command
* cmake comes with numerous libraries.

### use cmake's own module(/usr/share/cmake/Modules)

* Where is your path is?
* The module path is /usr/share/cmake/Modules
* cmake --help-module

```
find_package(Bzip2)
if(BZIP2_FOUND)
  include_directoties(${BZIP_INCLUDE_DIRS}) # tell the compile where bzlib is
  target_link_libraries(helloworld ${BZIP_INCLUDE_DIRS}) # need to find bzip lib to build exe
			   |
			executable name
endif
```

### Using external libraries that CMake doesn't have module

```
find_package(libXML++ REQUIRED)
include_directories(${libXML++ INCLUDE_DIRS})
set(LIBS ${LIBS} ${libXML++_LIBRARIES})  #set variable
target_link_libraries(helloworld ${LIBS})
```

For this work, you need to put FindLibXML++.cmake file to cmake module path.

>把FindLibXML++.cmake 放在自己project的root（source）目录下的cmake/Modules在cmakelists中加上一句

            set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/Modules")

### 需要一个包的一部分

* find_package(Qt COMPONENT QtOpenGL QtXML REQUIRED)
* 对应的变量 <package>_<component>_FOUND
* e.g. Qt_QtXML_FOUND
* namely, if (Qt_QtXML_FOUND)  巴拉巴拉

### How package find working?

1. CMAKE checks all directories in ${CMAKE_MODULE_PATH}

   then it looks its own directory 

> <CMAKE_Root>/share/cmake-x.y/Modules

   <CMAKE_Root> 一般指 /usr/

2. If no such file is found, it looks for <NAME>Config.cmake e.g. OpenCVConfig.cmake
   or <low-case-name>-config.cmake 

   no matter what mode is used, the following will be defined:
```
   <NAME>_FOUND
   <NAME>_INCLUDE_DIRS or <NAME>_INCLUDES
   <NAME>_LIBRARIES or <NAME>_LIBS
   <NAME>_DEFINITIONS
```
_All this_ takes places in the Find<NAME>.cmake file

套用上页用到的if found， include， target

### pkg-config

小心使用

pkg-config is a build-helping tool, based on '.pc' files that record the location of _ library files _ and _ include files _

## INCLUDE_DIRECTORIES LINK_DIRECOTRIES LINK_LIBRARIES

INCLUDE_DIRECTORIES 增加include路径
LINK_DIRECOTRIES（"×××.so"） 添加需要链接的库文件路径
TARGET_LINK_LIBRARIES(myProject hello)，连接libhello.so库
