---
layout: post
title: OpenSfM opensource textbook
date: 2017-07-07
categories: blog
tags: [技术总结]
description: 边看程序，边总结一下原理和代码书写风格
---
# 控制整个工程的参数方法

**renew a file called config.py**
```
import os
import yaml
default_config_yaml = '''
\# Metadata
use_exif_size: yes
default_focal_prior: 0.85'''
def default_config():
    '''Return default configuration
    '''
    return yaml.load(default_config_yaml)
```
---

The above function default_config() return a dict, {'use_exif_size': yes  }

# Incremental reconstruction


## Initialization from 2 images with most matches

**找到两张图共有的feature（feature-id, feature-x-y, feature-color, ）后，执行一下步骤**

### two_view_reconstruction

1. pixel_bearing

> 首先将每张图片对应的feature points通过cv2.undistorPoints(Point,K, distortion).reshape((-1,2))

> 该函数要求Point需要是三维的数组，namely,[[[a,b]],[[c,d]],[[e,f]]]

> 如果是[[],[],[]]这种，用reshape((-1,1,2))

> K为np.array([[a,b,c],[a,b,c],[a,b,c]])

> distortion为np.array([a,b,c,d])

> 将上述函数得到的结果扩展一列，变为齐次坐标homogeneous，然后取范数为1

> homogeneous / np.linalg.norm(b, axis = 1)[:, np.newaxis]

> 求范数可在在Python tips中找到为何加[:, np.newaxis](https://bryanibit.github.io/blog/2017/07/05/python-tips/)

### R, t and inliers

> 通过OpenGV找到矫正feature points关系，算出两视图R和t，及使用到的inliers

> 以上是使用两种方法计算inliers，同时优化R,t

> RANSAC和非线性优化方法：RANSAC是使用最小配置解，然后迭代找最优；Non-linear一次使用所有的点求最优

2. if len(inliers) > 5:Bundle Adjustment 优化

3. triangulation and bundle adjustment

## Grow reconstruction

### 3D-2D corresponding (PnP)

pass

## Bundle adjustment

pass



