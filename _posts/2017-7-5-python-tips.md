---
layout: post
title: Python Tips
date: 2019-7-25
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
For certain version:
```pip install networkx==1.9.1```

enjoy!

## Python packaging ecosystem

参考一下链接[sholmes](http://shanmo.github.io/2016/08/Python-Packaging-Ecosystem)，系统是Ubuntu 14.04 Python2.7/3.0.  
**dist-packages** is a Debian-specific convention that is also present in its derivatives, like Ubuntu.  
Package via *pip* are installed from the package manager and put in ```/usr/local/lib/python2.7/dist-packages```. System packages are located in
```/usr/lib/python2.7/dist-packages``` on Ubuntu. However, if you manually install Python from source, it uses the **site-packages** directory.  

## 两个维数相同的bool类型数组做与运算
```
np.nonzero(ok1 * ok2)[0]
```
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

使用例如json从文本中读取到的数字得到的是文本，需要加上int、float强制转换类型

追加文件内容
```python
with open(filename,'a') as filepoint:
    filepoint.write(string)
```

读文件
```python
with open(filename, 'r') as fout:
    lines = fout.readlines()
for line in lines:
    print line
```

文件打开方式*a append, w empty then write, r read*

## 两个数组合并/某个添加一列

在shape=(432,2)的数组上加上一列使用```np.hstack()```
**e.g.**  
```python
pixel = np.zeros((439,2), dtype=int)
s = pixel.shape[:-1]+(1,)
result = np.hstack((pixel,np.ones(s)))
```

在shape=(432,2)的数组上加上一列使用```np.colomn_stack(())```
```python
pixel = np.zeros((439,2), dtype=int)
s = np.ones(439, dtype=int)
result = np.column_stack((pixel,s))
```

## 矢量求范数

bearing_b3 是一个n×3的矩阵  
```python
normbearing = np.linalg.norm(bearing_b3, axis=1)[:, np.newaxis]]
```

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

```python
results = np.array([7,8,9,10])
dists = np.array([1,2,3,4])
# bitwise operation: 7 & 1 = 1
good = dists & results
# array([ True, False, False, False], dtype=bool)
matches = zip(results[good],good.nonzero()[0])
# matches is a list
# [(7, array([0]))]
```
## np.array中两个维度相同的数组互相相乘注意

```python
ok1 = np.array([ True, False, False, False], dtype=bool)
ok2 = np.array([ True, False, True, False], dtype=bool)
ok1*ok2
# >>>array([ True, False, False, False], dtype=bool)
np.nonzero(ok1*ok2)
# >>>(array([0]),)
np.nonzero(ok1*ok2)[0]
# >>>array([0])
```

## python运算符优先级

从上到下优先级依次递减  
![python运算符优先级](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/python_.png)

## argparse

```python
parser = argparse.ArgumentParser()
parser.add_argument("path", help="Provide ANS_test path for create_link <ANS_test_path>")
args = parser.parse_args()
path = args.path # path is str
# xx.py <path-name> //必须提供此参数，否则程序报错

parser.add_argument("-v","--verbosity", type=int, choices=[0,1,2], help="increase output verbosity")
args = parser.parse_args()
args.verbosity
# ××.py -v 1

parser.add_argument("-v", "--verbosity", action="count", help="increase output verbosity")
args = parser.parse_args()
# ××.py -vv --verbosity --verbosity

parser.add_argument("-v", "--verbosity", action="store_true", help="increase output verbosity")
args = parser.parse_args()
if args.verbosity:
   do something
# ××.py -v
```
**<Note>** 如果不指定参数，参数默认为None，与整数比较会报错，可以加上default=

## ord() chr()

```
ord('a') -> 97 # 返回ascII
chr(97) --> 'a' # return 字符
```

## list <==> str

string to list  
```python
str1='123456789'
str2='1.2.3.65'
str3 = 'user1 user2 user3'
list(str1) # ['1', '2', '3', '4', '5', '6', '7', '8', '9']
str2.split('.') # ['1', '2', '3', '65']
str3.split() # 移除string空格 ['user1', 'user2', 'user3']
str1.strip() # 移除空格和回车
```

list to str  
```python
#将list中元素组成一个string
''.join(list)
```

## list to array

```python
a = np.array([[1,2],[4,5],[2,3]])
# 此时才能用切片功能,a不能是list，执行下列操作：
plt.plot(a[:,0],a[:,1])
```
```python
# numpy to list
numpy.ndarry.tolist
```

## 整数之间的进制转换:
    10进制转16进制: hex(16)  ==>  0x10  
    16进制转10进制: int('0x10', 16)  ==>  16  

## 字符串转整数:
    10进制字符串: int('10')  ==>  10  
    16进制字符串: int('10', 16)  ==>  16  
    16进制字符串: int('0x10', 16)  ==>  16  

## 字节串转整数:
    转义为short型整数: struct.unpack('<hh', bytes(b'\x01\x00\x00\x00'))  ==>  (1, 0)  
    转义为long型整数: struct.unpack('<L', bytes(b'\x01\x00\x00\x00'))  ==>  (1,)  

## 整数转字节串:
    转为两个字节: struct.pack('<HH', 1,2)  ==>  b'\x01\x00\x02\x00'  
    转为四个字节: struct.pack('<LL', 1,2)  ==>  b'\x01\x00\x00\x00\x02\x00\x00\x00'  

## 字符串转字节串:
    字符串编码为字节码: '12abc'.encode('ascii')  ==>  b'12abc'  
    数字或字符数组: bytes([1,2, ord('1'),ord('2')])  ==>  b'\x01\x0212'  
    16进制字符串: bytes().fromhex('010210')  ==>  b'\x01\x02\x10'  
    16进制字符串: bytes(map(ord, '\x01\x02\x31\x32'))  ==>  b'\x01\x0212'  
    16进制数组: bytes([0x01,0x02,0x31,0x32])  ==>  b'\x01\x0212'  

## 字节串转字符串:
    字节码解码为字符串: bytes(b'\x31\x32\x61\x62').decode('ascii')  ==>  12ab  
    字节串转16进制表示,夹带ascii: str(bytes(b'\x01\x0212'))[2:-1]  ==>  \x01\x0212  
    字节串转16进制表示,固定两个字符表示: str(binascii.b2a_hex(b'\x01\x0212'))[2:-1]  ==>  01023132  
    字节串转16进制数组: [hex(x) for x in bytes(b'\x01\x0212')]  ==>  ['0x1', '0x2', '0x31', '0x32']  

## list.pop()使用

```python
l=['a','b','c','d','e','f','g','h','i']
for i in xrange(len(l))
  for j in xrange(i+1, len(l))
    L = l[:]
    L = [v for i, v in enumerate(l) if i not in frozenset((i,j))]
```

删除了元素i，j在list L中

## 小数的舍入

```
round() 四舍五入
floor() 11.9=>11
ceil()  11.1=>11
int() 直接截去小数部分
```

## 二维数组按照size排序方法

已知一个二维数组，现在使用每行的第三列数据大小作为依据，将二维数组排序

```python
size = p_unsorted[:, 2]
order = np.argsort(size)
p_sorted = p_unsorted[order, :]
```

## Python内置函数

**map(func, seq1[, seq2,…])**  
第一个参数接受一个函数名，后面的参数接受一个或多个可迭代的序列，返回的是一个list。如果func为None，作用同zip()。map相当于

```python
def map(f, iterable)
    return [f(x) for x in iterable]
```

```python
print map(lambda x , y : x ** y, [2,4,6],[3,2,1])
[8, 16, 6]
print map(None, [2,4,6],[3,2,1])
[(2, 3), (4, 2), (6, 1)]
map(int, (1,2,3))
[1, 2, 3]
map(int, '1234')
[1, 2, 3, 4]
```

**reduce(func, seq1[,seu2,...])**  
reduce相当于

```python
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

```python
# Create array from 0 to 9
np.arange(10)
# >>array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])

# Create a 3×3 numpy array of all True’s
np.full((3, 3), True, dtype=bool)
np.ones((3,3), dtype=bool)
# >> array([[ True,  True,  True],
# >>        [ True,  True,  True],
# >>        [ True,  True,  True]], dtype=bool)

# Extract all odd numbers from arr
arr = np.array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
arr % 2 == 1
# >>array([False,  True, False,  True, False,  True, False,  True, False,True])
arr[arr % 2 == 1]
# >>array([1, 3, 5, 7, 9])

# Replace all odd numbers in arr with -1
arr[arr % 2 == 1] = -1
# >>array([ 0, -1,  2, -1,  4, -1,  6, -1,  8, -1])

# Stack arrays a and b vertically
a = np.arange(10).reshape(2,-1)
b = np.repeat(1, 10).reshape(2,-1)
np.vstack([a, b])
np.r_[a, b]
np.concatenate([a, b], axis=0)
# >> array([[0, 1, 2, 3, 4],
# >>        [5, 6, 7, 8, 9],
# >>        [1, 1, 1, 1, 1],
# >>        [1, 1, 1, 1, 1]])

# Stack the arrays a and b horizontally.
a = np.arange(10).reshape(2,-1)
b = np.repeat(1, 10).reshape(2,-1)
np.hstack([a, b])
np.c_[a, b]
np.concatenate([a, b], axis=1)
# >> array([[0, 1, 2, 3, 4, 1, 1, 1, 1, 1],
# >>        [5, 6, 7, 8, 9, 1, 1, 1, 1, 1]])

# Create the following pattern without hardcoding.
a = np.array([1,2,3])
np.r_[np.repeat(a, 3), np.tile(a, 3)]
# >> array([1, 1, 1, 2, 2, 2, 3, 3, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3])
```
以上是[Numpy小技巧前10个整理](https://www.machinelearningplus.com/101-numpy-exercises-python/)，请一定掌握！

## Python file path not related to input file path in terminal

```python
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

## How do I write a function with output parameters (call by reference)?

Remember that arguments are **passed by assignment** in Python. Since assignment just creates references to objects, there’s no alias between an argument name in the *caller* and *callee*, and so no call-by-reference. However, there are a number of ways which achieve desired effect.  
1. By returning the tuple of the results:
```python
def f(a, b):
    a = a + 1
    b = b + 2
    return a, b
```
2. By using global variables
3. By passing the mutable (changable in-place) object
```python
# a is list, and list is mutable
def f(a, b):
    a[0] = a[0] + b
```

Then let me show mutable and immutable objects in python:  
**Everything** in Python is an **object**. And every **variable** holds an **object instance**. All objects in Python can be either *mutable* or *immutable*.  
In short, objects of built-in types like (*int, float, bool, str, tuple, unicode*) are immutable. Objects of built-in types like (*list, set, dict*) are mutable. Custom classes are generally mutable. 

## 廖雪峰博客速刷

### 默认参数

```python
def add_end(L=[]):
    L.append('END')
    return L
```
Python函数在**定义**的时候，**默认参数L** 的值就被计算出来了，即[]，因为默认参数L也是一个变量，它指向对象[]，每次调用该函数，如果改变了L的内容，则下次调用时，默认参数的内容就变了，不再是函数定义时的[]了。

**定义默认参数要牢记一点：默认参数必须指向不变对象！**

上面使用None这个不变对象来实现：```def add_end(L=None):```

### 可变参数

可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple。
```python
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```
调用上式可以采用```calc(1,2,3)```的方式，但是如果现在已经有了```num=[1,2,3]```，可以采用这样的方法调用上函数：```calc(*num)```。```*nums```表示把```nums```这个list的所有元素作为可变参数传进去。

### 关键字参数

关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。

```python
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)
person('Bob', 35, city='Beijing')
person('Adam', 45, gender='M', job='Engineer')
```

和可变参数一样，如果已经有了```extra = {'city': 'Beijing', 'job': 'Engineer'}```，可以采用这样的方法调用函数：```person('Jack', 24, **extra)```。```**extra```表示把extra这个dict的所有key-value用关键字参数传入到函数的```**kw```参数，kw将获得一个dict，注意kw获得的dict是extra的一份```拷贝```，对kw的改动**不会**影响到函数外的extra。


### 命名关键字参数

调用者可以传入不受限制的关键字参数,如果只想接受**city**和**job**相关的dict，则person函数的定义改为：
```python
def person(name, age, *, city, job):
    print(name, age, city, job)
```
调用方式如下：
```python
>>> person('Jack', 24, city='Beijing', job='Engineer')
Jack 24 Beijing Engineer
```

### 参数组合

- 在Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。参数定义的顺序必须是：**必选参数、默认参数、可变参数、命名关键字参数和关键字参数**。
- 对于任意函数，都可以通过类似```func(*args, **kw)```的形式调用它，无论它的参数是如何定义的。

## 高级特性

### 切片
```python
L[:3]
L[-2:]
L[:] #复制一个list
```

### 迭代

dict迭代的是key，```for key in d```。如果要迭代value，可以用```for value in d.values()```，如果要同时迭代key和value，可以用```for k, v in d.items()```。

Python内置的enumerate函数可以把一个list变成索引-元素对：

```python
for i, value in enumerate(['A', 'B', 'C']):
    print(i, value)
for x, y in [(1, 1), (2, 4), (3, 9)]:
    print(x, y)
```

### 列表生成式

```python
[x * x for x in range(1, 11)]
[m + n for m in 'ABC' for n in 'XYZ']
[s.lower() for s in L]
```

### 生成器

要创建一个generator。第一种方法很简单，只要把一个列表生成式的[]改成():
```python
L = [x * x for x in range(10)]
g = (x * x for x in range(10))
next(g)
```

第二种方法，如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator：每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行。

```python
def odd():
    print('step 1')
    yield 1
    print('step 2')
    yield(3)
    print('step 3')
    yield(5)

# >>> o = odd()
# >>> next(o)
# step 1
# 1
# >>> next(o)
# step 2
# 3
# >>> next(o)
# step 3
# 5
# >>> next(o)
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
# StopIteration
```
以上函数可以增加```except StopIteration as e:```作为退出条件。

### 迭代器

可以直接作用于for循环的对象统称为可迭代对象：**Iterable**。

一类是集合数据类型，如list、tuple、dict、set、str等。另一类是generator，包括生成器和带yield的generator function。

可以使用isinstance()判断一个对象是否是*Iterable*对象：
```
>>> from collections import Iterable
>>> isinstance([], Iterable)
True
>>> isinstance({}, Iterable)
True
>>> isinstance('abc', Iterable)
True
```
可以被next()函数调用并不断返回下一个值的对象称为迭代器：Iterator。可以使用isinstance()判断一个对象是否是*Iterator*对象：

```
>>> from collections import Iterator
>>> isinstance((x for x in range(10)), Iterator)
True
>>> isinstance([], Iterator)
False
>>> isinstance({}, Iterator)
False
>>> isinstance('abc', Iterator)
False
```

生成器都是Iterator对象，但list、dict、str虽然是Iterable，却不是Iterator。把list、dict、str等Iterable变成Iterator可以使用iter()函数。因为Python的Iterator对象表示的是一个数据流，Iterator对象可以被next()函数调用并不断返回下一个数据，直到没有数据时抛出StopIteration错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过next()函数实现按需计算下一个数据，所以Iterator的计算是惰性的，只有在需要返回下一个数据时它才会计算。

Iterator甚至可以表示一个无限大的数据流，例如全体自然数。而使用list是永远不可能存储全体自然数的。

## 函数式编程

### 返回函数

```
def lazy_sum(*args):
    def sum():
        ax = 0
        for n in args:
            ax = ax + n
        return ax
    return sum
>>> f = lazy_sum(1, 3, 5, 7, 9)
>>> f
<function lazy_sum.<locals>.sum at 0x101c6ed90>
>>> f() #返回的函数并没有立刻执行，而是直到调用了f()才执行
25
```

我们在函数lazy_sum中又定义了函数sum，并且，内部函数sum可以引用外部函数lazy_sum的参数和局部变量，当lazy_sum返回函数sum时，相关参数和变量都保存在返回的函数中，这种称为“闭包（Closure）”的程序结构拥有极大的威力。我们调用lazy_sum()时，每次调用都会返回一个新的函数，即使传入相同的参数。

### 匿名函数

```lambda x: x * x```实际上就是
```
def f(x):
  return x*x
```

关键字**lambda**表示匿名函数，冒号前面的x表示函数参数。匿名函数有个限制，就是只能有一个表达式，不用写return，返回值就是该表达式的结果。

### 装饰器

在代码运行期间动态增加功能的方式，称之为“装饰器”（Decorator）.装饰器的参数是函数，同时返回一个函数。
```python
import functools
def log(func):
  @functools.wraps(func)
  def wrapper(*args, **kw):
    print('call %s():' % func.__name__)
    return func(*args, **kw)
  return wrapper
```
以上是一个decorator，所以接受一个函数作为参数，并返回一个函数。我们要借助Python的@语法，把decorator置于函数的定义处：
```python
@log
def now():
    print('2015-3-25')
```
这样得到的结果是：
```python
call now();
2015-3-25
```
The above is simplified to ```now = log(now)```. But now本身而言，```now.__name__``` becomes ```wrapper```. So the above is changed and add ```@functools.wraps(func)```

If I need to add param to log, then the function will modified to:
```python
import functools
def log(text): # text is "I " here
  def decorator(func): # param is now
    @functools.wraps(func)
    def wrapper(*args, **kw): #now param
      print('%s cal %s' %(text, func.__name__))
      return func(*args, **kw) #let now() to run
    return wrapper
  return decorator
@log("I ")
def now():
  print('2015-3-25')
```
The above decorator is simplified to ```now = log('execute')(now)```

### 偏函数

functools.partial的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单。
```python
int('1234', base = 10) # return 1234
int2 = functools.partial(int, base = 2)
int2('101') #return 5
```
int2仅仅固定base参数，所以```int2('101', base = 10)```同样会返回101.

### 类的私有对象和访问限制

如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线__，在Python中，实例的变量名如果以__开头，就变成了一个私有变量（private），只有内部可以访问，外部不能访问。
```python
class Student(object):
    def __init__(self, name, score):
        self.__name = name
        self.__score = score

    def print_score(self):
        print('%s: %s' % (self.__name, self.__score))
```
- [ ] 在Python中，变量名类似__xxx__的，也就是以双下划线开头，并且以双下划线结尾的，是特殊变量，特殊变量是可以直接访问的，不是private变量，所以，不能用__name__、__score__这样的变量名。
- [ ] 以一个下划线开头的实例变量名，比如_name，这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，当你看到这样的变量时，意思就是，“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。

### 类的继承与多态

```python
class Animal(object):
    def run(self):
        print('Animal is running...')
# inherit from Animal
class Cat(Animal):
    def run(self):
        print('Cat is running...')
    def eat(self):
        print('Cat eats fish.')
class Dog(Animal):
    def run(self):
        print('Dog is running...')
    def eat(self):
        print('Dog eats meat')
```

Polymorphism can be accomplished by this way:
```python
def run_twice(animal):
    animal.run()
```
We could have this result:
```
>>> run_twice(Animal())
Animal is running...
Animal is running...
>>> run_twice(Dog())
Dog is running...
Dog is running...
>>> run_twice(Cat())
Cat is running...
Cat is running...
```

对于一个变量，我们只需要知道它是```Animal```类型，无需确切地知道它的子类型，就可以放心地调用```run()```方法，而具体调用的```run()```方法是作用在*Animal、Dog、Cat*还是其他Animal子类对象上，由运行时该对象的确切类型决定，这就是多态真正的威力：调用方只管调用，不管细节，而当我们新增一种Animal的子类时，只要确保run()方法编写正确，不用管原来的代码是如何调用的。这就是著名的“开闭”原则：

对扩展开放：允许新增Animal子类；对修改封闭：不需要修改依赖Animal类型的run_twice()等函数。

**静态语言 vs 动态语言**

对于静态语言（例如Java, C++）来说，如果需要传入Animal类型，则传入的对象必须是Animal类型或者它的子类，否则，将无法调用run()方法。

对于Python这样的动态语言来说，则**不一定需要传入Animal类型**。我们只需要保证传入的对象有一个run()方法就可以了：
```python
class Timer(object):
    def run(self):
        print('Start...')
```
这就是动态语言的“鸭子类型”，它并不要求严格的继承体系，一个对象只要*“看起来像鸭子，走起路来像鸭子”*，那它就可以被看做是鸭子。

Python的“file-like object“就是一种鸭子类型。对真正的文件对象，它有一个read()方法，返回其内容。但是，许多对象，只要有read()方法，都被视为“file-like object“。许多函数接收的参数就是“file-like object“，你不一定要传入真正的文件对象，完全可以传入任何实现了read()方法的对象。
```python
def readImage(fp):
    if hasattr(fp, 'read'):
        return readData(fp)
    return None
```
从文件流fp中读取图像，我们首先要判断该fp对象是否存在read方法，如果存在，则该对象是一个流。但有read()方法，不代表该fp对象就是一个文件流，它也可能是网络流，也可能是内存中的一个字节流，但只要read()方法返回的是有效的图像数据，就不影响读取图像的功能。

## 获取对象信息

```
>>> type(123)
<class 'int'>
>>> type('str')
<class 'str'>
>>> type(None)
<type(None) 'NoneType'>
>>> type(abs)
<class 'builtin_function_or_method'>
>>> a = Animal()
>>> type(a)
<class '__main__.Animal'>
```
----------------------------------------------

对于class的继承关系来说，使用type()就很不方便。我们要判断class的类型，可以使用isinstance()函数:
```
# object -> Animal -> Dog -> Husky
>>> a = Animal()
>>> d = Dog()
>>> h = Husky()
>>> isinstance(h, Husky)
True
>>> isinstance(h, Dog)
True
>>> isinstance(b'a', bytes)
True
>>> isinstance(123, int)
True
>>> isinstance('a', str)
True
## list or tuple, either is true.
>>> isinstance([1, 2, 3], (list, tuple))
True
>>> isinstance((1, 2, 3), (list, tuple))
True
```

------------------------------------------------

使用```dir()```

如果要获得一个对象的所有**属性**和**方法**，可以使用dir()函数，它返回一个包含字符串的list，比如，获得一个str对象的所有属性和方法:
```
>>> dir('ABC')
['__add__', '__class__',..., '__subclasshook__', 'capitalize', 'casefold',..., 'zfill']
```
其中**__×××__**有特殊用途,比如**__len__**方法返回长度,调用```len()```函数试图获取一个对象的长度，实际上，在len()函数内部，它自动去调用该对象的```__len__()```方法。
```
>>> len('ABC')
3
>>> 'ABC'.__len__()
3
```

- [ ] 我们自己写的类，如果也想用**len(myObj)**的话，就自己写一个```__len__()```方法：
```
>>> class MyDog(object):
...     def __len__(self):
...         return 100
>>> dog = MyDog()
>>> len(dog)
100
```

## Python动态绑定属性和方法

给类的对象绑定属性和方法：
```
## Bind attributes
class Student(object):
    pass
>>> s = Student()
>>> s.name = 'Michael' # 动态给实例绑定一个属性
>>> print(s.name)
Michael
## Bind function
>>> def set_age(self, age): # 定义一个函数作为实例方法
...     self.age = age
...
>>> from types import MethodType
>>> s.set_age = MethodType(set_age, s) # 给实例绑定一个方法
>>> s.set_age(25) # 调用实例方法
>>> s.age # 测试结果
25
```

绑定对象不会影响该类其他对象的属性和方法，如果想给所有该类的对象都绑定属性和方法：
```
# Bind attributes
Student.name = 'Mike'
## Bind function to class
>>> def set_score(self, score):
...     self.score = score
...
>>> Student.set_score = set_score
```

使用```__slot__```限制class可以绑定（添加）的属性，```__slots__```定义的属性仅对当前类实例起作用，对继承的子类是不起作用的。
```
class Student(object):
    __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称
```

### 使用property

Python内置的@property装饰器就是负责把一个方法变成属性调用的：
```
class Student(object):

    @property
    def score(self):
        return self._score

    @score.setter
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value
>>> s = Student()
>>> s.score = 60 # OK，实际转化为s.set_score(60)
>>> s.score # OK，实际转化为s.get_score()
60
>>> s.score = 9999
Traceback (most recent call last):
...
ValueError: score must between 0 ~ 100!
```

## Dict Operations

### new a dict and initialize it

```python
def main():
    '''
    Creating empty Dictionary
    '''
    # Creating an empty dict using empty brackets
    wordFrequency = {}
    # Creating an empty dict using dict()
    wordFrequency = dict()
    print(wordFrequency)
    
    '''
    Creating Dictionaries with literals
    '''                                  
    wordFrequency = {
        "Hello" : 7,
        "hi" : 10,
        "there" : 45,
        "at" : 23,
        "this" : 77
        }
    print(wordFrequency)
 
    '''
    Creating Dictionaries by passing parametrs in dict constructor
    '''
    wordFrequency = dict(Hello =  7, 
                         hi    = 10,
                         there  = 45,
                         at    = 23,
                         this  = 77
                         )
    print(wordFrequency)
    
    '''
    Creating Dictionaries by a list of tuples
    '''
    # List of tuples    
    listofTuples = [("Hello" , 7), ("hi" , 10), ("there" , 45),("at" , 23),("this" , 77)]
    # Creating and initializing a dict by tuple
    wordFrequency = dict(listofTuples)        
    print(wordFrequency)    

    '''
    Creating Dictionary by a list of keys and initialzing all with same value
    '''    
    listofStrings = ["Hello", "hi", "there", "at", "this"]
    # create and Initialize a dictionary by this list elements as keys and with same value 0
    wordFrequency = dict.fromkeys(listofStrings,0 )
    print(wordFrequency)
 
    '''
    Creating a Dictionary by a two lists
    '''         
    # List of strings
    listofStrings = ["Hello", "hi", "there", "at", "this"]
    # List of ints
    listofInts = [7, 10, 45, 23, 77]
    # Merge the two lists to create a dictionary
    wordFrequency = dict( zip(listofStrings,listofInts ))
    print(wordFrequency)
 
if __name__ == "__main__":
    main()
```

### find the max value for some key in a dict

```python
## return a list and l[0] is key and l[1] is value
l = max(stat.items(), key=operator.itemgetter(1))
## dict remove a pair: stat.pop(key, default)
stat.pop(l[0])
```