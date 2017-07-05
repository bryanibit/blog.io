---
layout: post
title: Ubuntu unmet problems
date: 2017-7-5
categories: blog
tags: [总结,知识管理]
description: Linux中的一些问题，总结一下
---


## build-essential

### problem

- sudo apt-get install build-essential
- Reading package lists... Done
- Building dependency tree       
- Reading state information... Done
- Some packages could not be installed. This may mean that you have
- requested an impossible situation or if you are using the unstable
- distribution that some required packages have not yet been created
- or been moved out of Incoming.
- The following information may help to resolve the situation:

- The following packages have unmet dependencies:
-  build-essential : Depends: dpkg-dev (>= 1.13.5) but it is not going to be installed
- E: Unable to correct problems, you have held broken packages.

### Solution

* Run sudo apt-get install libdpkg-perl=1.17.5ubuntu5 to downgrade libdpkg-perl.
* You somehow installed a newer version of the package than what is in the repositories, which is causing problems with other packages.

## clean apt-get cache

        sudo apt-get clean
        cd /var/lib/apt
        sudo mv lists lists.old
        sudo mkdir -p lists/partial
        sudo apt-get clean 
        sudo apt-get update

