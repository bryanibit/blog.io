---
layout: post
title: OpenSfM Installation
date: 2018-05-14
categories: blog
tags: [技术总结]
description: OpenSfM安装的坑
---

## 系统状态

- OS: Ubuntu 16.04 X86-64(arch)
- 安装了ros kinetic，所以opencv3.0已经存在在ros安装目录的include和lib目录中
- 安装了Python2.7和Python3.5，注意区分pip3和pip，python3和python，opensfm中使用python2
- 安装了eigen，使用apt-get

## 编译安装opengv

## 编译安装ceres solver



### Eigen::map<>()
```
int array[9];
for(int i = 0; i < 9; ++i) array[i] = i;
cout << Map<Matrix3i>(array) << endl;

Output:

0 3 6
1 4 7
2 5 8

```
注意以上map得到的结果是先填充行再填充列的

### Eigen block <> ()

```
/#include <Eigen/Dense>
/#include <iostream>
using namespace std;
int main()
{
  Eigen::MatrixXf m(4,4);
  m <<  1, 2, 3, 4,
        5, 6, 7, 8,
        9,10,11,12,
       13,14,15,16;
  cout << "Block in the middle" << endl;
  cout << m.block<2,2>(1,1) << endl << endl;
  for (int i = 1; i <= 3; ++i)
  {
    cout << "Block of size " << i << "x" << i << endl;
    cout << m.block(0,0,i,i) << endl << endl;
  }
}
```
output:

```
Block in the middle
 6  7
10 11

Block of size 1x1
1

Block of size 2x2
1 2
5 6

Block of size 3x3
 1  2  3
 5  6  7
 9 10 11
```
