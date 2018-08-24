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

## Condition Variable(条件变量)

Condition variable is a kind of Synchronization primitive used for the communication between different threads.

* std::condition_variable：必须与std::unique_lock配合使用
* std::condition_variable_any：更加通用的条件变量，可以与任意类型的锁配合使用，相比前者使用时会有额外的开销

线程B调用wait的时候会释放传入的锁并同时进入等待，当被唤醒时会重新获得锁，

其中

```condition_variable cv.wait(lock, pred());```相当于```while (!pred()) {wait(lock);}```

这个实例可以尝试去掉延时效果：

```
#include <iostream>
#include <string>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <chrono>
std::mutex m;
std::condition_variable cv;
std::string data;
bool ready = false;
bool processed = false;
void worker_thread(){
		    // 等待直至 main() 发送数据
		    std::unique_lock<std::mutex> lk(m);
		    cv.wait(lk, []{return ready;});
		    // 等待后，我们占有锁。
		    std::cout << "Worker thread is processing data\n";
		    data += " after processing";	 
		    // 发送数据回 main()
		    processed = true;
		    std::cout << "Worker thread signals data processing completed\n";
         	    // 通知前完成手动锁定，以避免等待线程只再阻塞（细节见 notify_one ）
		    lk.unlock();
		    cv.notify_one();
}
int main(){
		    std::thread worker(worker_thread);
		    data = "Example data";
		    //std::cout << "Enter main function!\n";
		    // 发送数据到 worker 线程
		    {
		        std::lock_guard<std::mutex> lk(m);
		        ready = true;
		        std::cout << "main() signals data ready for processing\n";
		    }
		    cv.notify_one();
	            //std::this_thread::sleep_for(std::chrono::seconds(3));
		    // 等候 worker
    		    std::cout << "Enter the second breakpoint!\n";
		    {   std::unique_lock<std::mutex> lk(m);
		        cv.wait(lk, []{return processed;});}
		    std::cout << "Back in main(), data = " << data << '\n';
		    worker.join();
}
```

## Const 

* const iterator or const_iterator

```
std::vector<int> vec;
-------------------
const std::vector<int>::iterator iter = vec.begin();//iter acts like a T* const
*iter = 10;//OK, changes what iter points
++iter; //error, iter is const
-------------------
std::vector<int>::const_iterator cIter = vec.begin();
*cIter = 10;//error!*cIter is const
++cIter; //fine, changes cIter
```

const member functions(const成员函数)
member functions(成员函数)被声明为const的目的是标明这个member functions(成员函数)可能会被const objects(对象)调用。因为两个原因,这样的member functions(成员函数)非常重要。首先,它使一个class(类)的interface(接口)更容易被理解。知道哪个函数可以改变object(对象)而哪个不可以是很重要的。第二,它们可以和const objects(对象)一起工作。

member functions(成员函数)在只有constness(常量性)不同时是可以被overloaded(重载)的。

```
class TextBlock{
public:
	const char& operator[](std::size_t position) const{
	return text[position];}
	char& operator[](std::size_t position){
	return text[position];}
private:
	std::string text;
};
```

一个const member function(成员函数)不被允许改变调用它的object(对象)的任何non-static data members(非静态数据成员)。


