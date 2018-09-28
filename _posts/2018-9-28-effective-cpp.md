---
layout: post
title: Effective C++
date: 2018-09-28
categories: blog
tags: [技术总结]
description: Effective c++ 精读
---

# template type deduce

The behaviour and purpose
```
template<typename T>
void f(ParamType param);
f(expr); // deduce T and ParamType from expr
```

## by-reference/pointer 之 lvalue-reference

**Rule** is:

1. by-reference said *const int* is different from *int*. To be honest, both are different anywhere.
2. If expr’s type is a reference, ignore the reference part. 

```
template<typename T>
void f(T& param); // param is a reference
int x = 27; // x is an int
const int cx = x; // cx is a const int
const int& rx = x; // rx is a reference to x as a const int
```

then 

```
f(x); // T is int, param's type is int&
f(cx); // T is const int, param's type is const int&
f(rx); // T is const int, param's type is const int&
```
----------------------------------

```
template<typename T>
void f(const T& param); // param is a const reference
int x = 27; // x is an int
const int cx = x; // cx is a const int
const int& rx = x; // rx is a reference to x as a const int
```

then 

```
f(x); // T is int, param's type is const int&
f(cx); // T is int, param's type is const int&
f(rx); // T is int, param's type is const int&
```

-----------------------------------

```
template<typename T>
void f(T* param); // param is a pointer
int x = 27; // x is an int
const int *cx = &x; // cx is a const int *
```

then 

```
f(&x); // T is int, param's type is int*
f(px); // T is const int, param's type is const int*
```

## by-reference 之 universal-reference

**Rule** is: 

1. If *expr* is an lvalue, both T and ParamType are deduced to be lvalue references.**it’s the only situation in template type deduction where T is deduced to be a reference.**
2. If expr is an rvalue, the “normal” (i.e., the above Case 1) rules apply.

```
template<typename T>
void f(T&& param); // param is a universal reference
int x = 27; // x is an int
const int cx = x; // cx is a const int
const int& rx = x; // rx is a reference to x as a const int
```

then 

```
f(x); // T is int&, param's type is int&
f(cx); // T is const int&, param's type is const int&
f(rx); // T is const int&, param's type is const int&
f(32); // 32 is rvalue, T is int, param's type is int&&
```

## by-value

**Rule** is :

1. if expr’s type is a reference, ignore the reference part.
2. if expr is const, ignore that, too. If it’s volatile, also ignore that

```
template<typename T>
void f(T param); // param is now pass by value
int x = 27; // x is an int
const int cx = x; // cx is a const int
const int& rx = x; // rx is a reference to x as a const int
```

then 

```
f(x); // T is int, param's type is int
f(cx); // T is int, param's type is int
f(rx); // T is int, param's type is int
```

This is because param is an object that’s completely independent of cx and rx—a copy of cx or rx. The fact that cx and rx can’t be modified says nothing about whether param can be. That’s why expr’s constness (and volatileness, if any) is ignored when deducing a type for param.

### Array Arguments

```
const char name[] = "J. P. Briggs";
const char * ptrToName = name; 
template<typename T>
void f(T param); // template with by-value parameter
f(name); // T is deduced to const char *, param is the same.
```

If the above is changed to:

```
template<typename T>
void f(T& param); // template with by-reference parameter
f(name); // T is deduced to const char [13], param is const char (&) [13]
```

- [ ] return size of an array as a compile-time constant.

```
template <typename T, std::size_t N>
constexpr std::size_t arraySize(**T (&) [N]**) noexcept //result availble during compilation
{
	return N;
}
int keyvals [] = {1,2,3,4,5,6,7};
int mapvals [arraySize(keyvals)];
std::array<int, arraySize(keyVals)> mappedVals;
```

### Function Arguments

```
void someFunc(int, double); // someFunc is a function; type is void(int, double)
template<typename T>
void f1(T param); // in f1, param passed by value
template<typename T>
void f2(T& param); // in f2, param passed by ref
f1(someFunc); // param deduced as ptr-to-func; type is void (*)(int, double)
f2(someFunc); // param deduced as ref-to-func; type is void (&)(int, double)
```

## conclusion

```
                   Things to Remember
• During template type deduction, arguments that are references are treated as
non-references, i.e., their reference-ness is ignored.
• When deducing types for universal reference parameters, lvalue arguments get
special treatment.
• When deducing types for by-value parameters, const and/or volatile argu‐
ments are treated as non-const and non-volatile.
• During template type deduction, arguments that are array or function names
decay to pointers, unless they’re used to initialize references.
```
