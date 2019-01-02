---
layout: post
title: Leetcode
date: 2018-6-13
categories: blog
tags: [技术总结]
description: Laser data Process
---

## Euler and Quaternion

### From Euler to Quaternion:

```
using namespace Eigen;
//Roll pitch and yaw in Radians
float roll = 1.5707, pitch = 0, yaw = 0.707;    
Quaternionf q;
q = AngleAxisf(roll, Vector3f::UnitX())
    * AngleAxisf(pitch, Vector3f::UnitY())
    * AngleAxisf(yaw, Vector3f::UnitZ());
std::cout << "Quaternion" << std::endl << q.coeffs() << std::endl;
```

### From Quaternion to Euler:

```
auto euler = q.toRotationMatrix().eulerAngles (0, 1, 2);
std::cout << "Euler from quaternion in roll, pitch, yaw"<< std::endl << euler << std::endl;
```

### Use Eigen::Isometry3d pose to describe translation and rotation

```
Eigen::Isometry3d pose; //4 by 4 matrix in fact
pose.linear() = q.normalized().toRotationMatrix();
pose(0,3) = pos_x;
pose(1,3) = pos_y;
pose(2,3) = pos_z;
```

## an affine transformation to an isometric transformation

b.rotation() extract the rotation part of the transformation. It involves a SVD, and thus it is read-only. On the left hand side, you have to use .linear():

```
Eigen::AffineCompact3f a;
Eigen::Isometry3f b;
b.translation() = a.translation();
b.linear() = a.rotation();
```

If you know that 'a' is an isometry and only want to cast it to an Isometry 3f, you can simply do:

```
b = a.matrix();
```
