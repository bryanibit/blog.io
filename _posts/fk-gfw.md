---
layout: post
title: How to fly over GFW
date: 2017-07-19
categories: blog
tags: [技术总结]
description: You have nothing to lose but your chains!
---

## **----Salvation lies within.----**

# GoAgent

Download GoAgent which has been employed.(Your own cloud space)

## Setting

### Chrome on Windows

1. Download [xx-net](https://github.com/bryanibit/XX-Net)
2. Upload goagent account to xx-net on chrome
3. Use it

### Firefox on ubuntu with foxproxy

1. Download foxproxy
2. Add new pattern to foxproxy

* Manually add 127.0.0.1:8087 to the pattern  
* Do not use this proxy for internal IP addresses  

3. Pattern Subscription

* Subscription URL: https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt  
* Proxies: add proxies with GoAgent of process 2
* Format: Autoproxy obfuscation: base64  

4. Quick add  
* enabled

## Terminal use

```
  export https_proxy="127.0.0.1:8087"
  export http_proxy="127.0.0.1:8087"
```
The following processes are optional: ```git config --global http.sslVerify false```. Then git config uses proxy agent.  
When you use export, you could use ```curl ip.gs``` to check the ip address.  
Of course, you should install curl with ```sudo apt-get install curl```.  

### Release busy port with GoAgent

**Because GoAgent is busy with port 8087**

```
inin@inin-pc:~$ sudo lsof -i:8087
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
python  2274 root    6u  IPv4  16595      0t0  TCP localhost:8087 (LISTEN)
inin@inin-pc:~$ kill 2274
```

# xx-net

Log in [xx-net](https://github.com/XX-net/XX-Net/wiki/How-to-use) and download source code. And no need to download GoAgent in Baidu cloud.

1. Install the following software
```
sudo apt-get install python-openssl
sudo apt-get install libffi-dev
sudo apt-get install python-gtk2
sudo apt-get install python-appindicator
sudo apt-get install libnss3-tools
```
2. Run background: carry out the command:
```
xx_net.sh start/stop/restart
```
At this time, input ```127.0.0.1:8085``` to browser. Adopt the way of "Firefox on ubuntu with foxproxy" to employ brower setting.

3. AutoBoot when start PC(optional): add the following content to ```/etc/rc.local```:
```
sudo /home/username/xxnet/xx_net.sh start
```
# vps配置

http://blog.csdn.net/boxuerixin/article/details/72393518  
http://www.cnvultr.com/416.html

我还没有试试上面教程，只是我的同学试过，可以使用。

# Shadownsocks setting

## Shadowsocks on ubuntu

1. Install ss-qt5

```
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```
You can also go to [launchpad](https://launchpad.net/) and search .deb file and install it.

2. Renew gui-config.json at any place.

3. Open terminal and input ```ss-qt5```

4. In pop out gui named *connection Manager*, **file -> import Connections from gui-config.json**(choose gui-config.json renewed in the second process)

5. Config brower proxy setting

## Shadowsocks on macOS

refer to the [link](https://lvii.gitbooks.io/outman/content/) and download shadowsocks.

# Google cloud platform vm

GCP is far more expensive than other agencies like bandwagonHost([搬瓦工](https://bandwagonhost.com)), [vultr](https://www.vultr.com/).

## Register account

Register Google cloud platform, renew a project and renew a vm instance in the project, choose *asia area*, choose *single cpu*, and choose system as
```
Debian GNU/Linux 8 (jessie)
amd64 built on 20180611
```
check *allow HTTP and HTTPS* data.

## Install BBR and shadowsocks

Use ssh to log in the VM instance:

```
sudo -i
wget -N --no-check-certificate https://raw.githubusercontent.com/FunctionClub/YankeeBBR/master/bbr.sh && bash bbr.sh install
```
Abort kernel removal? Choose No. Then we reboot the system. Log the system with ssh another time. BBR is abbreviation of "Bottleneck Bandwidth and RTT" which optimize and control packages of **TCP**. BBR is supported above linux kernel 4.9.

```
sudo -i
bash bbr.sh start
wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocksR.sh && chmod +x shadowsocksR.sh
./shadowsocksR.sh
```

Input the password and port ID. And then input "enter" all the time.

The ports of *TCP* and *UDP* In firewall pattern: **default-allow-http** and **default-allow-https** need to be changed to the ports set **above** just now.

One of firewall setting (such as *default-allow-https* and the other is *default-allow-http*) is displayed in detail as:
![default-allow-https1](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/firewall1.PNG)
![default-allow-https2](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/firewall2.PNG)

The complete setting of firewall is shown:
![default-allow-https-complete](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/firewall_whole.PNG)

From the above, we can see that the firewall rule is applied to all of VM instances. Add TCP ports of all of VM instances to *default-allow-https* and *default-allow-https*. In face, we can add different rules to difference instances, we can edit each instance to different web sign(网络标记)，namely, the red squre shows.

Enjoy!

You can also refer to the [link](https://appso.github.io/2017/09/12/bbr/).

## Donation

**If you think this useful for you, you can donate for me. Thank you for your support!**

![weixin](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/wx.jpg) | ![zhifubao](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/zfb.jpg)

