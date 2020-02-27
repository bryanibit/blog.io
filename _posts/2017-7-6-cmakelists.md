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

1. Method one: set **OpenCV_DIR** variable
```
  set(OpenCV_DIR "/home/inin/OpenDroneMap/Superbuild/install/share/OpenCV")
  ""中的内容应该包含OpenCVConfig.cmake
```
2. Method two: set **CMAKE_PREFIX_PATH** variable
```
set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} "/home/inin/OpenDroneMap/Superbuild/install/share/OpenCV")
  ""中的内容应该包含OpenCVConfig.cmake
```
3. Method complement
```
if(OpenCV_FOUND)
  message("Found OpenCV")
endif()
```

## How package find working?

1. CMAKE checks all directories in **${CMAKE_MODULE_PATH}** then it looks for its own directory, like **<CMAKE_Root>/share/cmake-x.y/Modules** (*<CMAKE_Root>* 一般指 */usr/*).  
2. If no such file is found, it looks for **<NAME>Config.cmake** e.g. **OpenCVConfig.cmake** or **<low-case-name>-config.cmake**. In fact, we need to define **CMAKE_PREFIXE_PATH** for stuff **not** including Find××.cmake but **××config.cmake**.  
No matter what mode(config:XXconfig.cmake or module:findXX.cmake) is used, the following operations will be defined:
```
   <NAME>_FOUND
   <NAME>_INCLUDE_DIRS or <NAME>_INCLUDES
   <NAME>_LIBRARIES or <NAME>_LIBS
   <NAME>_DEFINITIONS
```
**ALL these variables** define in the Find<NAME>.cmake or <NAME>config.cmake file.  
In face, you should generate .cmake file when you write a lib, which is achieved by the following lines in file *CMakeLists.txt*. It can generate .cmake file and save it to *{CMAKE_MODULE_PATH}* or user-defined path.
```
install(EXPORT <name> DESTINATION <path>)
```

## pkg-config

**小心使用**  
pkg-config is a build-helping tool, based on **.pc** files that record the location of _ library files _ and _ include files _.  
凡是由二进制文件（deb、源等）安装的包，都会将<package-name>.pc文件放置在 {PKG-CONFIG-PATH}所包含的路径下。**××.pc**的内容一般是：指明某个包include和lib的目录。  
e.g.  
```sh
➜  echo $PKG_CONFIG_PATH 
/home/bryan/catkin_ws/devel/lib/pkgconfig:/opt/ros/kinetic/lib/pkgconfig:/opt/ros/kinetic/lib/x86_64-linux-gnu/pkgconfig
➜  ls /opt/ros/kinetic/lib/x86_64-linux-gnu/pkgconfig
opencv-3.3.1-dev.pc
➜  head -14 /opt/ros/kinetic/lib/x86_64-linux-gnu/pkgconfig/opencv-3.3.1-dev.pc #显示前14行
Package Information for pkg-config
prefix=/opt/ros/kinetic
exec_prefix=${prefix}
libdir=${exec_prefix}/lib/x86_64-linux-gnu
includedir_old=${prefix}/include/opencv-3.3.1-dev/opencv
includedir_new=${prefix}/include/opencv-3.3.1-dev
Name: OpenCV
Description: Open Source Computer Vision Library
Version: 3.3.1
Libs: -L${exec_prefix}/lib/x86_64-linux-gnu -lopencv_stitching3 -lopencv_superres3 -lopencv_videostab3 -lopencv_aruco3 -lopencv_bgsegm3 -lopencv_bioinspired3 -lopencv_ccalib3 -lopencv_cvv3 -lopencv_dpm3 -lopencv_face3 -lopencv_photo3 -lopencv_fuzzy3 -lopencv_hdf3 -lopencv_img_hash3 -lopencv_line_descriptor3 -lopencv_optflow3 -lopencv_reg3 -lopencv_rgbd3 -lopencv_saliency3 -lopencv_stereo3 -lopencv_structured_light3 -lopencv_viz3 -lopencv_phase_unwrapping3 -lopencv_surface_matching3 -lopencv_tracking3 -lopencv_datasets3 -lopencv_text3 -lopencv_dnn3 -lopencv_plot3 -lopencv_xfeatures2d3 -lopencv_shape3 -lopencv_video3 -lopencv_ml3 -lopencv_ximgproc3 -lopencv_calib3d3 -lopencv_features2d3 -lopencv_highgui3 -lopencv_videoio3 -lopencv_flann3 -lopencv_xobjdetect3 -lopencv_imgcodecs3 -lopencv_objdetect3 -lopencv_xphoto3 -lopencv_imgproc3 -lopencv_core3
Libs.private: -ldl -lm -lpthread -lrt
Cflags: -I${includedir_old} -I${includedir_new}
```

## INCLUDE_DIRECTORIES LINK_DIRECOTRIES LINK_LIBRARIES

```
INCLUDE_DIRECTORIES 增加include路径
LINK_DIRECOTRIES（"×××.so"） 添加需要链接的库文件路径
TARGET_LINK_LIBRARIES(myProject hello)，连接libhello.so库
```

## CMake中一些预定义变量

### cmake变量

    **PROJECT_SOURCE_DIR** 工程的根目录  
    **PROJECT_BINARY_DIR** 运行cmake命令的目录,通常是${PROJECT_SOURCE_DIR}/build  
    **CMAKE_INCLUDE_PATH** 环境变量,非cmake变量  
    **CMAKE_LIBRARY_PATH** 环境变量  
    **CMAKE_CURRENT_SOURCE_DIR** 当前处理的CMakeLists.txt所在的路径  
    **CMAKE_CURRENT_BINARY_DIR** target编译目录，使用ADD_SURDIRECTORY可以更改此变量值，SET(EXECUTABLE_OUTPUT_PATH <新路径>)并不会对此变量有影响,只是改变了最终目标文件的存储路径  
    **CMAKE_MODULE_PATH** 定义自己的cmake模块所在的路径，SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake),然后可以用INCLUDE命令来调用自己的模块  
    **EXECUTABLE_OUTPUT_PATH** 重新定义目标二进制可执行文件的存放位置  
    **CMAKE_CURRENT_LIST_FILE** 输出调用这个变量的CMakeLists.txt的完整路径  
    **CMAKE_CURRENT_LIST_LINE** 输出这个变量所在的行  
    **LIBRARY_OUTPUT_PATH** 重新定义目标链接库文件的存放位置  
    **PROJECT_NAME** 返回通过PROJECT指令定义的项目名称  
    **CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS** 用来控制IF ELSE语句的书写方式

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
CMAKE_FIND_DEBUG_ON 打印find_package信息
```

### cmake常用命令

```
**aux_source_directory(dir var)**，查找指定目录下的所有源文件后，结果放在指定变量名中
**ADD_SURDIRECTORY(src_dir)**指明本项目包含一个子目录 src_dir，这样 src_dir 目录下的 CMakeLists.txt  
     文件和源代码也会被处理 (比如src_dir中cmakelists将自身目录编译为一个静态库\[使用add-library\]，在上一级  
     目录中target_link_libraries，这个过程需要使用ADD_SURDIRECTORY(src_dir)，告知编译器需要编译该链接库目录)
```

## cmake中打印变量信息

- message([STATUS] "message content")
- e.g.
- message([WARRNING] ${EIGEN_INCLUDE_DIR})  e.g. PROJECT_SOURCE_DIR  CMAKE_INCLUDE_PATH
- cmake gets a result: [WARNING]/usr/include/eigen3

## cmake常见问题解析

1. CMake Error: The following variables are used in this project, but they are set to NOTFOUND. ${CERES_INCLUDE_DIR}  
* Find the include dir: set(CMAKE_INCLUDE_PATH ${CMAKE_INCLUDE_PATH} "/home/inin/OpenDroneMap/SuperBuild//install/include/")  
* 设置include为库安装目录，解决问题  
2. Could not find a package configuration file provided by "Eigen" with any of the following names: EigenConfig.cmake eigen-config.cmake [config-file and find-module](https://cmake.org/cmake/help/v3.7/manual/cmake-packages.7.html#config-file-packages)  
* Tell Find*.cmake files where: set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} /home/inin/OpenDroneMap/SuperBuild/src/opensfm/opensfm/src/cmake/)  
* Tell *config.cmake(no find) files where: set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} /home/bryan/Downloads/pybind11-master/pybind11-master/build/mock_install/share/cmake/pybind11/)  
* The prefixes (directories) listed in it will be searched before the default search directories(CMAKE_MODULE_PATH), and lib/, include/ and bin/ will be appended appropriately.  
3. undefined reference  
Maybe most people met the hardship often, the basic reason is that functions have no definitions. For instance, ```add_executable``` command doesn't add some cpp files which have definitions used in *another cpp file*, such as *main.cpp*. Sometimes, the situation happen because of no corresponding header file and source file.

## cmakelists中关键字（函数）

```
cmakelists             ==>  makefile  
target_link_libraries  ==>  -l  
include_directories    ==>  -I/path  
link_directories       ==>  -L/path  
```

CMake provides direct support for two forms of packages, Config-file Packages and Find-module Packages. Indirect support for pkg-config packages is also provided via the FindPkgConfig module. In all cases, the basic form of find_package() calls is the same: *find_package(Qt5Core 5.1.0 REQUIRED)*

```
find_package(Qt5Core 5.1.0 CONFIG REQUIRED) //显示说明使用Config-file (more modern approach)
find_package(Qt4 4.7.0 MODULE REQUIRED) //显示说明使用find-module
```

* findXXX.cmake中需要手动设置一些变量，包括<package>_FOUND等，而config.cmake中，一旦找到包，将自动设置该变量，config.cmake是一种更高级的方法。  
* find_package() 命令会在模块路径中寻找 Find<name>.cmake ，这是查找库的一个典型方式。首先CMake查看${CMAKE_MODULE_PATH} 中的所有目录，然后再查看它自己的模块目录 <CMAKE_ROOT>/Modules/ ，这种称为模块模式。如果没找到这样的文件，会寻找 <Name>Config.cmake 或者 <lower-case-name>-config.cmake ，它们是假定库会安装的文件（但是目前还没有多少库会安装它们），这种叫做配置模式。  
* 在配置模式下，如果没有找到config.cmake，CMake expects the user to specify the location of this file by filling a cache entry called <name>_DIR (this entry is created by CMake automatically).无论哪种模式，只要找到包，就会定义下面的这些变量，只是这些变量在findXXX.cmake中需要显示声明，在config.cmake中不需要显示声明：

```
<NAME>_FOUND
<NAME>_INCLUDE_DIRS or <NAME>_INCLUDES
<NAME>_LIBRARIES or <NAME>_LIBRARIES or <NAME>_LIBS
<NAME>_DEFINITIONS
```

Find_package自动搜索<name>_DIR，查找config.cmake，并执行，config.cmake创建<name>_LIBRARIES, <name>_INCLUDE_DIRS and <name>_DEFINES

## 指定动态库动态加载时的目录

1. 使用GCC编译动态链接库的项目时，在其他目录下执行很可以出现找不到动态链接库的问题。  
2. 这种情况多发生在动态链接库是自己开发的情况下，原因就是程序运行时找不到去何处加载动态链接库。  
3. 可能会说在编译时指定了链接的目录啊！编译时指定的 -L的目录，只是在程序链接成可执行文件时使用的。-L等效为```include_directories```或者```eport LD_LIBRARY_PATH=...```。  
4. 程序执行时动态链接库加载不到动态链接库，解决办法有两种，第一程序链接时指定链接库的位置，就是使用-wl,-rpath=<link_path>参数，<link_path>就是链接库的路径。如：  

```
gcc -o foo foo.c -L. -lfoo -Wl,-rpath=./
```

上面就是指定了链接的位置在当前目录，这种情况只有在当前目录执行./foo时，才是可以正确使用的。一般情况我们使用如下格式：

```
gcc -o foo foo.c -L$(prefix)/lib -lfoo -Wl,-rpath=$(prefix)/lib
```

第二种方式就是，将链接库的目录添加到*/etc/ld.so.conf*文件中或者添加到*/etc/ld.so.conf.d/*.conf*中，然后使用ldconfig进行更新，进行动态链接库的运行时动态绑定。如：

添加文件*/etc/ld.so.conf.d/foo.conf*，内容如下：

```
/usr/local/lib
```

然后执行如下命令：*ldconfig*


## Set with keyword **CACHE**

```
set(<variable> <value>
    [[CACHE <type> <docstring> [FORCE]] | PARENT_SCOPE])
```

In CMake there are two types of variables: *normal variables* and *cache variables*. Normal variables are meant for the internal use of **the script** (just like variables in most programming languages); they are not persisted across CMake runs. Cache variables (unless set with INTERNAL) are mostly intended for configuration settings where the first CMake run determines a suitable default value, which the user can then override, by editing the cache with tools such as ccmake or cmake-gui. Cache variables are stored in the CMake cache file, and are persisted across CMake runs.

```set(FOO "x")``` sets the normal variable *FOO*, then *${FOO}* first search normal variable, then find cache variable if no normal variable named *FOO*.

* The code ```set(FOO "x")``` sets the normal variable *FOO*. It does not touch the cache, but it will hide any existing cache value *FOO*.  
* The code ```set(FOO "x" CACHE …)``` checks for *FOO* in the cache, ignoring any normal variable of the same name. If *FOO* is in the cache then nothing happens to either the normal variable or the cache variable. If *FOO* is not in the cache, then it is added to the cache.  
* whenever a cache variable is added or modified by a command, *CMake also removes the normal variable of the same name from the current scope* so that an immediately following evaluation of it will **expose the newly cached value**.  
* No need to use ```find_library``` in root directory after applying ```set(<libname> <libvalue> CACHE STRING "description" FORCE)``` before or after *add_library and target_link_directories* in subdirectory.  
As usual, you sould add include_directories of subdirectory to root cmakelists:
```
set(DSV_LIB ${PROJECT_NAME} CACHE INTERNAL "description" FORCE)
set(DSV_INCLUDE ${PROJECT_SOURCE_DIR} CACHE INTERNAL "description" FORCE)
```

## Load Script in CMakeLists.txt

* Use the following commands to load python or shell scripts.  

```
set(cmd "python")
set(generated_path "${PROJECT_SOURCE_DIR}/generated")
execute_process(COMMAND ${cmd} "script.py"
                WORKING_DIRECTORY ${generated_path}
                RESULT_VARIABLE result_generated)
```
The last command means executing ```python script.py``` in directory called "${generated_path}". result_generated save whether true or not.
