---
layout: post
title: Miscellaneous杂记
date: 2018-07-27
categories: blog
tags: [技术总结]
description: Miscellaneous杂记
---

## 小岛经济学

没有自由的失败，就没有自由的成功

## Effective Modern C++

It's been said that the truth shall set you free, but under the right circumstances, a well-chose lie can be equally liberating. We comprise "lie" to abstraction.

Bear in mind that the truth doesn't make the abstraction any less useful.

In short, rvalue reference should be *unconditionally* cast to rvalue (via **std::move**).

Universal refenrence should be *conditionally* cast to rvalue (via **std::forward**).

## 外汇管理局新规2016

根据外管局最新规定：[链接](https://xueqiu.com/5514474822/66337756)

1. 5个以上不同个人，同日、隔日或连续多日分别购汇后，将外汇汇给境外同一个人或机构；
2. 个人在7日内从同一外汇储蓄账户5次以上提取接近等值1万美元外币现钞；
3. 同一个人将其外汇储蓄账户内存款划转至5个以上直系亲属等情况。
将被界定为个人分拆结售汇行为，也就是俗称的“蚂蚁搬家”，一律进入黑名单，违规者将被剥夺两年合计10万美元的换汇额度。

## 活着

内心使他真正地了解自己，了解了自己也就了解了世界。

## 海盗经济学

海盗船实行的是民主（democracy），而商船实行的是专制（autocracy）；在海盗船上，甚至黑人（18th century)也能够享受和白人一样的权利，很少出现像商船那样，船长侮辱甚至殴打船员的现像，而且无论船长还是舵手和普通船员的工资差不多，没有商船上的差别那么悬殊。归结原因其实是*所有制*导致了二者实行不同的管理制度，不同的管理制度保证了二者都有最大的总体利益，只是相对个人而言不甚相同。

## 精通数据科学
The purpose of computing is insight, not numbers.
If you are immune to boredom, there is literally nothing you cannot accomplish. -- David Foster Wallace

## Effective C++
I have not been able to find a convincing explanation. But the rule is the rule, and this means you must
remember that if you declare a variable using auto and you initialize it with a braced initializer, the deduced type will always be std::initializer_list. It’s especially important to bear this in mind if you embrace the philosophy of uniform initialization—of enclosing initializing values in braces **as a matter of course(理所当然)**.

It provides results that leave you scratching your head and turning to reference works or online Q&A sites for revelation.

What may initially seem contradictory (decltype and auto?) actually makes perfect sense.

Employing pass-by value for objects of **an unknown type** generally risks the performance hit of unnecessary
copying, the behavioral problems of object slicing, and the sting of our coworkers’ derision.

bring it into accord with admonition 使之符合告诫

You’ll have to familiarize yourself with a few special cases. Most of these are too obscure to warrant discussion in a book like this, but looking at one lends insight into decltype as well as its use.

At the same time, don’t lose sight of the bigger picture.

I do my best to convice you to use lambdas instead of std::bind.

Take a deep breath and relax. auto is a option, not a mandate.

such abstract type information should almost always be at hand.

nullptr shines especially brightly when templates enter the picture.

There’s a historical reason for that, but we’ll skip over it (it’s dull, I promise)

Examples will clarify what I mean:

In many cases, you may decide that typing a few extra characters is a reasonable price to pay for the ability to avoid the pitfalls of an enum technology that dates to a time when the state of the art in digital telecommunications was the 2400-baud modem.

This is hardly the most encapsulated design that’s seen the light of day.

There’s some wiggle room for optimization through what is known as the “as if rule,” but you’d be foolish
to rely on your compilers finding a way to take advantage of it.

Don’t let the warm glow of this happy ending distract you from the true point of this Item

Restrictions are imposed on their implementations.

have endured my endless blathering about the rules governing the copy and move operations in C++11.
