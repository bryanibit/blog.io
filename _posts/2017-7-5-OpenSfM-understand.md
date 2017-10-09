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

**SIFT**: 图像在不同scale上，（不同的scale就是不同的octave，每个octave中图像的长宽是相同的，但是所用的高斯核大小不同）与不同大小的高斯核函数卷积，这样得到高斯模糊图像，相邻的高斯模糊图像做差，得到DoG空间下的结果，查找极值即可。

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

首先将每张图片对应的feature points通过cv2.undistorPoints(Point,K, distortion).reshape((-1,2))

该函数要求Point需要是三维的数组，namely,[[[a,b]],[[c,d]],[[e,f]]]

如果是[[],[],[]]这种，用reshape((-1,1,2)) np.array子函数reshape

K为np.array([[a,b,c],[a,b,c],[a,b,c]])

distortion为np.array([a,b,c,d])

将上述函数得到的结果扩展一列，变为齐次坐标homogeneous，然后取范数为1

homogeneous / np.linalg.norm(homogeneous, axis = 1)[:, np.newaxis]

求范数可在在Python tips中找到为何加[:, np.newaxis](https://bryanibit.github.io/blog/2017/07/05/python-tips/)

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


## delaunay三角剖分算法

- 三角剖分
* 定义：
假设V是二维实数域上的有限点集，边e是由点集中的点作为端点构成的封闭线段, E为e的集合。那么该点集V的一个三角剖分T=(V,E)是一个平面图G，该平面图满足条件：
1.除了端点，平面图中的边不包含点集中的任何点。
2.没有相交边。
3.平面图中所有的面都是三角面，且所有三角面的合集是散点集V的凸包。



- 确定平面点集的delaunay三角
* 定义：
假设T为V的任一三角剖分，则T是V的一个Delaunay三角剖分，当前仅当T中的每个三角形的外接圆的内部不包含V中任何的点

```
input: 顶点列表(vertices)　　　　　　　　　　　　　　　　　　　　  　//vertices为外部生成的随机或乱序顶点列表
output:已确定的三角形列表(triangles)
　　　　初始化顶点列表
　　　　创建索引列表(indices = new Array(vertices.length))　　　　//indices数组中的值为0,1,2,3,......,vertices.length-1
　　　　基于vertices中的顶点x坐标对indices进行sort　　  　　　　　  //sort后的indices值顺序为顶点坐标x从小到大排序（也可对y坐标，本例中针对x坐标）
　　　　确定超级三角形
　　　　将超级三角形保存至未确定三角形列表（temp triangles）
　　　　将超级三角形push到triangles列表
　　　　遍历基于indices顺序的vertices中每一个点　　　　　　　　　  　//基于indices后，则顶点则是由x从小到大出现
　　　　　　初始化边缓存数组（edge buffer）
　　　　　　遍历temp triangles中的每一个三角形
　　　　　　　　计算该三角形的圆心和半径
　　　　　　　　如果该点在外接圆的右侧
　　　　　　　　　　则该三角形为Delaunay三角形，保存到triangles
　　　　　　　　　　并在temp里去除掉
　　　　　　　　　　跳过
　　　　　　　　如果该点在外接圆外（即也不是外接圆右侧）
　　　　　　　　　　则该三角形为不确定        　　　　　　　　　     //后面会在问题中讨论
　　　　　　　　　　跳过
　　　　　　　　如果该点在外接圆内
　　　　　　　　　　则该三角形不为Delaunay三角形
　　　　　　　　　　将三边保存至edge buffer
　　　　　　　　　　在temp中去除掉该三角形
　　　　　　对edge buffer进行去重
　　　　　　将edge buffer中的边与当前的点进行组合成若干三角形并保存至temp triangles中
　　　　将triangles与temp triangles进行合并
　　　　除去与超级三角形有关的三角形
end
```

## Urban 3D Modelling from Video

[网址](http://www.cs.unc.edu/Research/urbanscape/)

 Given structure and motion estimates, dense pixel correspondences can be established and polygonal models of the scene can be generated using frames of the videos for texture-mapping.

 In our current work, the speed of the system is a major consideration. Our algorithms are fast by nature and amenable to GPU implementations. We have achieved real-time performance leveraging both the CPU and the GPU.

**Processing**

The inputs to our processing pipeline are the trajectory of the vehicle and the video sequences. Since the cameras do not overlap, reconstruction is performed on the frames of a single camera as it moves through the scene. The main steps of the processing pipeline are the following:

2D feature tracking: Our GPU implementation of the KLT tracker is used to track features in the image. The features are used in the Structure from Motion computation and in the sparse scene analysis step. The sources for the **KLT tracker** can be downloaded from [here](http://cs.unc.edu/~ssinha/Research/GPU_KLT/).
Pose refinement: Information from the vehicle trajectory is fused with visual measurements via an Extended Kalman Filter to obtain the pose of each camera at each frame.
Sparse scene analysis: The tracked features can be reconstructed in 3D, given the camera poses, to provide valuable information about the scene surfaces and their orientation.
Multi-way plane-sweeping stereo: We use the **plane-sweeping algorithm** for stereo reconstruction. Planes are swept in multiple directions to account for slanted surfaces and a prior probability estimated from the sparse data is used to disambiguate textureless surfaces. Our stereo algorithm is also run on the GPU to take advantage of its efficiency in rendering operations, which are the most costly computations in plane-sweeping stereo.
Depth map fusion: Stereo depth maps are computed for each frame in the previous step in real time. There are large overlaps between adjacent depth maps that should be removed to produce a more economical representation of the scene. The depth map fusion stage combines multiple depth estimates for each pixel of a reference view, enforces visibility constraints and thus improves the accuracy of the reconstruction. The result is one fused depth map that replaces several raw depth maps that cover the same part of the scene.
Model generation: A multi-resolution mesh is generated for each fused depth map and video frames are used for texture-mapping. The camera gain is adjusted within and across video streams so that transitions in appearance are smoother. In addition, partial reconstructions are merged and holes in the model are filled in, if possible.

 ### KLT algorithm

 最初，KLT算法是为了解决图像配准问题（Registration Problem）而提出的，可以表述如下：对于给定的图像F(x)和G(x)，需要找到一个视差向量（Disparity Vector）h使得F(x+h)与G(x)的差异最小

# Name 

后验概率 先验概率
Gaussian Mixture Model
Fisher
似然函数



