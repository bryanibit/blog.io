---
layout: post
title: Unix Socket
date: 2017-7-29
categories: blog
tags: [技术总结]
description: tcp socket unix socket
---

# Socket TCP Python实现

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

## can to ros / ros to can

- ROS message经过struct.pack序列化后使用socket发送到某IP的端口上
- Can报文使用socket接收后，经过struct.unpack反序列化后publish到ROS上

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
str = struct.pack("ii", a, b)  
print 'length: ', len(str)          # length:  8  
print str                           # # # #乱码   int==>string stream
print repr(str)                     # '\x14\x00\x00\x00\x90\x01\x00\x00'

str2 = struct.unpack("ii", str)  
print 'length: ', len(str2)          # length:  2  
print str2                           # (20, 400)  流==>int
print repr(str2)                     # (20, 400)  
```

打包之后的数据所占用的字节数与C语言中的struct十分相似:
![format对照表](http://images.cnblogs.com/cnblogs_com/coser/201112/201112171613441943.png)

打包的后的字节顺序默认上是由操作系统的决定的，当然struct模块也提供了自定义字节顺序的功能:
![library对照表](http://images.cnblogs.com/cnblogs_com/coser/201112/20111217161345373.png)

可以查看下列网站寻找 [详细信息](http://blog.csdn.net/w83761456/article/details/21171085)

### python str和repr的区别

略

# Unix Socket Principal Concept

A socket is a thought model and an **application programming interface (API)**. You can create sockets in different "domains" (such as e.g. a "unix socket" or a "internet socket") and of different types of communication (e.g. a "datagram" socket or a "stream" socket) and talk to different recipients, and everything works exactly the same (well, 99%, there are obviously minute differences taht you will have to account for).  
With that in mind, it is clear that sockets do something much more complicated and generally have more overhead than pipes (which are basically no more than a simple memcpy to and from a buffer!), but if you create local sockets (i.e. on the same computer), the operating system usually applies a heavily optimized fast path, so there is really not much of a difference. Sockets are one possible way of **inter-process communication** (shared memory and pipes being examples of alternatives). All at the same time, they are being used for "networking", as explained above.  
When a socket is created, the program has to specify the *address domain* and *socket type*. Two process can communicate each other only if their sockets are of the same type and in the same domain.  
* **domain:**
* Unix domain, two processes share a common file system communicate. The address in Unix domain is a character string which is basically an entry in file system.  
* Internet domain, two processes run on two hosts on the interest communicate. The address in Internet domain referred to IP address(32bit) and a port number(16bit) on that host.  
* Each of these has its own address **format**.  
**socket type**
* Stream sockets, which use TCP (Transmission Control Protocol), which is a reliable, stream oriented protocol.  
* Datagram sockets, which use UDP (Unix Datagram Protocol), which is unreliable and message oriented.  
* Each uses its own communciations protocol.  

- The steps involved in establishing a socket on the **client** side are as follows:  
    1. Create a socket with the *socket()* system call
    2. Connect the socket to the address of the server using the *connect()* system call
    3. Send and receive data. There are a number of ways to do this, but the *simplest* is to use the *read()* and *write()* system calls.

- The steps involved in establishing a socket on the **server** side are as follows:  
    1. Create a socket with the *socket()* system call
    2. Bind the socket to an address using the *bind()* system call. For a server socket on the Internet, an address consists of a port number on the host machine.
    3. Listen for connections with the *listen()* system call
    4. Accept a connection with the *accept()* system call. This call typically **blocks** until a client connects with the server.
    5. Send and receive data

The example can be found in [link](http://www.linuxhowtos.org/C_C++/socket.htm).
