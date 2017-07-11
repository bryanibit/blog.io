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
 height\=10;
 width\=10;
 length\=10;
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










