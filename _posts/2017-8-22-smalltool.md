---
layout: post
title: Linux small tools
date: 2017-8-22
categories: blog
tags: [技术总结]
description: linux小工具
---

## install vim

```
sudo apt-get install vim
```

renew a file called .vimrc:

```
set nu
set confirm
set mouse=a
set tabstop=4
set cindent
syntax on
```

## install terminator

```
udo apt-get install terminator
sudo apt-get install dconf-tools
gsettings set org.gnome.desktop.default-applications.terminal exec   /usr/bin/terminator
gsettings set org.gnome.desktop.default-applications.terminal exec-arg "-x"
```

