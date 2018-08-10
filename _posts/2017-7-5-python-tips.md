---
layout: post
title: Python Tips
date: 2017-7-5
categories: blog
tags: [技术总结]
description: Using python in Linux
---


## Install python and pip on windows

- Download python.exe and install, at the same time, add C:\Python27 (python install dir) to system env
- Go to [pip download](https://pypi.python.org/pypi/pip#downloads), 解压到随便一个文件夹下，在cmd中找个该解压目录，输入

```
python setup.py install
```

- 添加C:\Python27\Scripts到Windows系统环境变量(need to reboot)
- 安装正确会在Python安装目录中多一个Scripts文件夹，此时cmd中找个Scripts文件夹，然后

```
easy_install.exe pip
```

打开cmd后，键入 pip install numpy

enjoy!


## Python packaging ecosystem

参考一下链接[sholmes](http://shanmo.github.io/2016/08/Python-Packaging-Ecosystem)，系统是Ubuntu 14.04 Python2.7/3.0

## 两个维数相同的bool类型数组做与运算

np.nonzero(ok1 * ok2)[0]

其中ok1和ok2都是一个一维数组bool型的

如果加[0]会使得到的结果仍为一维数组

         > ok1*ok2
         array([False,  True, False, False], dtype=bool)
         > np.nonzero(ok1*ok2)[0]
         array([1])
         > np.nonzero(ok1*ok2)
         (array([1]),)


         > b1*b2
         array([False,  True,  True, False], dtype=bool)
         > np.nonzero(b1*b2)
         (array([1, 2]),)
         > np.nonzero(b1*b2)[0]
         array([1, 2])


## 文本读取打开

使用例如json从文本中读取到的数字得到的是文本， 需要自己加上int、float强制转换

追加文件内容
```
with open(filename,'a') as filepoint:
    filepoint.write(string)
```

读文件
```
with open(filename, 'r') as fout:
    lines = fout.readlines()
for line in lines:
    print line
```

文件打开方式 a append,  w empty then write,  r read

## 两个数组合并/某个添加一列

在shape=（432,2）的数组上加上一列使用 np.hstack()

e.g. 
- pixel = np.zeros((439,2), dtype=)
- s = pixel.shape[:-1]+(1,)
- result = np.hstack((pixel,np.ones(s)))

在shape=（432,2）的数组上加上一列使用 np.colomn_stack(())

- pixel = np.zeros((439,2), dtype=int)
- s = np.ones(439, dtype=int)
- result = np.column_stack((pixel,s))

## 矢量求范数

bearing_b3 是一个n×3的矩阵

'normbearing = np.linalg.norm(bearing_b3, axis=1)[:, np.newaxis]]'

e.g.

         > bearing_b3
         array([[1, 2, 3],
                [4, 5, 6],
                [7, 8, 9],
                [2, 5, 8]])

         > np.linalg.norm(bearing_b3, axis=1)[:, np.newaxis]
         array([[  3.74165739],
                [  8.77496439],
                [ 13.92838828],
                [  9.64365076]])

         > np.linalg.norm(bearing_b3, axis=1)
         array([  3.74165739,   8.77496439,  13.92838828,   9.64365076])

## cv2.Rodrigues

Python: cv2.Rodrigues(src[, dst[, jacobian]]) → dst, jacobian¶

R' = cv2.Rodrigues(R)[0]

Note that: [0]

## 两个大小一致的数组，满足条件的配对

```
results = np.array([7,8,9,10])
dists = np.array([1,2,3,4])
good = dists &lt 2
*array([ True, False, False, False], dtype=bool)*
matches = zip(results[good],good.nonzero()[0])
*[(7, array([0]))]* \# matches is a list
```
## np.array中两个维度相同的数组互相相乘注意

```
ok1 = np.array([ True, False, False, False], dtype=bool)
ok2 = np.array([ True, False, True, False], dtype=bool)
ok1*ok2
*array([ True, False, False, False], dtype=bool)*
np.nonzero(ok1*ok2)
*(array([0]),)*
np.nonzero(ok1*ok2)[0]
*array([0])*
```

## python运算符优先级

从上到下优先级依次递减

![python运算符优先级](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/python_.png)

## argparse

```
parser = argparse.ArgumentParser()
parser.add_argument("path", help="Provide ANS_test path for create_link <ANS_test_path>")
args = parser.parse_args()
path = args.path// path is str
xx.py <path-name> //必须提供此参数，否则程序报错

parser.add_argument("-v","--verbosity", type=int, choices=[0,1,2], help="increase output verbosity")
args = parser.parse_args()
args.verbosity
××.py -v 1

parser.add_argument("-v", "--verbosity", action="count", help="increase output verbosity")
args = parser.parse_args()
××.py -vv --verbosity --verbosity

parser.add_argument("-v", "--verbosity", action="store_true", help="increase output verbosity")
args = parser.parse_args()
if args.verbosity:
   do something
××.py -v
```

如果不指定参数，参数默认为None，与整数比较会报错，可以加上default=

## ord() chr()

ord('a') -> 97 # 返回ascII

chr(97) --> 'a' # return 字符

## list <==> str

string to list
```
str1='123456789'
str2='1.2.3.65'
str3 = 'user1 user2 user3'
list(str1) # ['1', '2', '3', '4', '5', '6', '7', '8', '9']
str2.split('.') # ['1', '2', '3', '65']
str3.split() # 移除string空格 ['user1', 'user2', 'user3']
str1.strip() # 移除空格和回车
```

list to str
''.join(list) #将list中元素组成一个string


## list to array

```
a = np.array([[1,2],[4,5],[2,3]])
此时才能用切片功能,a不能是list，执行下列操作：
plt.plot(a[:,0],a[:,1])
```

numpy.ndarry.tolist #numpy to list

## list.pop()使用

```
l=['a','b','c','d','e','f','g','h','i']
for i in xrange(len(l))
  for j in xrange(i+1, len(l))
    L = l[:]
    L = [v for i, v in enumerate(l) if i not in frozenset((i,j))]
```

删除了元素i，j在list L中

## 小数的舍入

round() 四舍五入
floor() 11.9=>11
ceil()  11.1=>11
int() 直接截去小数部分

## 二维数组按照size排序方法

已知一个二维数组，现在使用每行的第三列数据大小作为依据，将二维数组排序

```
size = p_unsorted[:, 2]
order = np.argsort(size)
p_sorted = p_unsorted[order, :]
```

## Python内置函数 

1. map(func, seq1[, seq2,…]) 
第一个参数接受一个函数名，后面的参数接受一个或多个可迭代的序列，返回的是一个list。如果func为None，作用同zip()。
map相当于

```
def map(f, iterable)
    return [f(x) for x in iterable]
```

```
print map(lambda x , y : x ** y, [2,4,6],[3,2,1])
[8, 16, 6]
print map(None, [2,4,6],[3,2,1])
[(2, 3), (4, 2), (6, 1)]
map(int, (1,2,3))
[1, 2, 3]
map(int, '1234')
[1, 2, 3, 4]
```

2. reduce(func, seq1[,seu2,...])

reduce相当于 

```
def reduce(f, list)
product = 1
for num in list:
    product = f(product,num)
return product
``` 

## multiprocessing Module

Python中一种多进程方法（another way: Threading.Thread())
apply_async(func,args=(),kwds={}, callback=None) 非阻塞式，异步

## divmod(a,b)

相当于（math.floor(a/b), a%b) if a or b is float 和 （a//b, a % b)  if a or b is int

## Numpy使用(import numpy as np)

[Numpy小技巧前10个整理](https://www.machinelearningplus.com/101-numpy-exercises-python/)

```
Create array from 0 to 9
np.arange(10)
>>array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])

Create a 3×3 numpy array of all True’s
np.full((3, 3), True, dtype=bool)
np.ones((3,3), dtype=bool)
>> array([[ True,  True,  True],
>>        [ True,  True,  True],
>>        [ True,  True,  True]], dtype=bool)

Extract all odd numbers from arr
arr = np.array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
arr % 2 == 1
>>array([False,  True, False,  True, False,  True, False,  True, False,True])
arr[arr % 2 == 1]
>>array([1, 3, 5, 7, 9])

Replace all odd numbers in arr with -1
arr[arr % 2 == 1] = -1
>>array([ 0, -1,  2, -1,  4, -1,  6, -1,  8, -1])

Stack arrays a and b vertically
a = np.arange(10).reshape(2,-1)
b = np.repeat(1, 10).reshape(2,-1)
np.vstack([a, b])
np.r_[a, b]
np.concatenate([a, b], axis=0)
>> array([[0, 1, 2, 3, 4],
>>        [5, 6, 7, 8, 9],
>>        [1, 1, 1, 1, 1],
>>        [1, 1, 1, 1, 1]])

Stack the arrays a and b horizontally.
a = np.arange(10).reshape(2,-1)
b = np.repeat(1, 10).reshape(2,-1)
np.hstack([a, b])
np.c_[a, b]
np.concatenate([a, b], axis=1)
>> array([[0, 1, 2, 3, 4, 1, 1, 1, 1, 1],
>>        [5, 6, 7, 8, 9, 1, 1, 1, 1, 1]])

Create the following pattern without hardcoding.
a = np.array([1,2,3])
np.r_[np.repeat(a, 3), np.tile(a, 3)]
>> array([1, 1, 1, 2, 2, 2, 3, 3, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3])
```

## Python file path not related to input file path in terminal

```
#!/usr/bin/env python
import os

print("Path at terminal when executing this file")
# 运行python脚步的路径
print(os.getcwd() + "\n")

print("This file path, relative to os.getcwd()")
print(__file__ + "\n")

print("This file full path (following symlinks)")
full_path = os.path.realpath(__file__)
# python real path
print(full_path + "\n")

print("This file directory and name")
path, filename = os.path.split(full_path)
print(path + ' --> ' + filename + "\n")

print("This file directory only")
print(os.path.dirname(full_path))
```





























