---
layout: post
title: Harxon smart antenna
date: 2018-8-9
categories: blog
tags: [技术总结]
description: Harxon smart antenna TS102 Differential GPS
---

## 使用的GPS接收机简介

- 使用带3G功能的移动站TS102，移动站出厂设置为com1-115200 Baud，输出定位信息和航向(GGA,RMC,VTG,ZDA,HDT),现在硬件连接出来的串口线对应接收机的com1。
- 使用3G网络接入[千寻位置](https://qxwz.com/)提供的差分位置服务，所以无需配置基站，只需配置移动站。

## 配置方法

1. 连接好设备(定向天线，3G天线，电源线)，使用串口助手打开com×
2. 在上电(9-36V)前，保证定向天线及smart antenna处于空旷环境，防止GPS信号被遮挡，此时串口助手显示

        Output from Receiver:

        *****************MODULE SELFTEST*****************
        DTU MODULE: RX 451.12500 MHz
                    TX 451.12500 MHz
                    PRT TRIMTALK
                    BAUD 9600 
                    MODNM 1006D
                    E003.03.01  
        CAN MODULE: $GCAN=CANBUS_29B_250k
                    GC117033626
                    17031603
        GNSS MODULE: "UB280", SW_VER "R4.10Build15042"
        ************************end**************************
        3G MODULE: SIM  #QSS: 1
                    CLASS 2
                    CSQ 
                    IP   ××.×××.×××.×××

3. **正常情况**下，移动运营商会分配一点动态IP给接收机，显示在××××××中，使用命令

        $CFG 3G U   //进入user模式，此时接受机返回 *>ok*
        AT+CGPADDR=1   //返回接收机IP地址，运营商分配的动态IP
        AT#PING="www.baidu.com"  //ping外网，正常会有OK返回

以上如果表现正常，说明接收机(smart antenna) SIM卡没有问题

4. 使用[千寻社区](https://bbs.qxwz.com/read.php?tid=514)提供的工具，Ntrip client测试千寻差分账号是否正常，按照[社区教程](https://bbs.qxwz.com/read.php?tid=453)配置软件，需要登录账号发言后才能下载该软件

5. 接收机可以ping通外网，千寻账号也可以使用，现在使用下面命令配置接收机：

        $CFG NTRIP rtk.ntrip.qxwz.com, 8001 RTCM32_GGB NTRIP NtripLinuxClient *usrname* *passwd*

以上命令无需进入3G用户模式，直接在连接上接收机后即可输入上面的命令

6. 在输出的GGA信息中，你应该能看到第6位变为5或其他(见备注，1代表单点定位)，M后的字符不为0，为接收到的差分数据

        $GNGGA,055339.50,3903.60948753,N,11827.60751943,E,1,07,4.4,38.2096,M,-3.2007,M,00,0000*59

## 备注

#### 配置命令都已回车(windows中/r/n)结束

#### GPS数据解析

$GPGSA:(模式、位置)
- 模式, 定位形式, PRN数字, PDOP位置, HDOP水平精度因子, VDOP垂直精度因子, 检验位

$GPGSV:(卫星、角度)
- 总的GSV语句电文数, 当前GSV语句号, 可视卫星总数, 卫星号, 仰角, 方位角,信噪比, 总和校验数, 回车, 换行

$BDGSV:GPGSV(卫星相关的信息)
- 收到讯号的卫星总数,定位的卫星总数,卫星总数,卫星仰角,方位角,讯号信噪比,校验位

$GNRMC:GPRMC(时间日期、地面速度方向)
- UTC时间, 定位状态,纬度,纬半球,经度,经半球,地面速率,方向,UTC日期,磁偏角,磁偏角方向,模式指示,地面速度信息

$GNGGA:GPGGA(时间、定位质量)
- UTC时间,纬度,纬半球,经度,经半球,定位质量标,卫星数量,水平精确度,天、地水准高度，差分GPS数据等, *, 校验和, 回车、换行



