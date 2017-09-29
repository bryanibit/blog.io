---
layout: post
title: OpenSfM opensource textbook
date: 2017-07-14
categories: blog
tags: [理论梳理]
description: 边看程序，边总结一下原理和代码书写风格
---


<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>


# Need to solved:

- feature.py 238 extract_feature_hahog(image,config)
- hahog's feature descriptor is different from SIFT or SURF, its feature descriptor should sqrt to real desc.

# Other Software

visualSfM, PhotoScan, ReCap 360, RealityCapture, Acute 3D Context Capture, etc.

# Local Feature and Global Feature

DoG: Difference of Gaussian
HoG: Histogram of oriented gradient

- Global descriptors generally used in image retrieval, object detection and classification,
- while the local descriptors used for object recognition/identification.
- contour representations, shape descriptors, and texture features and local features represent the texture in an image patch.
- Shape Matrices, Invariant Moments (Hu, Zerinke), Histogram Oriented Gradients (HOG) and Co-HOG are some examples of global descriptors. 
- SIFT, SURF and FREAK are some examples of local descriptors.

# Feature Point Detection of an Image using Hessian Affine Detector


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

# 三 Feature Detection

- Feature detection returns 3 values: **p_unsorted**, **f_unsorted**, **c_unsorted**
- Corresponding to point positions and other things like size, respond, octave, etc, feature descriptor(n×128), color
- p_unsorted is a matrix of NumFeatures * 6 for SIFT and SURF *4 for HAHOG

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

> homogeneous / np.linalg.norm(homogeneous, axis = 1)[:, np.newaxis]

> 求范数可在在Python tips中找到为何加[:, np.newaxis](https://bryanibit.github.io/blog/2017/07/05/python-tips/)

### 2.1.2 R, t and inliers

> 通过OpenGV找到矫正feature points关系,得到矫正后的2D points: b1 and b2，**算出两视图R和t(with OpenGV)**，及使用到的inliers

### 插播一下R和t求法

求解R和t使用的是对极约束相关内容：

#### 极线 epipolar line

![epipolar line 1](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/epipolar_line1.PNG)
![epipolar line 2](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/epipolar_line2.PNG)

$$X_2^T E X_1 = 0$$

We know $$E X_1 = 0$$ points epipolar line(一副图中的一点乘以E等于另一个视角中的极线)

![epipolar point 1](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/epipolar_point1.PNG)
![epipolar point 2](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/epipolar_point2.PNG)

一个视角中的点乘以E得到的极线在另一个视图中汇聚成一个点，这个点就是极点，由于极点是2自由度，所以rank(E)=2


```
def run_relative_pose_ransac(b1, b2, method, threshold, iterations):
    return pyopengv.relative_pose_ransac(b1, b2, method, threshold, iterations)

def run_relative_pose_optimize_nonlinear(b1, b2, t, R):
    return pyopengv.relative_pose_optimize_nonlinear(b1, b2, t, R)
```

* 由于需要8对点才能计算基本矩阵（相机内参已知的情况下），所以会有Ransac方法和最小二乘法


### 插播一下inliers求法

- 需要通过R，t和事先设定的阈值，删除不满足阈值的feature points: b1 and b2,具体函数为

```
def _two_view_reconstruction_inliers(b1, b2, R, t, threshold):
    p = pyopengv.triangulation_triangulate(b1, b2, t, R)

    br1 = p.copy()
    br1 /= np.linalg.norm(br1, axis=1)[:, np.newaxis]

    br2 = R.T.dot((p - t).T).T
    br2 /= np.linalg.norm(br2, axis=1)[:, np.newaxis]

    ok1 = np.linalg.norm(br1 - b1, axis=1) < threshold
    ok2 = np.linalg.norm(br2 - b2, axis=1) < threshold
    return np.nonzero(ok1 * ok2)[0], p
```
- 解释：p（br1）是3D points 在第一视图坐标系下表示的， br2是3D points 在第二视图坐标系下表示
R, t 是**第二视图变换到第一视图**的坐标变换阵，hence， R×br2 + t = br1 ==> (p-t).dot(R)

> 以上是使用了两种方法计算inliers，RANSAC和非线性优化方法，同时优化R,t

> RANSAC和非线性优化方法：RANSAC是使用最小配置解，然后迭代找最优；Non-linear一次使用所有的点求最优

1. if len(inliers) > 5: Bundle Adjustment 优化

2. triangulation and bundle adjustment

### Triangulation

![Trangulation](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/triangulation.png)

The following is parts of [OpenGV code](https://github.com/laurentkneip/opengv/blob/master/src/triangulation/methods.cpp) for triangulation.
The code is corresponding to the theory.
And the matrixv maybe the most approximate solution for X satifying *AX=0*

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

### coordinate system R, t

There is R, t defined by the following words and figure:

![one coordinate define](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/one_coordinate_define.png)

- I recall we can define the corner system any way we want it for Bob. 
- And we would define the corner system in the first person perspective. That's the world is measured according to Bob's corner system.
- So Bob is the center of the universe. 
- And his X is universe X. His Y pointing down is the universe of Y, and Z pointing into the scene is the Z direction. And, as such, Bob has a simple camera projection matrix, consists of a camera calibration matrix K times identity, followed by a zero column. Mike, camera projection matrix. Consists of calibration matrix K, rotation matrix, and t. 
- So Mike need to take a point in the world coordinate system and transform it through the rotation and translation, into his first person perspective, and then project through the k matrix, into 2D. 

> The R, t can transform 3D points in Bob's first coordinate system to Mike's first coordinate system.

## 2.2 Grow reconstruction


### 2.2.1 3D-2D corresponding (PnP)

pass

## Bundle adjustment

使用[Ceres Solver](http://www.ceres-solver.org)
**CostFunction** depends on the parameter blocks {xi1,...,xik}.

# Name 

后验概率 先验概率
Gaussian Mixture Model
Fisher
似然函数



