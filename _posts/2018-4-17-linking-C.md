---
layout: post
title: 链接 Link
date: 2018-4-17
categories: blog
tags: [技术总结]
description: 计算机基本内容之链接
---

## 静态链接

ld是静态链接器，可重定位目标文件由代码段和数据节组成。链接器ld主要完成：**符号解析**和**重定位**。符号解析就是将符号引用与符号定义联系起来，重定位是符号定义与存储器位置联系起来。

## 目标文件

分为三种：可重定位目标文件（.o），可执行文件，共享文件（.so/.dll）。

## 可重定位目标文件

其有很多类型，以ELF (Executable and Linkable Format)为例，其文件结构为

ELF header、节和描述节的节头部表，每个节都有固定大小的entry（条目），不同节的位置与大小由节头部表确定。

典型的ELF文件中，

```
.text:编译的代码
.rodata:只读数据，比如printf中的string
.data:初始化的全局变量
.bss:未初始化的全局变量，只是占位符，所以better save space
.symtab:符号表，存放定义和引用的函数与全局变量信息
.strtab:字符串表，其内容包含.symtab中的符号表，以及节头部中的节名称。字符串表实际上是以null结尾的字符串
等等
```

注意：这里局部变量存在程序运行的栈中，不在重定位目标文件中。

## 符号和符号表

符号表（在.symtab中）中有三种类型符号

* 没有static的全局变量或函数
* 带有static的本地变量或函数
* 其他模块定义，本模块引用的全局变量或函数

注意：不包含任何局部变量，但是如果局部变量带有static修饰，则在.data中存储而不在栈中存储。

### ELF符号表条目

对于main.c swap.c而言，使用

```
gcc -s main.c swap.c
```
得到main.o swap.o

```
readelf -a main.o
```
可以查看符号表条目，其中value表示该符号相对于所属节的偏移，size是大小，bind表示全局还是本地，这里没有局部变量，Ndx表示所属字段，包括ABS(不该被重定位的符号), UNDEF(未定义的符号，例如extern修饰的变量), COMMON(为初始化的变量，将来变为.bss中的内容), 1(.text), 3(.data)

## 符号解析