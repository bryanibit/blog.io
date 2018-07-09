---
layout: post
title: Linux Shell Tips
date: 2018-05-14
categories: blog
tags: [技术总结]
description: Linux shell tips
---

## Require root privilage

```
#!/bin/sh
[ `whoami` = root ] || exec su -c $0 root
ls /opt
```

As above, ls /opt is still in parent process, if no the above command. $0 means the file itself.

If the file call main.sh. It is the same with ```su -c ./main.sh root```

## if-else

```
if [ -f main.md ]; then //-f means file exists not directory;   -d means directory
    rm main.md          //-s means file exists and is not empty;  -x means file is executable 
else                    //-w means file is writable; -r means file is readable
    touch main.md
fi
```

## 查看两个文件夹不同点

* diff -ruaq dir1 dir2(如果dir1和dir2中彼此都没有对方文件,那么将以only的形式human提示)
