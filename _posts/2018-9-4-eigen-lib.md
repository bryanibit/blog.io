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

For dynamic matrix or vector, the error wil be shown like that, if the matrix or vector are not assigned memory before. The error will be shown:
```
Eigen::DenseCoeffsBase<Derived, 1>::Scalar& Eigen::DenseCoeffsBase<Derived, 1>::operator()(Eigen::Index) 
[with Derived = Eigen::Matrix<double, -1, 1>; Eigen::DenseCoeffsBase<Derived, 1>::Scalar = double; Eigen::Index = long int]:
Assertion `index >= 0 && index < size()'
```

So the right operation is assign memory before assign value, like,
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

## Initialize matrix

1. Initialized with initialized_list
If C++11 is enabled, **fixed-size** column or row vectors of arbitrary size can be initialized by passing an arbitrary number of coefficients:
```cpp
Matrix<int, 5, 1> b {1, 2, 3, 4, 5};// A row-vector containing the elements {1, 2, 3, 4, 5}
Matrix<int, 1, 5> c = {1, 2, 3, 4, 5};// A column vector containing the elements {1, 2, 3, 4, 5}
```
2. Initialized with *std::vector*
The basic format is like ```Matrix (const Scalar *data)``` and *std::vector* has the following characteristics:
```cpp
std::vector::data
T* data();
const T* data() const;
```
Returns **pointer** to the underlying array serving as element storage. The pointer is such that range *[data(); data() + size()]* is always a valid range, even if the container is **empty**.  
In conclusion, we can intialize matrix with the following:  
```cpp
// implicit
std::vector<int> v1{1,2,3};
Eigen::VectorXd v2(v1.data());
Eigen::VectorXd v2(v1.data(), 3);
// explicit
double *pt = &v1[0];
Eigen::map<Eigen::VectorXd> v2(pt, 1);// size = 1
Eigen::VectorXd v2 = Eigen::Map<Eigen::VectorXd, Eigen::Unaligned>(v1.data(), v1.size());
```

In the general case of matrices and vectors with either fixed or **runtime sizes**,  coefficients have to be grouped by rows and passed as **an initializer list of initializer list**:
```cpp
MatrixXd a {  //construct a 2x2 matrix
      {1,2},  // first row
      {3,4}   // second row
};
```

## Coefficient accessors

*m(index)* is not restricted to **vectors**, it is also available for general **matrices**, meaning index-based access in the array of coefficients. This however depends on the matrix's **storage order**.  
The *operator[]* is also overloaded for index-based access **only** in vectors.

## Inheritance of Matrix  

The inheritance diagram for *Matrix* or *Array* looks as follows:
```
EigenBase<Matrix>
  <-- DenseCoeffsBase<Matrix>    (direct access case)
    <-- DenseBase<Matrix>
      <-- MatrixBase<Matrix>
        <-- PlainObjectBase<Matrix>    (matrix case)
          <-- Matrix
          <-- Array
EigenBase<DiagonalMatrix>
  <– DiagonalBase<DiagonalMatrix>
    <– DiagonalMatrix
```

## Storage order for Eigen matrices

All Eigen matrices default to **column-major storage order**, but this can be changed to row-major, see Storage orders.
```cpp
Matrix<int, 3, 4, RowMajor> Arowmajor;
Matrix<int, 3, 4, ColMajor> Arowmajor;
for(int i = 0; i < Arowmajor.size(); ++i){
      std::cout << *(Arowmajor.data() + i) << std::endl;
}
```
Eigen **defaults** to storing the entry in **column-major**. Eigen library may well work best with column-major matrices. 


## pitfall of Eigen
The following 1 to 7 are [**common pitfalls when using Eigen**](https://eigen.tuxfamily.org/dox-devel/TopicPitfalls.html)  

1. Compilation error with template methods  
[The template and typename keywords in C++](https://eigen.tuxfamily.org/dox-devel/TopicTemplateKeyword.html)  
2. Aliasing  
[Aliasing](https://eigen.tuxfamily.org/dox-devel/group__TopicAliasing.html)  
Matrix multiplication is the only operation in Eigen that assumes aliasing by default. What is aliasing? Aliasing means temporary copy type in general. If *matA* is a squared matrix, then the statement *matA = matA * matA*; is safe. All other operations in Eigen assume that there are no aliasing problems, such as *a = a.transpose()* is forbidden. You should add aliasing to it: *a = a.transpose().eval()*.  
3. Alignment Issues (runtime assertion)  
[Explanation of the assertion on unaligned arrays](http://eigen.tuxfamily.org/dox/group__TopicUnalignedArrayAssert.html)  
4. C++11 and the auto keyword  
In short: do not use the auto keywords with Eigen's expressions. In particularlly, do not use the *auto* keyword as a replacement for a *Matrix<>* type.  
```cpp
MatrixXd A = ..., B = ...;
auto C = A*B;
MatrixXd R1 = C;
A = ...;
MatrixXd R2 = C;
```
In this example, the type of C is not a MatrixXd but an abstract expression representing a matrix product and storing **references** to A and B. So R1 ≠ R2.  
5. Header Issues (failure to compile)  
A method in a class may require an additional *#include* over what the class itself requires! For example, if you want to use the *cross()* method on a vector (it computes a cross-product) then you need to:
```cpp
#include<Eigen/Geometry>
```
6. Ternary operator  
In short: avoid the use of the ternary operator (COND ? THEN : ELSE) with Eigen's expressions.  
7. Pass-by-value  
In short, do not use pass-by-value in Eigen. [Passing Eigen objects by value to functions](https://eigen.tuxfamily.org/dox-devel/group__TopicPassingByValue.html)  
Passing objects by value is almost always a very bad idea in C++, as this means useless copies, and one should pass them by reference instead. 