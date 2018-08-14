---
layout: post
title: Leetcode
date: 2017-7-28
categories: blog
tags: [理论梳理]
description: Leetcode & Codeing skill
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

## 最小生成树

生成树是建立在无向图中的，对于有向图，则没有生成树的概念，最小生成树不一定唯一，当所有边权值不同时唯一。但是权值之和时一定的

## 邻接矩阵/逆邻接矩阵

无向图的邻接矩阵时对称的，主队角元素为0，有向图不一定

## 连通图

每个顶点之间都有一个边的为连通图

## make_share/share_ptr

```
#include <memory>
#include <string>
 
class Foo
{
public:
    typedef std::shared_ptr<Foo> Ptr;
 
    Foo()
    : a(42)
    , b(false)
    , c(12.234)
    , d("FooBarBaz")
    {}
 
private:
    int a;
    bool b;
    float c;
    std::string d;
};
 
const int loop_count = 100000000;
int main(int argc, char** argv)
{
    for (int i = 0; i < loop_count; i++)
    {
#ifdef USE_MAKE_SHARED
        Foo::Ptr p = std::make_shared<Foo>();
#else
        Foo::Ptr p = Foo::Ptr(new Foo);
#endif
    }
    return 0;
}
```

以上代码当使用```g++ -o2 main.cc```，即可看到make_shared的优势。

## Empty a normal array (e.g. int[])

```
std::fill_n(array, elementCount, 0);
```

## Mutex

```
using MuxGuard = std::lock_guard<std::mutex>;
MuxGuard g(func_mutex);
```

以上代码将锁定上面代码所包括的域


