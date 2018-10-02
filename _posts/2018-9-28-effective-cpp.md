---
layout: post
title: Effective C++
date: 2018-09-28
categories: blog
tags: [技术总结]
description: Effective c++ 精读
---

# 1. template type deduce

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

# 2. auto type deduction

## Same with template type deduction

When a variable is declared using *auto*, auto plays the role of **T** in the template, and
the type specifier for the variable acts as *ParamType*.
```
------------lvalue reference-------------
auto x = 27; //auto is deducted to int, x is int
const auto cx = x; //auto is deducted to int, cx is const int
const auto& rx = x; // auto is deducted to int, rx is const int&
--------------universal reference--------
auto&& uref1 = x; // x is int and lvalue, so uref1's type is int&
auto&& uref2 = cx; // cx is const int and lvalue, so uref2's type is const int&
auto&& uref3 = 27; // 27 is rvalue, so uref3's type is int&&
const char name[] = // name's type is const char[13]
----------array and function-------------
"R. N. Briggs";
auto arr1 = name; // arr1's type is const char*
auto& arr2 = name; // arr2's type is const char (&)[13]
void someFunc(int, double); // someFunc is a function; type is void(int, double)
auto func1 = someFunc; // func1's type is void (*)(int, double)
auto& func2 = someFunc; // func2's type is void (&)(int, double)
```
The template type deduction is the same with the auto type deduction. They are essentially
two sides of the same coin. Except for the one way they differ.

```
int x1 = 27;
int x2(27);
int x3 = {27};
int x4{27}
```
The above is the same in c++11 to one result: an int with value 27. And use *auto* to replace the above *int*.
Then the first two is ditto and the last two ones become ```std::initializer_list<T>```. The treatment of braced(means {}) initializers is the only way in which auto type deduction and template type deduction differ. So the only real difference between auto and template type deduction is that auto assumes that a braced initializer represents a ```std::initializer_list```, but template type deduction doesn’t. The evidence is shown the following:
```
template <typename T>
void f(T param);
f({1,2,3}); // fail
template <typename T>
void f(std::initializer_list<T> param);
f({1,2,3}); // OK
```
In C++14, the tale continues. C++14 permits auto to indicate that a function’s return type should be deduced, and
C++14 lambdas may use auto in parameter declarations.
auto in a function return type or a lambda parameter implies template type deduction, not auto type deduction.

# 3. Understand decltype

## decltype type

decltype saves the original type of variable, no complex deduction.
```
Widget w;
const Widget& cw = w;
auto myWidget1 = cw; // auto type deduction(by-value): myWidget1's type is Widget
decltype(auto) myWidget2 = cw; // decltype type deduction: myWidget2's type is const Widget&
```
Just like the above ```myWidget1``` and ```myWidget2```, the value of function returning can not be auto. If not, the reference and anyother things will diminish.

In according to rvalue and lvalue, the function's parameter becomes universal reference.
```
template<typename Container, typename Index> // final
decltype(auto) 															 // C++14
authAndAccess(Container&& c, Index i) 			 // version
{
authenticateUser();
return std::forward<Container>(c)[i];
}
--------------------------------------
template<typename Container, typename Index> // final
auto 																				 // C++11
authAndAccess(Container&& c, Index i) 			 // version
-> decltype(std::forward<Container>(c)[i])
{
authenticateUser();
return std::forward<Container>(c)[i];
}
```

## Conclusion

```
      Things to Remember
 • decltype almost always yields the type of a variable or expression without any modifications.
 • For lvalue expressions of type T other than names, decltype always reports a type of T&.
 • C++14 supports decltype(auto), which, like auto, deduces a type from its initializer, but it performs the type deduction using the decltype rules.
 • auto variables have their type deduced from their initializer,
 ```

# 4. Know how to view deduced types

```std::type_info::name``` mandates that the type be treated as if it had been passed to a template
function as a by-value parameter. e.g.

```
std::cout << typeid(x).name() << '\n'; // display types for
std::cout << typeid(y).name() << '\n'; // x and y
```

Boost TypeIndex library (often written as Boost.TypeIndex) is designed to succeed. And I don't want to expand the content.

# 5. Prefer auto to explicit type declarations

Here, we insert something irrelevant to *auto*. It is how to reserve bidirectional iterators:
```
#include<iterator>
template<class BidirIt>
void my_reverse(BidirIt first, BidirIt last)
{
    typename std::iterator_traits<BidirIt>::difference_type n = std::distance(first, last);//complete written
		//auto n = std::distance(first, last);//correct like ditto
    --n;
    while(n > 0) {
        typename std::iterator_traits<BidirIt>::value_type tmp = *first;//complete written
				//auto tmp = *first;//correct like ditto
        *(first++) = *(--last);
        *last = tmp;
        n -= 2;
    }
}
```
**std::function** is a template in the C++11 Standard Library that *generalizes* the idea of a function pointer. But you must specify the type of function(function signature) to refer to when you create a std::function object. the ```std::function``` approach is generally bigger and slower than the *auto* approach, and it may yield out-of-memory exceptions, too.

```
std::vector<int> v;
unsigned int n = v.size()
```
To be honest, the real returning type of ```v.size()``` is ```std::vector<int>::size_type```. It is the same with *unsigned int* on 32-bit, but different on 64-bit.

```
std::unordered_map<std::string, int> m;
for(std::pair<std::string, int> &p: m){}
```
This seem perfectly reasonable. But std::unordered_map's key is *const*, so it it std::pair<const std::string, int>. That will result in a temporary object producing. Use auto: ```for(const auto& p: m){}```.

The above examples show how	**explicitly specifying** types can lead to **implicit conversions** that you neither want nor expect. If you use ```auto``` as the type of the target variable, you need not worry about mismatches between the type of variable you’re declaring and the type of the expression used to initialize it.

# 6. Use the explicitly typed initializer idiom when auto deduces undesired types.

First of all, we see the example:
```
Widget w;
bool highPriority = feature(w)[5]; //std::vector<bool> feature(Widget);
processWidget(w, highPriority);
```
The code is right, but we can change that to the following:
```
auto highPriority = feature(w)[5];
```
[ ] return T&, auto here is deduced bool& as matter of course. But C++ is not empowered to use ```bool&```. So *highPriority* is ```std::vector<bool>::reference```. One implementation is for such objects to contain a pointer to the machine word holding the referenced bit,
plus the offset into that word for that bit. So the above code has some problems: highPriority references the type function feature returns which is a temporay object and is reserved until the sentence "feature(w)[5] finishs.

```std::vector<bool>::reference``` is an example of a **proxy class**: a class that exists for the purpose of emulating and augmenting the behavior of some other type. As a general rule, “invisible” proxy classes don’t play well with auto. The explicitly typed initializer idiom involves declaring a variable with auto, but casting the initialization expression to the type you want auto to deduce. The code above is modified like that:```auto highPriority = static_cast<bool>(features(w)[5]);```.

## Conclusion

```
				Things to Remember
• “Invisible” proxy types can cause auto to deduce the “wrong” type for an ini‐
tializing expression.
• The explicitly typed initializer idiom forces auto to deduce the type you want
it to have.
• Implicit type deduction is forbidden in C++11 and easy to result in undefined behaviour.
Sometimes, static_cast<> is adopted with auto.
```
