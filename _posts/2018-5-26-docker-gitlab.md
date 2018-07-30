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

如果是在arm的电脑上，将上面几行改为

```
$ sudo add-apt-repository \
   "deb [arch=armhf] https://download.docker.com/linux/ubuntu \
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

..1. 对于使用upstart的系统 (ubuntu 14.04) 而言,编辑**/etc/default/docker**文件,在其中的**DOCKER_OPTS**中添加获得的加速器配置**--registry-mirror=<加速器地址>**, 如:

```
DOCKER_OPTS="--registry-mirror=https://jxus37ad.mirror.aliyuncs.com"
```

重新启动服务。

```
$ sudo service docker restart
```

..2. 对于使用systemd的系统 (ubuntu 16.04),用

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
    --hostname 127.0.0.1 \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name gitlab \
    --restart always \
    --volume /srv/gitlab/config:/etc/gitlab \
    --volume /srv/gitlab/logs:/var/log/gitlab \
    --volume /srv/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest
```

* docker端口映射

```
ip:hostport:containerport   #指定ip、指定主机port、指定容器port
ip::containerport           #指定ip、未指定主机port、指定容器port
hostport:container          #未指定ip port、指定主机port、指定容器port
```

可以采用以下的端口映射：

```
sudo docker run --detach \
    --hostname 127.0.0.1 \
    --publish 4430:443 --publish 8000:80 --publish 2200:22 \
    --name gitlab \
    --restart always \
    --volume /srv/gitlab/config:/etc/gitlab \
    --volume /srv/gitlab/logs:/var/log/gitlab \
    --volume /srv/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest
```

进人运行的容器（docker exec -it gitlab /bin/bash）

**Set external_url** :

[ ] For HTTP  external_url "http://gitlab.example.com:8000"

[ ] For HTTPS (notice the https)  external_url "https://gitlab.example.com:8000"

**Set gitlab_shell_ssh_port** :

*gitlab_rails['gitlab_shell_ssh_port'] = 2200*

Following the above example you will be able to reach GitLab from your web browser under *hostIP*:8000 and push using SSH under the port 2200.

使用```sudo gitlab-ctl reconfigure```更新docker配置。

不知道有没有必要：Just allow git to login by ssh. Edit */etc/ssh/sshd_config*, and add this content: *AllowUsers git*.
可以使用以下方法验证git服务器的ssh功能

### 增加内容：git服务器ssh验证

```
ssh -p <port_num> git@192.168.1.254
```

如果以上的结果显示

```
Welcome to GitLab, <UserName>!
Connection to 192.168.1.254 closed.
```

### 一直出现restarting

* 使用```sudo docker logs <container_name>```查看容器状态
* 如果出现

```
exec user process caused "exec format error"
```

说明下载的镜像不正确，不是适合x86或者arm的

## gitlab迁移到another PC

在原来PC上使用一下命令，并copy到新PC上

```
docker save -o gitlab.docker
```

在新PC上使用

```
docker load -i gitlab.docker
```

此时完成image拷贝到新电脑的过程，然后按照**启动Gitlab**中的步骤，启动container for gitlab.

最后一步就是复制数据到新电脑上

将gitlab整个文件夹打包/压缩后复制到新PC。

## Remove and Restore for DOCKER Gitlab installations

### 在原来电脑上需要完成的工作：

* You have run sudo gitlab-ctl reconfigure at least once. 保证配置文件生效，所有配置文件和gitlab配置相同，可以不使用。

```
docker exec -t <container name> gitlab-rake gitlab:backup:create //backup gitlab
cp /etc/gitlab/gitlab-secrets.json ~/   //This file contains the database encryption key, CI/CD variables, and variables used for two-factor authentication.
cp /etc/gitlab/gitlab.rb ~/
cp -r /etc/ssh ~/    //backup ssh key
```

上面的gitlab-secrets.json需要单独备份到本地，如果丢失可能导致：GitLab Runners will lose access to your GitLab server. 
代替地，可以选择将整个/etc/gitlab和/etc/ssh文件夹备份。

### 在新迁移的电脑上需要完成的工作：

* You have installed the exact same version and type (CE/EE) of GitLab Omnibus with which the backup was created. 在[dockerImage](https://hub.docker.com/r/gitlab/gitlab-ce/tags/)上查找一定版本tag，即gitlab/gitlab-ce:{tag_name}。
* GitLab is running. If not, start it using sudo gitlab-ctl start.

```
sudo gitlab-ctl stop unicorn  // Stop the processes that are connected to the database. Leave the rest of GitLab running.
sudo gitlab-ctl stop sidekiq
sudo gitlab-ctl status // verify the two processes are down
sudo gitlab-rake gitlab:backup:restore BACKUP=1493107454_2018_04_25_10.6.4-ce //overwrite the contents of your GitLab database!. 
```

Make sure your backup tarball (1493107454_2018_04_25_10.6.4-ce_gitlab_backup.tar) is in the backup directory described in the *gitlab.rb* configuration *gitlab_rails['backup_path']*. The default is */var/opt/gitlab/backups*.

Restore /etc/gitlab/gitlab-secrets.json and /etc/ssh if necessary as mentioned above.

```
sudo gitlab-ctl restart  //Restart and check GitLab
sudo gitlab-rake gitlab:check SANITIZE=true
```

## Issues on Gitlab install

```docker logs -f gitlab``` shows *ERROR:  relation "plans" does not exist at character 183*.

Use ```docker exec -it gitlab gitlab-ctl restart gitlab-monitor``` to restart gitlab-monitor for shutting down gitlab-monitor to quiet the warning noise. The following command returns *ok: run: gitlab-monitor: (pid 1942) 1s*, no need to reboot and your gitlab server should be OK!
