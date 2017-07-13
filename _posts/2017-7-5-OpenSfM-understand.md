---
layout: post
title: OpenSfM opensource textbook
date: 2017-07-07
categories: blog
tags: [技术总结]
description: 边看程序，边总结一下原理和代码书写风格
---
# 一 控制整个工程的参数方法

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

# 二 Incremental reconstruction


## 2.1 Initialization from 2 images with most matches

**找到两张图共有的feature（feature-id, feature-x-y, feature-color, ）后，执行一下步骤**

### 2.1.1 two_view_reconstruction

- pixel_bearing

> 首先将每张图片对应的feature points通过cv2.undistorPoints(Point,K, distortion).reshape((-1,2))

> 该函数要求Point需要是三维的数组，namely,[[[a,b]],[[c,d]],[[e,f]]]

> 如果是[[],[],[]]这种，用reshape((-1,1,2)) np.array子函数reshape

> K为np.array([[a,b,c],[a,b,c],[a,b,c]])

> distortion为np.array([a,b,c,d])

> 将上述函数得到的结果扩展一列，变为齐次坐标homogeneous，然后取范数为1

> homogeneous / np.linalg.norm(b, axis = 1)[:, np.newaxis]

> 求范数可在在Python tips中找到为何加[:, np.newaxis](https://bryanibit.github.io/blog/2017/07/05/python-tips/)

### 2.1.2 R, t and inliers

> 通过OpenGV找到矫正feature points关系，算出两视图R和t，及使用到的inliers

> 以上是使用两种方法计算inliers，同时优化R,t

> RANSAC和非线性优化方法：RANSAC是使用最小配置解，然后迭代找最优；Non-linear一次使用所有的点求最优

1. if len(inliers) > 5: Bundle Adjustment 优化

### Bundle_single_view
空

2. triangulation and bundle adjustment

### Triangulation
![Trangulation](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/triangulation.png)

The following is [OpenGV code](https://github.com/laurentkneip/opengv/blob/master/src/triangulation/methods.cpp)
```
opengv::triangulation::triangulate(
    const relative_pose::RelativeAdapterBase & adapter,
    size_t index )
{
  translation_t t12 = adapter.gett12();
  rotation_t R12 = adapter.getR12();
  Eigen::Matrix<double,3,4> P1 = Eigen::Matrix<double,3,4>::Zero();
  P1.block<3,3>(0,0) = Eigen::Matrix3d::Identity();
  Eigen::Matrix<double,3,4> P2 = Eigen::Matrix<double,3,4>::Zero();
  P2.block<3,3>(0,0) = R12.transpose();
  P2.block<3,1>(0,3) = -R12.transpose()*t12;
  bearingVector_t f1 = adapter.getBearingVector1(index);
  bearingVector_t f2 = adapter.getBearingVector2(index);

  Eigen::MatrixXd A(4,4);
  A.row(0) = f1[0] * P1.row(2) - f1[2] * P1.row(0);
  A.row(1) = f1[1] * P1.row(2) - f1[2] * P1.row(1);
  A.row(2) = f2[0] * P2.row(2) - f2[2] * P2.row(0);
  A.row(3) = f2[1] * P2.row(2) - f2[2] * P2.row(1);

  Eigen::JacobiSVD< Eigen::MatrixXd > mySVD(A, Eigen::ComputeFullV );
  point_t worldPoint;
  worldPoint[0] = mySVD.matrixV()(0,3);
  worldPoint[1] = mySVD.matrixV()(1,3);
  worldPoint[2] = mySVD.matrixV()(2,3);
  worldPoint = worldPoint / mySVD.matrixV()(3,3);

  return worldPoint;
};
```

## 2.2 Grow reconstruction

### 2.2.1 3D-2D corresponding (PnP)

pass

## Bundle adjustment

使用[Ceres Solver](http://www.ceres-solver.org)




