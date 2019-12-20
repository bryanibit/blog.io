---
layout: post
title: Eigen LIB
date: 2018-09-04
categories: blog
tags: [理论梳理]
description: Determined to adopt Eigen. This is logging.
---

## Transform

```
Eigen::Vector3f trans_vec_A;
//note that you have to create a Translation because multiplying a 
//Transform with a vector will _apply_ the transform to the vector
Eigen::Translation<float,3> translation_A(trans_vec_A);
Eigen::Quaternionf rotation_B;
Eigen::Quaternionf rotation_C;
Eigen::Quaternionf rotation_D;
Eigen::Vector3f trans_vec_E;
Eigen::Translation<float,3> translation_E(trans_vec_E);
Eigen::Transform<float,3,Affine> combined = 
      translation_A * rotation_B * rotation_C * rotation_D * translation_E;
```

{Note that} ```combined = A*B*C*D*E```, so combined applied to a *vector v* is ```combined*v = A*B*C*D*E*v = A*(B*(C*(D*(E*v))))```.

## MatrixXd/VectorXd

For dynamic matrix or vector, the error wil be shown like that, if the matrix or vector are not assigned memory before.
```
Eigen::DenseCoeffsBase<Derived, 1>::Scalar& Eigen::DenseCoeffsBase<Derived, 1>::operator()(Eigen::Index) 
[with Derived = Eigen::Matrix<double, -1, 1>; Eigen::DenseCoeffsBase<Derived, 1>::Scalar = double; Eigen::Index = long int]:
Assertion `index >= 0 && index < size()'
```

So the right operation is assign value before assign memory, like,
```cpp
#include <Eigen/Dense>
using namespace Eigen;
MatrixXd A(3,3);
VectorXd B(3);
// the back of << should have 9 elements
A << 1,2,3,4,5,6,7,8,9;
// the back of << should have 3 elements
B << 1,2,3;
```

## pitfall of Eigen
[common pitfalls](https://eigen.tuxfamily.org/dox-devel/TopicPitfalls.html#TopicPitfalls_auto_keyword)
