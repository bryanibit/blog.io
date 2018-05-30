---
layout: post
title: Gitlab on Docker
date: 2018-5-26
categories: blog
tags: [技术总结]
description: install docker on ubuntu and deploy gitlab based on it
---

## 安装docker on ubuntu

### Uninstall old versions

Older versions of Docker were called docker or docker-engine. If these are installed, uninstall them:

```
$ sudo apt-get remove docker docker-engine docker.io
```

### Prepare Install

```
$ sudo apt-get update
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```

Add Docker's official GPG key:

```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Verify that you now have the key with the fingerprint 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88, by searching for the last 8 characters of the fingerprint.

```
$ sudo apt-key fingerprint 0EBFCD88

pub   4096R/0EBFCD88 2017-02-22
      Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid                  Docker Release (CE deb) <docker@docker.com>
sub   4096R/F273FCD8 2017-02-22
```


```
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

###  Install Docker CE (Community Edition)

```
$ sudo apt-get update
$ sudo apt-get install docker-ce
```

### Verify Docker CE is installed correctly

```
$ sudo docker run hello-world
```

### 镜像加速

1. 对于使用upstart的系统 (ubuntu 14.04) 而言,编辑**/etc/default/docker**文件,在其中的**DOCKER_OPTS**中添加获得的加速器配置**--registry-mirror=<加速器地址>**, 如:

```
DOCKER_OPTS="--registry-mirror=https://jxus37ad.mirror.aliyuncs.com"
```

重新启动服务。

```
$ sudo service docker restart
```

2. 对于使用systemd的系统 (ubuntu 16.04),用

```
systemctl enable docker
```

启用服务后,编辑**/etc/systemd/system/multi-user.target.wants/docker.service**文件,找到**ExecStart=**这一行,在这行最后添加加速器地址**--registry-mirror=<加速器地址>**, 如:

```
ExecStart=/usr/bin/dockerd --registry-mirror=https://jxus37ad.mirror.aliyuncs.com
```

重新加载配置并且重新启动。
```
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

## Deploy gitlab on docker

参考gitlab官方网站关于docker的[文档](https://docs.gitlab.com/omnibus/docker/README.html)
### pull镜像

```
sudo docker pull gitlab/gitlab-ce:latest
```

### 启动Gitlab

用下面的命令启动一个默认配置的Gitlab。如果我们只在本机测试使用的话，将hostname替换为localhost。如果需要让外部系统也能访问的话使用外网IP地址。

```
sudo docker run --detach \
    --hostname gitlab.example.com \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name gitlab \
    --restart always \
    --volume /srv/gitlab/config:/etc/gitlab \
    --volume /srv/gitlab/logs:/var/log/gitlab \
    --volume /srv/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest
```




