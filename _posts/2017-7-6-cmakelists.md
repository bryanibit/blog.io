---
layout: post
title: CMakeLists
date: 2017-7-5
categories: blog
tags: [技术总结]
description: Linux中cmakelists问题
---

[**解释cmake参数命令的博客**](https://blog.csdn.net/wzzfeitian/article/details/40963457)
[**CMake Docs Link**(```man cmake```)](https://cmake.org/Wiki/CMake_2.4.6_Docs)

## Use OpenCV install not in default location
```
  set(OpenCV_DIR "/home/inin/OpenDroneMap/Superbuild/install/share/OpenCV")
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

> <CMAKE_Root> 一般指 /usr/


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

## CMake中一些预定义变量

### cmake变量

```
    **PROJECT_SOURCE_DIR** 工程的根目录
    **PROJECT_BINARY_DIR** 运行cmake命令的目录,通常是${PROJECT_SOURCE_DIR}/build
    **CMAKE_INCLUDE_PATH** 环境变量,非cmake变量
    **CMAKE_LIBRARY_PATH** 环境变量
    **CMAKE_CURRENT_SOURCE_DIR** 当前处理的CMakeLists.txt所在的路径
    **CMAKE_CURRENT_BINARY_DIR target** 编译目录，使用ADD_SURDIRECTORY可以更改此变量值，SET(EXECUTABLE_OUTPUT_PATH <新路径>)并不会对此变量有影响,只是改变了最终目标文件的存储路径
    **CMAKE_MODULE_PATH** 定义自己的cmake模块所在的路径，SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake),然后可以用INCLUDE命令来调用自己的模块
    **EXECUTABLE_OUTPUT_PATH** 重新定义目标二进制可执行文件的存放位置
    **CMAKE_CURRENT_LIST_FILE** 输出调用这个变量的CMakeLists.txt的完整路径
    **CMAKE_CURRENT_LIST_LINE** 输出这个变量所在的行
    **LIBRARY_OUTPUT_PATH** 重新定义目标链接库文件的存放位置
    **PROJECT_NAME** 返回通过PROJECT指令定义的项目名称
    **CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS** 用来控制IF ELSE语句的书写方式
```

### 系统信息


```
    CMAKE_MAJOR_VERSION cmake主版本号,如2.8.6中的2
    CMAKE_MINOR_VERSION cmake次版本号,如2.8.6中的8
    CMAKE_PATCH_VERSION cmake补丁等级,如2.8.6中的6
    CMAKE_SYSTEM 系统名称,例如Linux-2.6.22
    CAMKE_SYSTEM_NAME 不包含版本的系统名,如Linux
    CMAKE_SYSTEM_VERSION 系统版本,如2.6.22
    CMAKE_SYSTEM_PROCESSOR 处理器名称,如i686
    UNIX 在所有的类UNIX平台为TRUE,包括OS X和cygwin
    WIN32 在所有的win32平台为TRUE,包括cygwin
```

### 开关选项

```
    BUILD_SHARED_LIBS 控制默认的库编译方式。如果未进行设置,使用ADD_LIBRARY时又没有指定库类型,默认编译生成的库都是静态库 （可在t3中稍加修改进行验证）
    CMAKE_C_FLAGS 设置C编译选项
    CMAKE_CXX_FLAGS 设置C++编译选项
       e.g. 
         set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -march=native -O3 -pthread")
```

### cmake常用命令

    **aux_source_directory(dir var)**，查找指定目录下的所有源文件后，结果放在指定变量名中
    **ADD_SURDIRECTORY(src_dir)**指明本项目包含一个子目录 src_dir，这样 src_dir 目录下的 CMakeLists.txt 文件和源代码也会被处理 (比如src_dir中cmakelists将自身目录编译为一个静态库\[使用add-library\]，在上一级目录中target_link_libraries，这个过程需要使用ADD_SURDIRECTORY(src_dir)，告知编译器需要编译该链接库目录)


## cmake中打印变量信息

- message([STATUS] "message content")
- e.g.
- message([WARRNING] ${EIGEN_INCLUDE_DIR})  e.g. PROJECT_SOURCE_DIR  CMAKE_INCLUDE_PATH
- cmake gets a result: [WARNING]/usr/include/eigen3

## cmake常见问题解析

1. CMake Error: The following variables are used in this project, but they are set to NOTFOUND. ${CERES_INCLUDE_DIR}

- Find the include dir: set(CMAKE_INCLUDE_PATH ${CMAKE_INCLUDE_PATH} "/home/inin/OpenDroneMap/SuperBuild//install/include/")

设置include为库安装目录，解决问题

2. Could not find a package configuration file provided by "Eigen" with any of the following names: EigenConfig.cmake eigen-config.cmake

- Tell .cmake files where: set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} /home/inin/OpenDroneMap/SuperBuild/src/opensfm/opensfm/src/cmake/)





















