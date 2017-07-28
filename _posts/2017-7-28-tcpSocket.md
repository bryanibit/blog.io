---
layout: post
title: socket tcp python实现
date: 2017-7-13
categories: blog
tags: [技术总结]
description: tcp connect socket
---

## push_frame(can_id, data)

- 将每针can报文打包为不定长度的字节（13个字节，一个字节8位），append到buffer，
- 这个过程需要锁定buffer，（如何完成锁定的？buffer_mutex和buffer的关系是如何产生的）

## send_buffer

- 锁定buffer
- 将buffer掏空，一个字节一个字节的掏空（在此为何一次取50个can报文【13个字节】）
- data是什么，一次循环加13个字节？
- dump.txt有什么用？
