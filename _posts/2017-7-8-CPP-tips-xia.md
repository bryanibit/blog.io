---
layout: post
title: C++ textbook 下
date: 2017-07-8
categories: blog
tags: [理论梳理]
description: 谭浩强的书粗略浏览一下
---

**以下部分高能预警，请细心体会（本书第8~12章）今天看完前10章**
## 8.类和队象
- 任何对象都应该有两个要素：属性（静态特征）/行为（动态特征）,由数据和函数两部分组成
- 对象在设计时，考虑封装特性，将外部行为和内部行为分隔开，同时将各个对象之间的相对独立
* 对象是具体存在的，类是对象的抽象，对象是类的特例
* 多态指：由继承而产生的相关不同的类，其对象对同一消息会作不同反应

**程序=对象s+消息**

#### 类和结构体的区别
- 首先你可以将类声明时使用的class关键字换成struct，这是完全成立的
- struct未做声明时，默认成员为public这是和class不同的地方
- 当类的某成员函数大于3行时，可以将声明放在类内，定义移到类外，使整个类看起来更加清楚
- 类的对象的存储空间：共有函数存储空间，自身的存储空间存储成员变量
```
class Time
{public:
 int hour;
 int minute;
 private:
 int show_times();
};//这些放在time.h头文件中
int Time::show_times()
{}
//这些放在time.cpp源文件中
Time t, *p;
p=&t;
cout<<p->hour;
cout<<t.minute;
cout<<endl;//这些放在main中
```
## 9.类和对象的进一步讨论

### 9.1 构造函数

- 用户根据**初始化**的要求设计函数体和函数参数，这就是构造函数
- 构造函数与类同名，在声明对象时自动执行构造函数，构造函数没有返回类型，因而也没有类型
         构造函数名（类型1形参1， 类型2形参2）
#### 9.1.1 用参数初始化表对数据成员初始化
```
class Box{Box(int,int, int);
int height;
int width;
int length;}
Box::Box(int h,int w, int len):height(h),width(w),length(len){}
```
#### 构造函数重载
以下声明两个Box()
```
class Box{Box(int,int, int);
Box();
int height;
int width;
int length;}
Box::Box()
{
 height = 10;
 width = 10;
 length = 10;
}
Box::Box(int h,int w, int len):height(h),width(w),length(len){}
```
#### 构造函数的默认参数

- Box(int h\=10,int w\=10, int len\=10); 这是声明，在类内部，具体定义在类外边
这相当于好多个重载函数的作用

#### 说明
* 调用构造函数时，不必给出实参的构造函数称为默认构造函数，或叫缺省构造函数
* 构造对象的写法为Box box2; 不是 Box box2();这是声明函数的返回值为类Box
* 构造函数的重载在函数声明的时候就要确定，具体定义的时候没有默认参数值，和普通的默认参数函数声明定义相同
* 一个类只能同时有一个构造函数有效：Box();Box(int\=10,int \=10, int \=10);同时存在二者是错误的

### 9.2 析构函数

- static局部对象在main结束后才被析构；用new运算符动态建立的对象，当用delete运算符释放该对象是，先调用该对象的析构函数
- 析构函数的**作用**不是删除对象，而是撤销对象占用的内存之前完成一些清理工作，使这部分内存可以分配给新对象使用
- 析构函数不能重载，你想重载啥！
- 析构函数也可写一些最后想说的话，在对象即将销毁之前
### 9.3 对象指针

```
类的成员变量
int *p1;
p1 = &t1.hour;
cout<< *p1<<endl;
```

```
普通函数
void (*p)(int, int);//指向void型函数的指针变量
p = fun;
(*p)(int a,int b)//调用fun函数
```
```
类的成员函数
void (Time::*p2)();
p2=Time::get_time;
(t1.*p2)();//调用t1中的p2所指函数
```
* 一个类多个对象，成员函数对应相同的代码段，以上赋值指针变量p2的应该是公用的函数代码段的入口地址
以上两句定义和指定指针指向合并为
```
void (Time::*p2)()=Time::get_time;
```
- 函数的指针变量的类型必须与赋值号右侧函数类型在以下3个方面匹配：
- 1.函数参数的类型和参数个数 2.函数返回值的类型 3.所属的类

### 9.4 this指针

- 如果一个类对应多个对象，存放多个对象的数据成员，但是不同的对象都调用同一个函数代码段
- 不同对象的成员函数引用数据成员时，保证引用的是所指定的对象的数据成员
- 每个*成员函数*都包含一个特殊的指针，成为this，它指向*本类对象*的指针，它的值是*当前被调用的成员函数所在的对象的起始地址*
```
对象指针
Time *p;
Time t1;
p=&t1;
```

- 用new运算符动态分配内存后返回一个指向*新对象的指针的值*,就是所*分配的内存空间的起始地址*
```
Box *pt;
pt=new Box;
```
- 以上方法新建了一个对象可以使用以下方法来访问
```
pt->height;
```
合成一句话并初始化
* Box *pt = new Box(12,15,18); //构造函数

#### 对象的赋值

对象名1 = 对象名2

box1 = box2;

#### 对象的复制

类型 对象2（对象1）

Box box2(box1);

Box box2 = box1;

### 9.5 类的静态成员

- 静态成员变量不隶属于任何一个对象，它是类的成员，即使不定义对象，静态数据成员也被分配空间，也可被引用
- 静态成员变量不随对象的撤销而释放，它是类的成员。
- 静态成员变量只能在类外进行初始化
```
int Box::height = 10;//height is static variable of class Box
```
- 初始化（构造函数）中不能操作静态变量
```
Box b = new Box();
b.height;//可以使用对象名访问静态成员
```
#### 类的静态函数

- 静态函数存在的价值主要是操作静态变量
- 静态函数是类的而不是对象的成员，所以它没有this指针，所以无法访问本类中的非静态成员
- 因为

```

int Box::volume()
{
	return (height\*width\*length);
}

change to the following

int Box::volume(Box *this)
{
	return (this->height\*this->width\*this->length);
}
```

- 由于静态变量没有this指针，找不到内存中当前需要操作的成员变量
- Therefore, 保证静态函数只操作静态变量，当然也可以操作非静态，只有在对静态变量时，才显得最简介易懂

### 9.6 友元

- 不属于类，但是放在在类的声明，不就像是一个不是家人的friend正在家里开party吗～
- 友元存在的价值在于虽可以不属于本类，但作为其友元，可以访问其的私有成员变量；友元函数实现多个类的数据共享；
* 下面的程序展示了Date中的友元函数由Time中的成员函数担任，使之能访问两个类的成员，注意访问的方式

```
class Date;//对Date类的提前引用声明
class Time
{
public: Time(int, int, int);
		void display(Date &);
private:int hour;
		int minute;
		int sec;
};
Time::Time(int a, int b, int c)
{
	hour = a;
	minute = b;
	sec = c;
}

class Date
{
public:Date(int, int, int);
	  friend void Time::display(Date &);
private:int month;
		int day;
		int year;
};
Date::Date(int a, int b, int c)
{
	month = a;
	day = b;
	year = c;
}

void Time::display(Date &d)//对Date对象的引用
{
	cout << d.day << ":" << d.month << ":" << d.year << endl;//访问私有成员
	cout << hour << ":" << minute << ":" << sec << endl;
}

int main()
{
	Time t1(10, 13, 56);
	Date d1(12, 25, 2004);
	t1.display(d1);
	return 0;
}
```


- 下面的程序，友元函数不是类的成员函数，通过重载访问两个类的私有数据

```
class Time
{
public: Time(int, int, int);
		friend void display(Time &);
private:int hour;
		int minute;
		int sec;
};
Time::Time(int a, int b, int c)
{
	hour = a;
	minute = b;
	sec = c;
}
class Date
{
public:Date(int, int, int);
	  friend void display(Date &);
private:int month;
		int day;
		int year;
};
Date::Date(int a, int b, int c)
{
	month = a;
	day = b;
	year = c;
}
void display(Date & d)
{
	cout << d.day << "/" << d.month << "/" << d.year << endl;
}
void display(Time & t)
{
	cout << t.hour << "/" << t.minute << "/" << t.sec << endl;
}
int main()
{
	Time t1(10, 13, 56);
	Date d1(12, 25, 2004);
	display(d1);
	display(t1);
	return 0;
}
```

##10.运算符重载

### 10.1 重载的方法

- 函数类型 operator 运算符名称 （形参表列）
- {对运算符的重载处理}
e.g.
int operator + (int a, int b)

```
class Complex
{public:Complex(){real=0;img=0;}
Comlex(double a, double b){real=a;img=b}
Complex operator + (Complex &);
private:double real;
double img;};

Complex Complex::operator + (Complex &c2)
{return Complex(c2.real+this->real,c2.img+img);// 直接返回一个无名对象
}
int main()
{Complex c1(1.2,3.4);
Complex c2(13.5,3.4);
Complex c = c1+c2;//c = c1.operator+(c2)
}
```

不能重载的运算符有5个:
- . | .* | :: | sizeof | ?:

重载不改变运算符优先级，重载运算符参数必须至少包含一个用户自定义的类或struct，否则就失去了重载运算符的意义，而且发生错误。
**一般情况：双目运算符重载为类的友元函数，单目运算符重载为类的成员函数**
以上代码段修改为friend表示为
```
class Complex
{public:Complex(){real=0;img=0;}
Comlex(double a, double b){real=a;img=b}
friend Complex operator + (Complex &, Complex &);
private:double real;
double img;};

Complex Complex::operator + (Complex c1,Complex &c2)
{return Complex(c2.real+c1.real,c2.img+c1.img);// 直接返回一个无名对象
}
int main()
{Complex c1(1.2,3.4);
Complex c2(13.5,3.4);
Complex c = c1+c2;//c = c1.operator+(c2)
}
```
### 重载流运算符

```
istream & operator >> (istream &, 自定义类 &);
ostream & operator << (ostream &, 自定义类 &);
```
重载流运算符函数必须作为友元函数不能作为类的成员函数，原因显而易见，位置已经被stream &占据

```
class Complex
{public:Complex(){real=0;img=0;}
Comlex(double a, double b){real=a;img=b}
friend ostream operator << (ostream &, Complex &);
friend istream operator >> (istream &, Complex &);
private:double real;
double img;};

friend ostream operator << (ostream & os, Complex & c)
{return os<<c.real<<" + "<<c.img<<"i"<<endl;
}
friend istream operator >> (istream & is, Complex & c)
{return is>>c.real>>c.img;
}
int main()
{Complex c1;
Complex c2;
cin>>c2>>c1;//手动输入4个数字
cout<<c1<<c2;//(cout<<c1)return ostream所以可以继续<<c2
}
```

### 构造函数as强制类型转换

构造函数复习（revise）
默认构造函数
- Complex()
- Complex(double, double);
- Complex(Comlex &);//e.g.在类成员函数的声明体中声明一个表示自身的对象 Complex temp(*this),最后return temp，将本身返回

Complex(double r){real=r;img=0;}

Complex(2.5) 比较 int(2.5)
- 可以认为二者都是进行了强制类型转换
- c=c1+int(2.5) false; c=c1+Complex(2.5) true

### 类型转换函数进行类型转换

类型转换函数作用是将一个类对象转换成另一个类型的数据，在Complex类中定义类型转换函数：
```
operator double()
 {return real;}
```
- 抽象一般形式为
```operator 类型名()
   {实现转换的语句}```
*在函数名前面不能指定函数类型，而且函数没有参数*


## 11.继承与派生

C++继承可以增加代码可重用性，继承主要学会使用protected

一个派生类有两个或多个基类的称为多重继承

**class 派生类名：继承方式 基类名**
**{派生类新增加的成员}；**

构建一个派生类包括以下3部分工作：
1. 从基类接收其全部成员
2. 调整从基类接收的成员：声明一个参数和函数名和函数返回值类型都相同的函数覆盖原来基类的函数
3. 声明派生类时增加成员

### 11.1成员的访问属性

基类和派生类的访问属性

公有继承/私有继承/保护继承，基类的私有成员仍然是私有的，公有和保护成员*近似*变为继承的类型

不能通过派生类**对象**引用从私有基类继承的任何成员，只能通过派生类成员函数引用私有基类的公有和保护成员。

继承是对基类公有和保护成员的继承，基类的私有成员只能被基类的成员函数调用。

- 保护成员相当于保险箱，任何外人均不得窥视，只有子女（派生类）才能打开

在派生类中有4中不同的访问属性：
- 公用的：派生类内外都可以访问
- 受保护的：对于本类同私有的，其下一层的派生类可以访问
- 私有的：派生类内可以访问，外不可
- 不可访问的：派生类内和外都无法访问
**总结** 保护（protected)是专门为派生设计的访问权限，基类的保护成员对于本类是私有的，对于保护继承的派生类可以类内访问，不可类外调用（派生类对象访问），这是因为它仍然是‘保护’访问权限，对于本类（派生类）是私有的。

**在实际使用中，常用的是公有继承**

### 11.2 派生类的构造函数和析构函数

基类的构造函数是不能继承的，所以要初始化派生类对象应该同时考虑基类的对象，方法是执行派生类构造函数时，调用基类的构造函数。e.g.
```
Student(int n,string nam,char s){}//基类构造函数，放在基类中
Student_derivedclass(int n,string name, char s, int a, string ad):Student(n,nam,s){}
//本句放在派生类中作为派生类的构造函数
```

### 11.3 有子对象的派生类的构造函数

类定义中含有类的对象，就像结构体中含有结构体
- 子对象的初始化是在建立派生类时通过调用派生类构造函数实现的
定义派生类构造函数的一般形式为
**派生类构造函数名（总参数列表）：基类构造函数名（参数表列），子对象名（参数表列）**
**{派生类中新增数据成员初始化语句}**
下面是一个例子，monitor是子对象，是基类的对象

```
class Student
{public:
 Student(int n, string nam)
 {num = n;
  name=nam;}
 protected:
 int num; string name;
};

class Student1: public Student
{public:
 Student1(int n, string nam, int n1, string nam1, int a, string ad):Student(n,nam),monitor(n1,nam1)
{age = a;
 addr = ad;}//构造函数
private:
 Student monitor;//派生类子对象
 int age;
 string addr;
```

### 11.4 虚基类



## C++ 11//14/17关键字

### using关键字

1. 通常我们使用 typedef 定义别名的语法是：typedef 原名称 新名称;，但是对函数指针等别名的定义语法却不相同。

```
typedef int (*process)(void*);
using process = int(*)(void *);

using TrueDarkMagic = MagicType<std::vector<T>, std::string>;
TrueDarkMagic<bool> you;
```

2. 在传统 C++ 中，构造函数如果需要继承是需要将参数一一传递的，这将导致效率低下。C++11 利用关键字 using 引入了继承构造函数的概念：

```
class BASE{
		public:
				int value1;
				int value2;
				BASE(){
						value1 = 1;
				}
				BASE(int value): BASE(){ //委托BASE()构造函数
						value2 = 2;
				}
};
class subBASE: public BASE{
		using BASE:BASE; //继承构造
};

int main(){
		subBASE s(3);
		std::cout << s.value1<<s.value2<<std::endl;
}
```

## C++11/14/17标准库

### std::array

std::array 保存在栈内存中，std::vector保存在堆内存。

std::array 会在编译时创建一个固定大小的数组，std::array 不能够被*隐式*的转换成指针。

使用 std::array 很简单，只需指定其类型和大小即可

```
std::array<int, 4> arr= {1,2,3,4};
int len = 4;
std::array<int, len> arr = {1,2,3,4}; // *非法*, 数组大小参数必须是常量表达式
```

与C风格接口传参

```
void foo(int *p, int len) {
    return;
}
std::array<int, 4> arr = {1,2,3,4}; //实例

// C 风格接口传参
// foo(arr, arr.size()); // *非法*, 无法隐式转换
foo(&arr[0], arr.size());
foo(arr.data(), arr.size()); //arr.data() return 指向arr的指针
// 使用 `std::sort`
std::sort(arr.begin(), arr.end());
```

### 智能指针

> 智能指针实质是一个对象，行为表现的却像一个指针

> 引用计数不是垃圾回收，引用技术能够尽快收回不再被使用的对象，同时在回收的过程中也不会造成长时间的等待，更能够清晰明确的表明资源的生命周期。

> 这些智能指针包括 std::shared_ptr/std::unique_ptr/std::weak_ptr，使用它们需要包含头文件 <memory>。

> std::shared_ptr 是一种智能指针，它能够记录多少个 shared_ptr 共同指向一个对象，从而无需显示得调用 delete，当引用计数变为零的时候就会将对象自动删除。

1. shared_ptr and unique_ptr 

单个unique_ptr离开作用域时，会立即释放底层内存，既然是独占，换句话说就是不可复制;可以有多个shared_ptr实例指向同一块动态分配的内存，当最后一个shared_ptr离开作用域时，才会释放这块内存.

### C++中正则表达式

正则表达式描述了一种字符串匹配的模式。一般使用正则表达式主要是实现下面三个需求：

1. 检查一个串是否包含某种形式的子串；
2. 将匹配的子串替换；
3. 从某个串中取出符合条件的子串。

--------------------------------------------------
| **特殊字符**  | **描述** |
| :--- | :----: |
| $ | 匹配输入字符串的结尾位置 |
| (,) | 标记一个子表达式的开始和结束位置 |
|* | 匹配前面的子表达式零次或多次。
|+  | 匹配前面的子表达式一次或多次。
|. | 匹配除换行符 \n 之外的任何单字符。
--------------------------------------------------


### 线程与并发

1. 最简单的是使用std::thread创建一个线程实例，这是并发编程的基础，需要包含<thread>头文件，其提供很多基本的线程操作：

*get_id()* required ID of the created thead.

*join()*  add one thread to pool.

```
\#include <iostream>
\#include <thread>
void foo() 
{
    std::cout << "hello world" << std::endl;
}
int main() 
{
    std::thread t(foo); //new one thread
    t.join(); //start the thread, the main funtion waits until the thread ends
    return 0;
}
```

2. std::mutex is the basic class in C++11, mutex variables can be created via instancing std::mutex. <mutex> needs to be included.

member funtion: lock() unlock()

object: std::unique_lock<std::mutex> std::look_guard<std::mutex>


```
\#include <iostream>
\#include <thread>
\#include <mutex>
std::mutex mtx;

void block_area() {
std::unique_lock<std::mutex> lock(mtx);
    //...临界区
lock.unlock();
    //...some other code 
lock.lock(); //  can lock again
}
int main() {
    std::thread thd1(block_area);
    std::thread thd2(block_area);
    thd1.join(); //syn
    thd2.detach(); //asyn
    return 0;
}
```






























