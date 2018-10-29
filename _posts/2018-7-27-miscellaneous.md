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
Have endured my endless blathering about the rules governing the copy and move operations in C++11.  
Don’t worry if you see something whose motivation is less than obvious.  
They don’t hinder callers from replacing it with its more flexible sibling.  
How primitive!  
Unleash your search engine.  
No divorce, no annulment, no dispensations.  
The more experience you have with these features, the more you realize that your initial impression was based on only the metaphorical tip of the proverbial iceberg.  
No matter how far you dig into these features, it can seem that there’s always more to uncover. Fortunately, there is a limit to their depths. This chapter will take you to the bedrock.  
It’s been said that the truth shall set you free, but under the right circumstances, a well-chosen lie can be equally liberating.  
My liberal use of quotation marks should tip you off that this line of reasoning is flawed.  
Relising problems is tantamount to solving them.  
The syntax is off-putting.  
Make it easier to see how the pieces fit together.  
Tantalizingly close.  
We got dragged into along the way.  
Pour these ingredients into the pot with everything else we’ve discussed, simmer over a low flame, and savor the aroma of success.  
Reference collapsing will kick in(起作用) to eliminate it.  
Move semantics can really pull that off, and that grants the feature an aura worthy of legend. Legends, however, are generally the result of exaggeration.  
At this level, there’s truth to the adage that references are simply pointers that are automatically dereferenced.  
Perhaps you noticed the weasel words I sprinkled through the preceding discussion.  
Do not stress over the detail.  
Default by-value capture isn’t the antidangling elixir you might imagine.  

## 鞋狗（译）

除了时刻都在排队等待死亡之外，人生还是非常美好的（P354）

据说加强某一方面知识的最好方法就是和别人分享。（P311）

找出你对某人态度的最简单方式就是想象与他分别（P141）

## 人间失格

一旦试图富有实验性地、忠诚于自我地生活下去，就很可能遭到社会的疏远和异化，成为“人间失格者”。或许在所有的现代人的心中，都或明或暗地存在者一块懦弱、孤独而又渴求爱的荒地，而这块荒地却被太宰治的文字无声地侵蚀着，而且无从回避。

## 韭菜的自我修养

关于这本书，首先我不想谈论作者本人，他是一个好人亦或是坏人，都不影响我们认真而耐心地读完这本小册子。
其次韭菜是指那些一入场就开始买买买，看见大盘下跌就着急上火，到处打探小道消息，最重要的特征是韭菜不学习，学习了却不思考，思考了却
不总结，总结了却不坚持这些原则。这本书和《principle》特别类似，《principle》里面将做一件事概况成
```
audacious goals -> failure -> leanring principles -> imporving -> more audacious goals
```
在这个过程中学习提高总结过程最重要，韭菜也是有耐心的，也是清楚自己玩的不是零和游戏，但是他们没有学习专业知识，没有从失败的投资里
学到principle，更别提从别人的失败经历中学习。所以韭菜的重要缺陷是没有专业知识而又不学习总结的人。

书中提到的孤独是成功交易者最宝贵的财富，这也是principle里面提到的找到自己的principle, "summon up the courage to find out it"，我尤其欣赏这句话。在孤独中，你才能反思自己的错误，才能在繁忙的工作和无聊的生活中找到一块难以被侵蚀的土地，在那里，你或许可以找到自我。

书中还提到人怎么变坏就会怎么变傻，这个principle很有意思，李笑来说人会因为努力解释自己原来犯下的错误是正确的，在心里麻痹自己，直到下一次同样的错误（变坏人、坑队友）发生在自己身上。我觉得人还是得有份从容，这份从容可以将你带离坏人的处境。（有点困了，睡会）

## 教师的自我修养

我无法想象她多日以来承受的压力有多大，或许她不应该选择这条道路，或许是时候通过哭泣释放一下自己内心的压抑了。

## Principle

Maturarity is the ability to reject good alternatives in order to persue even better ones.  
I've learned that judging people before really seeing things through their eyes stands in the way of understanding their circumstances -- that isn't smart. I urge you to be curious enough to want to understand how the people who see things differently from you came to see them that way. You will find that interesing and invaluable, and the richer perspective you gain will help you dicide what you should do.  
My belief that having ability to figure things out is more important than having specific knowledge of how to do something.  
Stifle your mild disbelief at the necessary circumlocution.  
