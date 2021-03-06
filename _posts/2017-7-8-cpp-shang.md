---
layout: post
title: C++ Textbook 上
date: 2017-07-08
categories: blog
tags: [技术总结]
description: 谭浩强的书粗略浏览一下
---

## 1. C++的输入输出

- 使用<< 流插入运算符和<< 流提取运算符
- C++的输出和输入是用流的方式实现的
- cout<<a<<b;
- cin>>a>>b;
- cin和cout不能用来输入空格和回车，
- 因为会将其跳过。

## 2. 判断语句

if和else if，以及else语句
```
switch(表达式)
  case 常量表达式：语句
  default：语句
```
最后一个default可以不加break，因为整体上是顺序结构的

## 3.循环语句

```
while()
do{}while()
```
- 后者先执行一次，然后再判断条件
- continue结束本次循环，继续下一次循环
- break跳出整个循环

## 4.函数预处理

- 大部分函数，除main以外都封装到class中  
- main只实现调用其他函数的功能  
- 定义函数是平行的/独立的，函数不能嵌套定义，更不能将函数定义写在main中（函数声明可以写在main中）

```
int main()
{
  int max(int a,int b);
}
```

- 形参的意思就是说在程序还没有执行到这里的时候，是不占用内存的
- include的意思就是使用<*库函数*>或者自己的库”自己的库文件.h“

### 4.1 内置/联函数（inline function）

- 在编译时将所调用函数的代码直接嵌入到主调函数中，二不是将流程转出来
- 一般将小于5句并频繁调用的函数前面加上inline
- inline不能包含循环和switch语句
- inline函数应该放在调用此函数相同的文件中

### 4.2 函数重载

- 函数名相同，形参类型/个数/顺序不同

### 4.3 运算符重载

- pass

### 4.4 函数模板

- 函数类型和形参**类型**不具体指定，函数体/形参个数相同
```
template <typename T1, class T2>
通用函数定义
```
```
template <class T>
通用函数定义
```

### 4.5 函数默认值

- 默认值放在函数形参最右侧
- 函数声明时应该写明默认参数值（定义函数内容在main后面）
- 声明指明默认参数后，定义不要再指明一次
- 函数重载不能有默认参数

### 4.6 局部变量和全局变量（内部变量，外部变量）

- main中定义的变量只在main中有效，不会在整个文件或程序中有效
- 函数声明的变量名是忽略的，可以认为其在该句结束后失效

- 全局变量范围为定义变量开始到本源文件结束为止
- 全局变量增加了函数间的数据联系
- 全局变量违背模块话思想，降低函数的通用性  

- 如果将一个文件移动到另一个工程中时，要将有关的外部变量一起移动过去，若该外部变量与其他文件变量同名，就会出现问题
- 在同一个源文件中，局部变量和全局变量同名，全局变量被屏蔽

### 4.7 动态存储与静态存储

**存储空间分为:**  
* 程序区
* 静态存储区：全局变量
* 动态存储区：函数调用的形参，函数中的自动变量（未加static的局部变量），函数调用的现场保护和返回地址

**C++变量属性包括数据类型，存储类别**，数据在内存中的存储方法为静态和动态，具体包括：自动，静态，寄存器，外部的

#### 4.7.1 static局部变量

加static的局部变量
```
int fun(int a)
{
  int b =2;
  static int c=3;
  b = b +1;
  c=c+1;
}
```
**结果：** 每次调用fun（）时，c一直保留,所以多次调用fun后，c一直在增加，但是b一直固定为2.

#### 4.7.2 register声明寄存器变量（编译器会自动优化，不需要自己设置）

- 某个函数执行很多次，每次循环都要引用某个局部变量，此时可以将该局部变量的值存在CPU的寄存器中，而不是内存中。
```
int fac(int n)
{
  register int i, fi=1;
  for(i =1;i<n;i++)
  fi = fi * i;
  return fi;
}
```
**注**: 以上程序当n较大的时候可以省去很多时间

#### 4.7.3 用extern声明外部变量

- 用extern变量声明全局变量，扩展全局变量的作用域
- 对于全局变量提前引用（未定义）作声明
- 在任何一个文件中定义外部变量num，而在另一个文件中用extern对num作外部变量声明
- *extern int num；*

#### 4.7.4 static静态外部变量

- 对于全局变量定义加static，使该变量只能作用于本文件，即使在另一个文件中用extern修饰也无法调用该变量了
- 这样可以使不同人编写的程序中全局变量更规范，防止其他文件引用

### 4.8 内部函数与外部函数

#### 4.8.1 内部函数

- static int fun(int a, int b)
- 又称静态函数，使函数局部化，不和其他文件中的同名函数冲突

#### 4.8.2 外部函数

- 调用外部文件的函数（定义在其他文件中），和extern修饰变量方法相同
- 函数默认都是**外部**的

#### 4.8.3 函数作用域符号::

以下程序输出6 7，表示::取global变量
```cpp
#include <iostream>
int i=6;
int main()
{
    int i=7;
    std::cout << ::i << ' ' << i << std::endl;
}
```

### 4.9 条件编译

```
#ifdef 标识符
  程序段
#else
  程序段
#endif
```
```
#ifndef 标识符
  程序段
#else
  程序段
#endif
```
```
#if 表达式
  程序段
#else
  程序段
#endif
```

## 5. 数组

- 有序数据的集合
- 数组的定义  
  ```int a [10]```编译的时候就需要确定数组的长度，不能在执行的时候才确定

### 5.1 一维数组的初始化

```int a[10]={0,1,2,3,4} //correct```

### 5.2 数组名作为函数参数

```
void select_sort(int array[], int n);
void select_sort(int* array, int n); // ditto
```

### 5.3 字符数组

```
char str[] = {"I am happy"} <==> char str[]="I am happy"  
char str[] = {'I',' ','a','m',' ','h','a','p','p','y','\0'}
```
- 以上三种写法等价，和下面这种不一样:  
```char str[] = {'I',' ','a','m',' ','h','a','p','p','y'}```
- 输出一个字符串数组，cout << str,只要遇到'\0'即停止输出  

### 5.4 字符串处理C函数简介

- #include <cstring\>

在s1末尾追加s2:
- char *strcat(char *restrict s1, const char *restrict s2);

清空s1，将s2赋值到s1中:
- char *strcpy(char *restrict s1, const char *restrict s2, int n);

直到出现第一个不相同的字符，以此为标准输出:
- int strcmp(const char *restrict s1, const char *restrict s2);

char*数组长度（不含'\0'）:
- int strlen(const char * s)

#### 5.4.1 遍历char*

```
void foreach(char* str)
{
  int len = strlen(str);
  for(int i = 0; i <\ len; ++i)
    {
       printf("%c", str[i]);
    }
}
void foreach(char* str)
{
   while(*str)
   {
       printf("%c", str[i]);
       ++str;
   }
}
```

### 5.5 字符串变量(避免数组越界)
- str1=str2 *correct*
- string word="then";
- word[2]='a'; *correct*
- 字符串常量以'\0'结尾，但是将字符串常量存在字符串变量中，只存放字符串本身，无'\0'，这与char *不同

### 5.6 字符串变量的运算

- ```string1 = string2``` in C++ correspond to ```char *strcpy``` in C
- ```string1 = string1+ string2``` correspond to ```char *strcat``` in C
- 直接使用== > 来控制

### 5.7 字符串数组
空

## 6. 指针

- 指针定义方法： 基类型 *指针变量名；
- 基类型决定了指针移动时，移动的字节数目
```
int a[10];
int *p;
p= a;
```
```
const char *str = "I love you";
string str = "I love you";
char str[]="I love you";
```
- char *背后的含义是：给我个字符串，我要修改它,传给函数的字面常量是没法被修改的,所以说，比较合理的办法是把参数类型修改为const char *

```
int two_dimen_array[3][4] = { {1,2,3,4},{5,6,7,8},{1,2,3,4} };
```
* 针对以上二维数组，two_dimen_array+1和two_dimen_array[0]+1在内存中移动的位数不同的
* 前者移动了4*4=16个字节，后者只移动了4个字节，(已知int型占4个字节)
* 所以说**指针类型很重要**，在定义一个指针时必不可少的部分

### 6.1 函数指针

- 函数在编译时分配一个入口地址就是函数的指针
- 函数名代表函数入口
```
int max(int x,int y);
int (*p)(int,int);//定义指向函数的指针变量p
p=max;//由于max才是函数入口，而不是max(int a,int b)
```
调用形式为
```
int a=0,b=0;
m=p(a,b);
```
### 6.2 指针数组

```
int *p[4];
```
a little confused! Think about ```char* str[]```.

下面举个例子
- 对一个数组{"basic","fortune","c++","pascal"}进行排序并显示输出

          #include <iostream>
          #include <cstring>
          using namespace std;
          void sort_select(char **name,int n);
          void print_screen(char **name, int n);
          int main(int argc, char **argv)
          {
              //There will have a warning about **const**
              char * name[]={"basic","fortune","c++","pascal"};
              //string name[]={"basic","fortune","c++","pascal"};
              sort_select(name,4);
              print_screen(name, 4);

          }
          //传进来的正是指针，所以修改了name内容
          void sort_select(char **name,int n)//void sort_select(char *name[],int n)
          {
              for(int i =0;i <n-1;++i)
              {
                  if(strcmp(name[i],name[i+1])>0)
                  {
                      char *temp=name[i];
                      name[i]=name[i+1];
                      name[i+1]=temp;
                  }
              }
          }
          void print_screen(char **name, int n)
          {
              for(int i = 0;i < n;++i)
              {
                  cout<<name[i]<<endl;
              }
          }

- std::string to char *
```
std::string str = "string";
char *cstr = new char[str.length() + 1];
strcpy(cstr, str.c_str());
// do stuff
delete [] cstr;
```

- char * to std::string
```
char* a;
string (a);
```

- std::string to double/float  
**If cout transformed string has missing numbers, it must be cout's error not transform**  
**Use compiler to see the double or use**
```c
double d = 3.14159265358979
cout.precision(17);
cout << "Pi: " << fixed << d << endl;
// or
//should include <iomanip>
cout << std::setprecision (15) << d << endl;
```
```c
//C++ 11
std::stod("2.345");
// sstream
std::istringstream iss("2.345");
double d = 0;
iss >> d;
//lexical_cast
boost::lexical_cast<double>("1.234567")
```

- double to std::string `std::to_string(123.4);`
`to_string` only transform 6 precision double. So `to_string(0.0000001)` is **0**. Instead of using `to_string`,
the following code adopts `std::stringstream` which should include headfile **<stream>**:
```c
string ConvertDouble(const double num, const int precision) {
	stringstream stream;
	// include <iomanip> for setprecision
	stream << std::fixed << std::setprecision(precision) << num;
	return stream.str();
```


**data type in all**
```c
int i;
int *p;
int a[n];
int *p[n];
int (*p)[n];
int f();
int *p();
int (*p)()
int **p
```
指针的使用优势：1.提高程序效率 2.在调用函数的时候，如果改变被调用函数中某些变量的值，这些值能为主调函数使用 3.实现动态存储分配

### 6.3 引用
- int a;
- int &b=a;
- b是a的一个引用，b和a占内存同一个存储单元，具有同一个地址
- 引用不能用来代表数组和指针

#### 引用作为函数参数

- 它作为函数参数，可以扩充函数传递数据的功能
- 当引用作为函数参数，可以看成传进函数的是地址，所以形参也会跟着被调用函数操作而变化

*e.g* 值传递的不同方式
```
void swap(int * p1, int * p2);
int main(int argc,char **argv)
{
	int a,b;
	swap(&a,&b);
}
```

```
void swap(int &p1, int &p2);
int main(int argc,char **argv)
{
	int a,b;
	swap(a,b);
}
```

## 7.自定义数据类型 struct

```
struct 结构体类型名
{成员列表}；
```
- 先定义在声明，一般放在某个文件中，使用的时候\#include该文件即可
- student.name 其中.是成员运算符，是所有运算符中优先级最高的
* 结构体变量指针
```
Student stu;
Student *p = &stu;
```
- p->name, ->是指向运算符

### 7.1 使用结构体设计链表
```
struct Student
{
	int num;
	float score;
	Student * next;
};
```
* 所有结点不是临时开辟的，不能用完后释放，这种链表称为静态链表。对其访问可以使用next指针也可以使用结构体变量a,b,c去访问
* 各结点可以随时插入和删除，这些结点并没有变量名，只能先找到上一个结点，才能根据找到该结点

### 7.2 结构体类型数据作为函数参数

（1）用结构体变量名作为函数参数。改变结构体内容，无法传出到调用函数  
（2）用指向结构体变量的指针作实参，将结构体地址传给形参  
（3）用结构体变量的引用作函数参数  

### 7.3 动态分配和撤销内存的运算符new和delete

- ```new``` and ```delete``` 和++ — 一样是运算符
- **C**中运用```malloc和free```来分配和撤销内存，C++使用```new和delete```取代前两者
- 一般格式为： ```new 类型[初值]```
- new**返回**指向```存储空间的地址```  
e.g.  
- new int;        开辟存放int型的存储空间，返回一个指向该存储空间的地址（返回指针）
- int *pt = new int[10];    开辟一个存放int型数组，返回首元素地址
- new int[9][9];
- float *p = new float(3.1415);开辟一个存放单精度空间，初值为3.1415，返回指针赋值到p
- 用new分配数组空间时，不能指定初值，new返回为NULL时，分配不成功. 这里的new和delete中[]指代是数组
```
int* p = new int; //allocate memory
new (p) int;   // not allocate memory just renew a object
```

---

- delete [] 指针变量
- delete p;
- delete [] pt;

### 7.4 共用体（union）类型
```
union data
{
	int i;
	char ch;
	double d;
}a,b,c;
```
- 节省存储空间，每次只能存在一个成员变量，对新的成员变量赋值，其他成员变量就消失，其在内存中所占的存储空间是最长的字符所占的存储空间

### 7.5 枚举类型

- enum weekday{Sun=7,Mon=1,Tue,Wed,Tur,Fri,Sat};
- enum 枚举类型名 {枚举常量列表}
- weekday workday； the same with the following:
- enum {Sun,Mon,Tue,Wed,Tur,Fri,Sat} workday；

* 不能直接对枚举类型赋值 Sun=0；
* 不能直接这样： workday = 0；

### 7.6 typedef

空



***
