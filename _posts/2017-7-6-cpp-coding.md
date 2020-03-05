---
layout: post
title: 2018 CPP Coding Techniques
date: 2017-7-5
categories: blog
tags: [考试经验总结]
description: c++问题
---

## CMakeLists.txt

```
project(point_group)
cmake_minimum_required(VERSION 2.8)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -march=native -O3 -pthread")

# OpenCV is not install in default location, if not delete that.
set(OPENCV_DIR "/home/inin/OpenDroneMap/SuperBuild/install/share/OpenCV")
# optional conditions like the following line
set(OpenCV_INCLUDE_DIRS "/home/opencv/install/include")
# Find OpenCV at the default location
find_package(OpenCV 3.3.1 REQUIRED)
set(OpenCV_LIBS opencv_core opencv_imgproc opencv_highgui)
include_directories(${OpenCV_INCLUDE_DIRS})

# Find PCL
# lowcase common and io, correspond to libpcl_common,libpcl_io.so, both files are located in ${PCL_COMMON_LIBRARY}
find_package(PCL 1.3 REQUIRED COMPONENTS common io)
include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})

# Find Boost
# asio.h
find_package(Boost REQUIRED COMPONENTS thread system date_time)
include_directories(${Boost_INCLUDE_DIRS})

# Find Vimba(no .cmake or .pc, not pkg-config)
link_directories("/opt/Vimba2.0/VimbaCPP/DynamicLib/x86_64bit")
#Another way to find dynamic lib
find_library(vimba VimbaCPP /opt/Vimba2.0/VimbaCPP/DynamicLib/x86_64bit)

# Add source directory
aux_source_directory("./src" SRC_LIST)
# Add exectuteable
add_executable(${PROJECT_NAME} ${SRC_LIST})
# set(OpenCV_LIBRARIES ${OpenCV_LIBS}) is content of OpenCVConfig.cmake
# sometimes OpenCV_LIBRARIES is the same with OpenCV_LIBS
target_link_libraries(${PROJECT_NAME}
${OpenCV_LIBS} ${Boost_LIBRARIES}
${PCL_COMMON_LIBRARIES} ${PCL_IO_LIBRARIES}
libVimbaCPP.so #Find Vimba
)
```

这里主要想说的是OpenCV如果作死不装在默认路径上（/usr/local/），需要指定.cmake 位置  
有篇[CMakeLists的博文](https://bryanibit.github.io/blog/2017/07/05/cmakelists/),里面写了指定${CMAKE_MODULE_PATH},  
然后把.cmake copy 到工程根目录下

## C++数据结构 (*). == ->

### c++ 文件结构

防止头文件重定义的方法
```
#ifndef __hull__
#define __hull__ #endif
```

### 获取文件夹下的文件名称

```c
// argb[1] is the input path, char* type
#include <string>
#include <iostream>
#include "boost/filesystem.hpp"
using namespace boost::filesystem;
for (auto i = directory_iterator(string(argv[1])); i != directory_iterator(); i++)
    {
        if (!is_directory(i->path()))
        {
		string p_a = i->path().filename().string();
            cout << string(argv[1]) + "/" + p_a << endl;
	}
        else
           continue;
```

### 读取文件C++

```c
#include<fstream>
// read-mode
std::ifstream infile;
infile.open("./1.txt");
string s;
while(infile >> s) //读取一个word
//while(std::getline(infile, s)) //读取一行
infile.close();
```

### 写入文件c++

```c
 //write-mode
 std::ofstream onfile;
 double x = 80.90809;
 onfile.open("absolute dir", std::ios::app);
 if(onfile.is_open()){
   onfile<<std::fixed<<std::setprecision(8)<<x<<endl;
 }
 onfile.close();
```

### 文件打开方式

用**oftream**或者**ifstream**对象的构造函数或者open()函数指定一种打开方式:  
ios::in        打开文件进行读操作，即读取文件中的数据  
ios::out     打开文件进行写操作，即输出数据到文件中  
ios::ate    打开文件时文件指针指向文件末尾，但是你可以在文件中的任何地方写数据  
ios::app   打开文件不会清空数据，文件指针始终在文件末尾，因此只能在文件末尾写数据  
ios:trunc  默认，若打开文件已存在，测清空文件的内容  
ios::nocreate    若打开文件不存在则不建立，返回打开失败信息  
ios::noreplace  打开文件时不能覆盖，若文件存在测返回打开失败信息  
ios::binary          打开文件为二进制文件，否则为文本文件  
**注：** ate 是 at end 的缩写，trunc是truncate(截断)的缩写，app是append(追加)的缩写


### C++ split()

```
std::vector<std::string> split(const std::string& s, char seperator)
{
	std::vector<std::string> output;
	std::string::size_type prev_pos = 0, pos = 0;
	while ((pos = s.find(seperator, pos)) != std::string::npos)
	{
		std::string substring(s.substr(prev_pos, pos - prev_pos));
		output.push_back(substring);
		prev_pos = ++pos;
	}
	output.push_back(s.substr(prev_pos, pos - prev_pos)); // Last word
	return output;
}
```

Better way to use Boost Library, for example,
```
string line("test\ttest2\ttest3");
vector<string> strs;
boost::split(strs,line,boost::is_any_of("\t"));

cout << "* size of the vector: " << strs.size() << endl;    
for (size_t i = 0; i < strs.size(); i++)
    cout << strs[i] << endl;
```
### 遍历一个vector<double> v

```
for(auto iterator = v.begin(); iterator != v.end(); ++iterator)
{
		double a = *iterator
}
```

### 遍历一个vector< vector<double> > v

```
for(auto iterator = v.begin(); iterator != v.end(); ++iterator)
{
	double b = (*iterator)[3]//取每个vector中的第四个元素
	for(auto iter = (*iterator).begin(); iter != (*iterator).end(); ++iter)
	{
		double a = *iter//取每个vector中的每个vector的每个元素
    }
}
```

### 平面点集表示 vector<pair<double, double>>

使用std::vector<string> point(每个string的
形式为 "3.5,12.3" 形式)填满point_num

```
for (vector<string>::iterator ite = point.begin(); ite != point.end(); ++ite)
	{
		// xy=split(*ite, ',');
		a = *ite;
		xy = split(a, ',');
		double x = atof(xy.at(0).c_str());
		double y = atof(xy.at(1).c_str());
		point_num.push_back(std::make_pair(x, y));

	}
```

```
遍历读取point_num的数字

for (auto iter1 = point_num.begin(); iter1 != point_num.end(); ++iter1)
	{
		double first_number = iter1->first
		double second_number = iter2->first
	}
```

### struct的遍历和表示

```
struct POINT
{
	double x;
	double y;
	bool value = false;
	POINT(double a = 0, double b = 0) { x = a; y = b; } //constructor   
};

POINT ch[100];
std::vector< pair<double,double> > point_hull;
...
得到一个ch，赋值的ch的value变为true，此时遍历ch，
注意此时的指针使用
for (auto ite = ch; ite->value != false; ++ite)
	{
		point_hull.push_back(std::make_pair(ite->x, ite->y));
		//make_pair 会将后面接的数字强制转化为double型
	}
```


## 设置Timer using Boost

```
#include <boost/asio.hpp>
#include <boost/date_time/posix_time/posix_time.hpp>
int main()
{
    boost::asio::io_service io;
    while(1)
        {
	  boost::asio::deadline_timer t(io, boost::posix_time::milliseconds(100));//boost::posix_time::seconds(5)
          // process your IO here - not sure how long your IO takes, so you may need to adjust your timer
	  t.wait();
	}    
    return 0;
}
```
