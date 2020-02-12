---
layout: post
title: 文职工作技巧
date: 2018-4-24
categories: blog
tags: [个人总结]
description: word技巧总结
---

# 2017-10-25

[MarkDown模板](https://guides.github.com/pdfs/markdown-cheatsheet-online.pdf)

测试一下一下链接的有效性：

[笔记本触摸板不好用？](https://www.youtube.com/watch?v=f2rfwR-IV-c)

这儿有个小技巧让你的触控板更好用！ 只需手动更换触控板驱动便可以让触控板更加精确，滚动更加顺滑，更可以启用Windows手势操作。 流程说明：

1. 在硬件管理器中找到自己的触控板所用的驱动（Elan或者Synaptic）
2. 下载以下联想提供的驱动文件 Elan 触控板： http://support.lenovo.com/us/en/downl... Synaptic 触控板： Synaptics Trackpads: https://support.lenovo.com/us/en/down...
3. 把下载的文件解压到C:/Drivers
4. 在硬件管理器中右键点击触控板
5. 选择“更新驱动程序”
6. Elan触控板选择ETD.inf；Synaptic触控板选择SynPD.inf
7. 重新启动计算机
8. 感受Windows驱动的流畅和强大功能 已经验证生效的笔记本电脑： Razer Blade Stealth 雷蛇潜行者 Razer Blade 14 雷蛇14寸笔记本 Some Hp Envy devices 一部分HP Envy笔记本 Some HP x360 devices 一部分HP X360笔记本 Most Hp Pavillion devices 大部分HP Pavillion笔记本 Most new Asus Gaming Laptops running Elan drivers 华硕大部分使用Elan触控板的游戏本 Most Dell Inspiron devices 大部分Dell Inspiron笔记本

# 2017-9-10

1. finish graduate docs in the morning
2. check the work chances and drop some resumes in the morning
3. In the afternoon, you should finish 40 questions for work
4. In the evening, you should exercise then you should go to lab and check your thesis for one or 2 hours

# 2017-9-6

## word技巧

### 将方块涂黑或者加对号

插入-符号-字体（Wingdings 2）里面有方框对号，手势等符号

# 2018-5-29

## 设置QC35链接Ubuntu蓝牙Bluetooth

**Deactivate Bluetooth LE**

1. Edit Bluetooth configuration file:
```
sudo vim /etc/bluetooth/main.conf
```
2. Replace:
```
#ControllerMode = dual
```
to:
```
ControllerMode = bredr
```
3. Restart Bluetooth
```
sudo service bluetooth restart
```
4. Go to System Setting > Bluetooth > Sound Setting

## Install and Configure Huawei E353 USB Modem on Ubuntu (used on Thinkpad)

使用```lsusb```确认HUAWEI设备，可以对应[List of USB ID](http://www.linux-usb.org/usb.ids)，查看设备的厂商号和设备号。

/etc/udev/rules.d/ 40-E303Switch.rules

```
# Huawei E303 Fallback-Mode Modeswitch
ATTRS{idVendor}=="12d1", ATTRS{idProduct}=="1f01", RUN+="/bin/E303Modeswitch.sh"
```

/bin/E303Modeswitch.sh:

```
#! /bin/sh
usb_modeswitch -v 0x12d1 -p 0x1f01 -V 0x12d1 -P 0x14dc -M "55534243000000000000000000000011060000000000000000000000000000"
```

## 激活visual studio 2015

[Visual Studio 2015简体中文企业版/专业版下载+有效激活密钥](https://www.cnblogs.com/bwlluck/p/5514424.html)  
Visual Studio Professional 2015简体中文版（专业版）下载地址:[http://download.microsoft.com/download/B/8/9/B898E46E-CBAE-4045-A8E2-2D33DD36F3C4/vs2015.pro_chs.iso](http://download.microsoft.com/download/B/8/9/B898E46E-CBAE-4045-A8E2-2D33DD36F3C4/vs2015.pro_chs.iso) 其中SHA1: 629E7154E2695F08A3C692C0B3F6CE19DF6D3A72  
该中文版激活密钥：```HMGNV-WCYXV-X7G9W-YCX63-B98R2```

Visual Studio Enterprise 2015简体中文版（企业版）下载地址:[http://download.microsoft.com/download/B/8/F/B8F1470D-2396-4E7A-83F5-AC09154EB925/vs2015.ent_chs.iso](http://download.microsoft.com/download/B/8/F/B8F1470D-2396-4E7A-83F5-AC09154EB925/vs2015.ent_chs.iso) 其中SHA1: 4FFA1EE3E2D3337D3EDAE550A3583ABE9C426BEF  
该中文版激活密钥：```HM6NR-QXX7C-DFW2Y-8B82K-WTYJV```

## UltrISO激活注册码

```
名：王涛
域：7C81-1689-4046-626F
```

## Git win 下载

[GitHub地址](https://github.com/waylau/git-for-win)

## Excel Pratices

**shortcut**

1. *ctrl + \*, 比较多列之间每行的不同  
2. 缩放到选取（视图），方便查看选中的区域
3. *ctrl+enter*快速填充， usually replace copy and paste
4. *Alt+↓*快速调出选单，选单只能用于文本格式
5. *shift+mouse拖拽*增加空行

**安全设置**

保护工作表（审阅）：保护单元格，只保护选定的单元格

**数据操作**

数据+模式：```:A21&"-"&A22```  
数据split：需要分隔符分开数据  
mouse变成＋后，可以选择拉到底，也可以选择双击自动到底  
分列（数据）：格式转换（重置格式）
TXT文本导入 - 分列 - 分隔符  

**快速定位的应用**

活动单元格内容为文本-mouse右键-定义名称-左上角下拉选择名称  
公式-根据所选内容创建定义名称-以列明为名称  
F3 shortcut for 粘贴名称对话框  
要打开“名称管理器”对话框，请在“公式”选项卡上的“定义的名称”组中单击“名称管理器”  
开始-查找和选择-定位条件-可见单元格 选择未被隐藏的行（对象、空值、行内容差异单元格等）  

**vlookup(你想要查找的内容, 要查找的位置, 包含要返回的值的区域中的列号, 返回近似或精确匹配-表示为 1/TRUE 或 0/假))**

固定公式中的某个量，用取值符号$A$1，这也称为相对地址
```
=VLOOKUP(B9,人事表!B:G,6,FALSE)
=VLOOKUP(B8,renshi,6,FALSE)
renshi是表名称
```

**rank**
排序  
补充：如果使用开始-排序和筛选-升序，不需要选择多个单元格，只需要放在需要排序的列上，点击升序就可以

**sumif**

**数据透视表**（插入）

光标放在表里，单击插入中的数据透视表，前提是原始数据是字段表，什么数据放在行列就是分类，什么数据放在数值就是汇总，即
```
名称 职务 年龄 工资
A    搞笑 23  2330
......
```

## Stop iphone upgrade automatically

[tvos11.mobileconfig](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/tvOS11.mobileconfig)
