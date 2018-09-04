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

