---
layout: post
title: C++ textbook 下
date: 2017-07-11
categories: blog
tags: [技术总结]
description: 谭浩强的书粗略浏览一下
---

**一下部分高能预警，请细心体会（本书第8~12章）今天看完前10章**
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
因为
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
下面的程序，友元函数不是类的成员函数，通过重载访问两个类的私有数据
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


























