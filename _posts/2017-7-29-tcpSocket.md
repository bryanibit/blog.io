---
layout: post
title: socket tcp python实现
date: 2017-7-29
categories: blog
tags: [技术总结]
description: tcp connect socket
---

## 连接canet 200T设备

- canet设备的IP端口为：192.168.0.98:40001
- 需要把电脑固定IP到同一个网段

```
   1. sudo gedit /etc/network/interfaces
   2. add the following lines and save:
   auto eth0  
   iface eth0 inet static  
   address 192.168.0.90  
   netmask 255.255.255.0  
   gateway 192.168.0.1
   3. sudo /etc/init.d/networking restart
```

## push_frame(can_id, data)

- 将每针can报文打包为不定长度的字节（13个字节，一个字节8位），append到buffer，
- 这个过程需要锁定buffer，（如何完成锁定的？buffer_mutex和buffer的关系是如何产生的）

## send_buffer

- 锁定buffer
- 将buffer掏空，一个字节一个字节的掏空（在此为何一次取50个can报文【13个字节】）
- data是什么，一次循环加13个字节？
- dump.txt有什么用？

## python struct

- struct.pack
用于将Python的值根据格式符，转换为字符串（因为Python中没有字节(Byte)类型，可以把这里的字符串理解为字节流，或字节数组），函数原型为：struct.pack(format, var1,var2)

- struct.unpack
用于将字节流转换成python数据类型。它的函数原型为：struct.unpack(fmt, string)，该函数返回一个**元组**

```
a = 20  
b = 400   
str = struct.**pack**("ii", a, b)  
print 'length: ', len(str)          # length:  8  
print str                           # 乱码：   
print repr(str)                     # '\x14\x00\x00\x00\x90\x01\x00\x00'

str2 = struct.**unpack**("ii", str)  
print 'length: ', len(str2)          # length:  2  
print str2                           # (20, 400)  
print repr(str2)                     # (20, 400)  
```



