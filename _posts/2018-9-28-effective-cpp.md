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
#include <typeinfo>
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

# 7. Distinguish between () and {} when creating objects

What can {} do?

Let try something simple.
```
Widget w1(10);// call Widget ctor with argument 10
Widget w2(); //error, compiler see it to function returning Widget class
Widget w3{}; // calls Widget ctor with no args
```

```
auto i = {1};
typeid(i).name() //i, int
auto j = {1, 2};
typeid(j).name() //St16initializer_listIiE, initializer_list<int>
```
We can see that {multiple element} will be an initializer_list. For example,
```
class Widget {
public:
Widget(int i, bool b);
Widget(int i, double d);
Widget(std::initializer_list<bool> il); // element type is now bool
... // no implicit conversionEntities funcs
};
Widget w{10, 5.0}; // use initializer_list
```
One rule is that {} is not allowed implicit conversion, but I think it it not certain every time. But if the code above is changed to ```Widget(std::initializer_list<std::string> il)```, then ```Widget w{10, 5.0}``` will use ```Widget(int i, double d)``` as ctor.

```std::vector<T>``` has initializer_list ctor, so

```
std::vector v1(10, 20); // 10 20s
std::vector v1{10, 20}; //two elements 10 and 20
```

## conclusion

```
						Things to Remember
• Braced initialization is the most widely usable initialization syntax, it prevents
narrowing conversions, and it’s immune to C++’s most vexing parse.
• During constructor overload resolution, braced initializers are matched to
std::initializer_list parameters if at all possible, even if other construc‐
tors offer seemingly better matches.
• An example of where the choice between parentheses and braces can make a
significant difference is creating a std::vector<numeric type> with two
arguments.
• Choosing between parentheses and braces for object creation inside templates
can be challenging.
```

## Prefer nullptr to 0 and NULL

It is the blending of NULL and 0 that C++98 programmers avoid overloading on pointer and
integral types. ```“I’m calling f with NULL -- the null pointer”``` and its actual meaning ```“I’m calling f with
some kind of integrals -- not the null pointer”```. NULL has a integer type and nullptr’s advantage is that it doesn’t have an integral type.
You can think **nullptr** as a point of all type.

nullptr is derived from ```std::nullptr_t```, which can convert to any raw pointers.
```
void* a = nullptr;
a == 0; // true
int a = 0;
a == nullptr; //error: invalid operands of types ‘int’
              //and ‘std::nullptr_t’ to binary ‘operator==’
```
## conclusion

```
				Things to Remember
• Prefer nullptr to 0 and NULL.
• Avoid overloading on integral and pointer types.
```

# 9. Prefer alias declarations to typedefs

```
//a unique_ptr
typedef std::unique_ptr<std::unordered_map<std::string, std::string>> UPtrMapSS; //c++ 98
using UPtrMapSS = std::unique_ptr<std::unordered_map<std::string, std::string>>; //c++ 11
//FP is a synonym for a pointer to a function taking an int and
// a const std::string& and returning nothing
typedef void (*FP)(int, const std::string&)
using FP = void (*)(int, const std::string&);
```

## alias template

```
//set structure member saving typedef objects
template<typename T> // MyAllocList<T>::type is synonym for std::list<T,MyAlloc<T>>
struct MyAllocList {
typedef std::list<T, MyAlloc<T>> type; //type could change to any name you like
};
MyAllocList<Widget>::type lw; // client code
---------------------------
template<typename T>
using MyAllocList = std::list<T, MyAlloc<T>>; // MyAllocList<T> is synonym for std::list<T,MyAlloc<T>>
MyAllocList<Widget> lw; // client code
```

If you want to use typedef inside a template for purpose of creating a linked list holding
objects of a type specified by a template parameter, you need to precede the **typedef name** with ```typename```.
For instance,
```
template <typename T>
class Widget{
	private:
		typename MyAllocList<T>::type lw;// structure member type
		...
};
```
Here, MyAllocList<T>::type refers to a type that’s dependent on a template type
parameter (T). MyAllocList<T>::type is thus a *dependent type*, and one of C++’s
many endearing rules is that the names of dependent types must be preceded by type
name.

If MyAllocList is defined as an alias template, this need for typename vanishes (as
does the cumbersome “::type” suffix):
```
template <typename T>
class Widget{
	private:
	MyAllocList<T> lw;
	...
}
```
Alias template must name a type, ```MyAllocList<T>``` above is thus a *non-dependent* type. A **typename**
specifier is neither required nor permitted. However compiler can not know ```MyAllocList<T>::type```, which
just a structure member object, so need **typename** to tell compiler it refers to a type.

## header file <type_traits>

```
std::remove_const<T>::type // yields T from const T
std::remove_reference<T>::type // yields T from T& and T&&
std::add_lvalue_reference<T>::type // yields T& from T
```

C++14 add something simplified:
```
std::remove_const<T>::type 					// C++11: const T → T
std::remove_const_t<T>     					// C++14 equivalent
std::remove_reference<T>::type 			// C++11: T&/T&& → T
std::remove_reference_t<T>     			// C++14 equivalent
std::add_lvalue_reference<T>::type  // C++11: T → T&
std::add_lvalue_reference_t<T>      // C++14 equivalent
```
The above is achieved by :
```
template <class T>
using remove_const_t = typename remove_const<T>::type;
template <class T>
using remove_reference_t = typename remove_reference<T>::type;
template <class T>
using add_lvalue_reference_t = typename add_lvalue_reference<T>::type;
```

## conclusion

```
				Things to Remember
• typedefs don’t support templatization, but alias declarations do.
• Alias templates avoid the “::type” suffix and, in templates, the “typename”
prefix often required to refer to typedefs.
• C++14 offers alias templates for all the C++11 type traits transformations.
```

# 10. Prefer scoped enum s to unscoped enum s

```
enum Color { black, white, red }; // unscoped enum
Color c = red;
--------------------------------
enum class Color { black, white, red }; // enum is now scoped
Color c = Color::red;
c > 14.5;//error, c is class, can not compare with int
static_cast<int>(c) > 14.5; // OK
```
In practice, the declaration and definition of enum are separated:
```
enum class Status; // forward declaration(.h)
enum class Status{one, two, three}; // definition(.cc)
```
Here, *Status*'s underlying type is int. We can specify it using:```enmu class Status:std::uint32_t{one, two}```(combine definition and declaration from <cstdin>). We can often use enum as int number, for example:
```
// unscoped enum
using UserInfo = std::tuple<std::string, std::string, std::size_t>
enum UserInfoFields { uiName, uiEmail, uiReputation };
UserInfo uInfo;
auto val = std::get<uiEmail>(uInfo);//tuple member function get<T>()
---------------------------------------
// scoped enum
using UserInfo = std::tuple<std::string, std::string, std::size_t>
enum class UserInfoFields { uiName, uiEmail, uiReputation };
UserInfo uInfo;
auto val = std::get<static_cast<std::size_t>(UserInfoFields::uiEmail)>(uInfo);//tuple member function get<T>()
```
In fact, there is a function which access **enum**’s underlying type via the ```std::underlying_type``` type trait.
```
template<typename E>
constexpr typename std::underlying_type<E>::type//C++11
//constexpr typename std::underlying_type_t<E>  //C++14
//constexpr auto                                //C++14
toUType(E enumerator) noexcept
{
return
static_cast<typename std::underlying_type<E>::type>(enumerator);
}
```

So ```auto val = std::get<toUType(UserInfoFields::uiEmail)>(uInfo)``` replaces the above ```static_cast<std::size_t>```.

## conclusion

```
				Things to Remember
• C++98-style enums are now known as unscoped enums.
• Enumerators of scoped enums are visible only within the enum. They convert
to other types only with a cast.
• Both scoped and unscoped enums support specification of the underlying type.
The default underlying type for scoped enums is int. Unscoped enums have no
default underlying type.
• Scoped enums may always be forward-declared. Unscoped enums may be
forward-declared only if their declaration specifies an underlying type.
```

# 11. Prefer deleted functions to private undefined ones

In C++98, declaring class member functions *private* prevents clients(class objects) from calling them.
Deliberately failing to define them means that if code that still has access to them (i.e., member
functions or friends of the class) uses them, linking will fail due to missing function definition.

In C++11, there’s a better way to achieve essentially the same end: use ```= delete``` to
mark the copy constructor and the copy assignment operator as **deleted functions**.

An important advantage of deleted functions is that *any function* may be deleted,
while only member functions may be private.

```
bool isLucky(int number); // original function
bool isLucky(char) = delete; // reject chars
bool isLucky(bool) = delete; // reject bools
bool isLucky(double) = delete; // reject doubles and floats
```

Delete a template instantiation inside a class:
```
// global template
template<typename T>
void processPointer(T* ptr);
template<>
void processPointer<void>(void*) = delete;
----------------------------------------
// class template functions
class Widget {
public:
...
template<typename T>
void processPointer(T* ptr)
{ ... }
...
};
template<>
void Widget::processPointer<void>(void*) = delete;
```
template specializations must be written at namespace scope, not class scope.
They can be deleted outside the class(hence at namespace scope).

## Conclusion

```
				Things to Remember
• Prefer deleted functions to private undefined ones.
• Any function may be deleted, including non-member functions and template
instantiations.
```

# 12. Declare overriding functions override

For overriding to occur, several requirments must be met:
• The base class function must be virtual.
• The base and derived function names must be identical (except in the case of
destructors).
• The parameter types of the base and derived functions must be identical.
• The constness of the base and derived functions must be identical.
• The return types and exception specifications of the base and derived functions
must be compatible.
• The functions’ reference qualifiers must be identical.

```
The last requirment points:
class Widget {
public:
...
void doWork() &; // this version of doWork applies only when *this is an lvalue
void doWork() &&; // this version of doWork applies only when *this is an rvalue
};
```

A policy of using override on all your **derived class** overrides can do more than just
enable compilers to tell you when would-be overrides aren’t overriding anything.
```
class Base {
public:
virtual void mf1() const;
virtual void mf2(int x);
virtual void mf3() &;
virtual void mf4() const;
};
class Derived: public Base {
public:
virtual void mf1() const override;
virtual void mf2(int x) override;
virtual void mf3() & override;
void mf4() const override;
};
```

Let me show you reference qualifiers:
```
class Widget {
public:
using DataType = std::vector<double>;
...
DataType& data() &
{ return values; } // for lvalue Widgets, return lvalue
DataType data() && // for rvalue Widgets, return rvalue
{ return std::move(values); }
...
private:
DataType values;
};
Widget w;
auto val1 = w.data();//calls lvalue overload for Widget::data, copy-constructs for val1
auto val2 = makeWidget().data();//calls rvalue overload for Widget::data, move-construct for val2
```

## Conclusion

```
				Things to Remember
• Declare overriding functions override.
• Member function reference qualifiers make it possible to treat lvalue and rvalue objects (*this) differently.
```

# 13. Prefer const_iterators to iterators

```const_iterator```s simply don't convert to ```iterator```s.

C++11 added the **non-member** function ```begin``` and ```end```, but it failed to add ```cbegin```,  ```rbegin```,
```rend```, ```crbegin``` and ```crend```. For example, you can write non-memnber *cbegin* youselves:
```
template <typename C>
auto cbegin(const C& container)
-> decltype(std::begin(container))
{
	return std::begin(container);
}
```

In C++11, std::vector has member function *cbegin* and *cend*:
```
#include <iostream>
#include <algorithm>//std::find
#include <vector>
#include <typeinfo>
#include <iterator>
int main(){
	std::vector<int> v{1,2,3,4,5};
	// auto ite is also OK
	std::vector<int>::const_iterator ite = std::find(v.cbegin(), v.cend(), 3);
	std::cout << "ite type is " << typeid(ite).name() << std::endl;
	v.insert(ite, 2);
	for(auto &p: v)
		std::cout << "v is " << p << std::endl;
	return 0;
}
```

If you change compiler to C++14, the following is OK:
```
template <typename C, typename T>
void findAndInsert(C& container, const T& valueFind, const T& valueInsert)
{
	//non-member function
	auto place = std::find(std::cbegin(container), std::cend(container), valueFind);
	container.insert(place, valueInsert);
}
```

## Conclusion

```
				Things to Remember
• Prefer const_iterators to iterators.
• In maximally generic code, prefer non-member versions of begin, end,
rbegin, etc., over their member function counterparts.
```

# 14. Declare functions noexcept if they won’t emit exceptions

# 15. Use constexpr whenever possible

Conceptually, constexpr indicates a value that’s not only constant, it’s known during compilation. I’ll just say that you can’t assume that the results of constexpr functions are const, nor can you take for granted that their values are known during compilation.

*constexpr* objects are, in fact, *const*, have values at compile time. ((Technically, their values are
determined during translation, and translation consists not just of compilation but also of linking.)
```
int a = 2;
const auto b = a; //find, b is const copy of a
constexpr auto c = a; //error, value not known at compilation time
```
all **constexpr** objects are const, but not all **const** objects are constexpr.

```
constexpr int pow(int base, int exp) noexcept {}
constexpr auto numConds = 5;
std::array<int, pow(3, numConds)> results; //std::array allocates memory at compilation time
```
If *base* and *exp* are compile-time constants, ```pow```’s result may be used as a
compile-time constant. If base and/or exp are not compile-time constants, ```pow```’s
result will be computed at runtime.

If params of constexpr functions can be specified at compilation time, them the function should
be computed at compilation time. C++11, constexpr member functions are implicitly const(const member functions
are forbidden *this).
And constexpr functions are limited to taking and returning *literal types*. (void is not)

## Conclusion

```
				Things to Remember
• constexpr objects are const and are initialized with values known during
compilation.
• constexpr functions can produce compile-time results when called with
arguments whose values are known during compilation.
• constexpr objects and functions may be used in a wider range of contexts
than non-constexpr objects and functions.
• constexpr is part of an object’s or function’s interface.
```

# 16. Make const member functions thread safe

Operations on ```std::atomic``` variables are often less expensive than **mutex acquisition and release**, you may be tempted to lean on std::atomics more heavily than you should.
```
class Point { // 2D point
public:
...
double distanceFromOrigin() const noexcept
{
	++callCount; // atomic increment
	return std::sqrt((x * x) + (y * y));
}
private:
	mutable std::atomic<unsigned> callCount{ 0 };
	double x, y;
};
```

There’s a lesson here. For a single variable or memory location requiring synchroni‐
zation, use of a std::atomic is adequate, but once you get to two or more variables
or memory locations that require manipulation as a unit, you should reach for a
mutex.
```
class Widget {
public:
...
int magicValue() const
{
std::lock_guard<std::mutex> guard(m);// lock m
if (cacheValid) return cachedValue;
else {
	auto val1 = expensiveComputation1();
	auto val2 = expensiveComputation2();
	cachedValue = val1 + val2;
	cacheValid = true;
	return cachedValue;
			}
}// unlock m
...
private:
mutable std::mutex m;
mutable int cachedValue; // no longer atomic
mutable bool cacheValid{ false }; // no longer atomic
};
```

## Conclusion

```
				Things to Remember
• Make const member functions thread safe unless you’re certain they’ll never
be used in a concurrent context.
• Use of std::atomic variables may offer better performance than a mutex, but
they’re suited for manipulation of only a single variable or memory location.
```

# 17. Understand special member function generation

C++98 has four such functions: the default constructor, the destructor, the copy constructor, and the copy assignment operator.

Generated special member functions are implicitly public and inline.(Normal member functions written outside the space of class are not inline.)
A destructor in a derived class inheriting from a base class with a virtual destructor is a virtual function.

C++11, the special member functions club has two more inductees: the move constructor and the move assignment operator.
```
class Widget {
public:
Widget(Widget&& rhs); // move constructor
Widget& operator=(Widget&& rhs); // move assignment operator
};
```

The move operations perform “**memberwise moves**” on the non-static data members of the class.
the move assignment operator move-assigns each non-static data member and base class parts from its parameter.

- [ ] Simply remember that a *default memberwise move* consists of move operations on data members and base classes that support move operations, but a copy operation for those that don’t.

## self-defined move member functions (dctor influence copy Operations)

The two copy operations are independent: declaring one doesn’t prevent compilers from generating the other. The two move operations are not independent. If you declare either, that prevents compilers from generating the other.

If you declare, say, a move constructor for your class, you’re indicating that there’s something about how move
construction should be implemented that’s different from the **default memberwise move** that *compilers would generate*.

Furthermore, move operations (construction or assignment) won’t be generated for any class if explicitly declaring a copy operation.
The justification is that declaring a copy operation indicates that the normal approach to copying an object
isn't appropriate for the class. Compiler figures that *memberwise move* is not as appropriate as *memberwise copy*.

Declaring a move operation (construction or assignment) in a class causes compilers to disable the copy operations, too.

In C++98/11, the existence of a user-declared destructor had no impact on compilers’ willingness to generate copy operations.

In C++11(C++98 no move operations), move operations are generated for classes (when needed) only if these three things are true:

• No copy operations are declared in the class.
• No move operations are declared in the class.
• No destructor is declared in the class.

Polymorphic base classes normally have virtual destructors

Declaring a destructor has a potentially significant side effect: it prevents the move operations from being generated. However, creation of
the class’s copy operations is unaffected. requests to move it will compile and run. Such requests will cause copies to be made.

## Conclusion

```
				Things to Remember
• The special member functions are those compilers may generate on their own:
default constructor, destructor, copy operations, and move operations.
• Move operations are generated only for classes lacking explicitly declared
move operations, copy operations, and a destructor.
• The copy constructor is generated only for classes lacking an explicitly
declared copy constructor, and it’s deleted if a move operation is declared.
The copy assignment operator is generated only for classes lacking an explic‐
itly declared copy assignment operator, and it’s deleted if a move operation is
declared. Generation of the copy operations in classes with an explicitly
declared destructor is deprecated.
• Member function templates never suppress generation of special member
functions.
```
