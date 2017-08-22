---
layout: post
title: Leetcode
date: 2017-7-28
categories: blog
tags: [理论梳理]
description: leetcode 题目心得总结
---

## 将string类型变量转换为常用的数值类型
//模板函数：（此方法具有普遍适用性）
\#include <sstream>  
template <class Type>  
Type stringToNum(const string& str)  
{  
    istringstream iss(str);  
    Type num;  
    iss >> num;  
    return num;      
}  


## int to string
1. 

```
int a = 10;
char *intStr = itoa(a);
string str = string(intStr);
```

2.

```
int a = 10;
stringstream ss;
ss << a;
string str = ss.str();
```

3.

```\#include <string> 
std::string s = std::to_string(42);
'''

## long int to string

```
#include <sstream>
std::string number;
std::stringstream strstream;
strstream << 1L;
strstream >> number;
```

## string to int

```
1.
string str="123";
atoi( str.c_str() );

2.
std::stoi(str);

3.
std::stringstream ss(str);
ss >> thevalue;
```

## string to double

```string str;
double value = atof(str.c_str());```

```
string word;  
openfile >> word;
double lol = atof(word.c_str());
```

## double to string

```
string s="235.19123";   **最后输出的number只能显示s中的六位（从高位数）**
std::stringstream ss(s);
double number=0;
ss>>number;
```




## 二叉搜索树

同一层的情况下，右边的元素一定比左边的大

* 插入一个数

```
```

* 删除一个数

```
```


