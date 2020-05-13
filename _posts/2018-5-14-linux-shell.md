---
layout: post
title: Linux Shell Commands
date: 2018-05-14
categories: blog
tags: [技术总结]
description: Linux shell tips
---

## **Basic Operation**

## Require root privilage

```sh
#!/bin/sh
[ `whoami` = root ] || exec su -c $0 root
ls /opt
```

As above, ls /opt is still in parent process, if no the above command. $0 means the file itself.  
If the file call main.sh. It is the same with `su -c ./main.sh root`.  

## if-else

```sh
if [ -f main.md ] #-f means file exists not directory;
then              #-d means directory
    rm main.md          #-s means file exists and is not empty;  -x means file is executable 
else                    #-w means file is writable; -r means file is readable
    touch main.md
fi
#-------------------------------------
if [ -f main.md ]; then #-f means file exists not directory;
                        #-d means directory
    rm main.md          #-s means file exists and is not empty;  -x means file is executable 
else                    #-w means file is writable; -r means file is readable
    touch main.md
fi
```

## Echo with color

```sh
NONE='\033[00m'
RED='\033[01;31m'
GREEN='\033[01;32m'
YELLOW='\033[01;33m'
echo -e "${RED}Begin Extracting${NONE}!"
echo -e "${GREEN}Finish Partition${NONE}\n"
```

## Variable renew using expression

```sh
export OSRM="$(find ./ -name "*\.osrm")"
echo $OSRM
```

## Command line Arguments

`$0` is the name of the command run (usually the name of the shell script file); `$1` is the first argument, `$2` is the second argument, `$3` is the third argument, etc...  
`echo $*` and `echo $@` print the list of all command line arguments. However, **$*** is one string and **$@** is a list of separate strings for each parameter. `$#` reports the number of command line arguments passed to the shell script program. For example,  

```sh
./myscript a b c
#---------
for i in "$*"
do
   echo $i
done
#---------
# a b c
#---------
for i in "$@"
do
   echo $i
done
#---------
#a
#b
#c
```

## **文件操作**

## 查看两个文件夹不同点

`diff -ruaq dir1 dir2`(如果dir1和dir2中彼此都没有对方文件,那么将以only的形式human提示).  
`diff -rupP file1 file2`（以+ - 的形式显示行的增减，显示的是如何将file1变为file2）.  
* -r: recursive  
* -u: shows line number  
* -p(small): shows differences in C functions  
* -P(capital): in case of multiple files the full path is shown  

## 列出目录项

以时间顺序列表式列出目录项
```sh
ls -lr
```

## 文件查找

*find*是实时查找，如果需要更快的查询，可试试*locate*；与*find*不同，*locate*并不是实时查找。  
*locate*会为文件系统建立索引数据库，如果有文件更新，需要定期执行更新命令来更新索引库:  
```sh
locate string
```

## 查看文件内容

显示文件第一行:  
```sh
$head -1 filename
```

显示文件倒数第五行:  
```sh
$tail -5 filename
```

查看两个文件间的差别:  
```sh
$diff file1 file2
```

## 给文件增加别名

创建符号链接/硬链接:  
```sh
ln cc ccAgain #硬连接；删除一个，将仍能找到；
ln -s cc ccTo #符号链接(软链接)；删除源，另一个无法使用；（后面一个ccTo 为新建的文件）
```

## 重定向与管道

前面成功，则执行后面一条，否则，不执行:**&&**  
前面失败，则后一条执行: **||**

```
ls /proc && echo  suss! || echo failed.
```

## find命令

`find <指定目录> <指定条件> <指定动作>`:  
```sh
find . ! -name "*.txt" -exec rm {} #查找不是txt的文件 并删除所有文件
find . -regex "*.txt" #不忽略大小写  -iregex 忽略大小写
find . -type d #只列出所有目录  -type f 文件 / l 符号链接 / d 目录
find . -atime 7 -type f #最近第7天被访问过的所有文件
find . -atime -7 -type f #最近7天内被访问过的所有文件
find . -cmin -60 #最近60mins更改的文件
find . -atime +7 type f #最近7天前被访问过的所有文件
find . -type f -size +2k #w字 k M G 寻找大于2k的文件
```
搜索当前目录（含子目录，以下同）中，所有文件名以my开头的文件。  

## 正则表达式

[正则表达式是一种用来识别文本模式的符号表示法](https://billie66.github.io/TLCL/book/zh/chap20.html)。

1. grep 程序以这样的方式来接受选项和参数：  
```
grep [options] regex [file...]
```
2. 正则表达式元字符由以下字符组成： 元字符相对于原意字符  
```
^ $ . [ ] { } - ? * + ( ) | \
```
- 注意正则表达式‘^$’（行首和行尾之间没有字符）会匹配空行  
- linux自带一本英文字典在/usr/share/dict中的word中。  
3. 否定意义表达  
如果在正则表示式中的**第一个字符**是一个插入字符，则剩余的字符被看作是不会在给定的字符位置出现的 字符集合。e.g.  
```sh
grep [^bg]zip file.txt #file.txt中包含zip不包含bzip、gzip的段落
```
4. 缩写  
表达式就匹配了所有以字母和数字开头的文件名：  
```sh
grep -h '[A-Za-z0-9]' word*.txt # -h hide filename
```

## -exec命令

如果要执行多个命令，可以将多个命令写成一个脚本，然后-exec调用执行脚本即可  
```sh
-exec ./commands.sh {}
```

## *grep*文本搜索

```
grep [option] <match_patten> <file> // 默认访问匹配行

```

option:
```
    -o 只输出匹配的文本行 VS -v 只输出没有匹配的文本行

    -c 统计文件中包含文本的次数
        grep -c “text” filename

    -n 打印匹配的行号
    -i 搜索时忽略大小写
    -l 只打印文件名
```

E.g:
```sh
grep -R -n "class" <file> #在多级目录中对文本**递归**搜索(程序员搜代码的最爱）
grep -e "class" -e "vitural" <file> #匹配**多个**模式
```

## xargs 命令行参数转换

xargs 能够将输入数据转化为特定命令的命令行参数；这样，可以配合很多命令来组合使用。  
有点像给其他命令加上更多的-a 或者-n 意义或者通过管道加到其他命令上的这种感觉  

## wc统计行和字符的工具

```sh
$wc -l file # 统计行数
$wc -w file # 统计单词数
$wc -c file # 统计字符数
```

## **Linux shell脚本入门之磁盘管理**

查看磁盘空间:`df -h`  
查询当前目录的大小:`du -sh`  

1. 打包和压缩：
```
tar -cvf etc.tar /etc //将/etc文件夹打包归并到一个文件但不压缩
    -c :打包选项
    -v :显示打包进度
    -f :使用档案文件
gzip etc.tar //压缩文件
```
2. 解包:
```
gunzip demo.tar.gz //解压后缀为 .tar.gz的文件 1. 先解压缩，生成**.tar:
tar -xvf demo.tar
```

```
bzip2 -d  demo.tar.bz2
tar -xvf  demo.tar
```

## **Smart Technique about shell**

1. Quickly backup a file:  
```sh
cp filename{,.orig} #backup file called filename.orig
```
2. Delete all files in a folder that don't match a certain file extension:  
```sh
rm !(*.foo | *.bar | *.baz)
```
3. Convert uppercase files to lowercase files:
```sh
rename 'y/A-Z/a-z/' *
```
4. Find duplicate files (based on MD5 hash):
```sh
find -type f -exec md5sum '{}' ';' | sort | uniq --all-repeated=separate -w 33
```

## Autoboot script

1. ros boot automatically(need to complete)
2. renew a gnome windows(need to complete)