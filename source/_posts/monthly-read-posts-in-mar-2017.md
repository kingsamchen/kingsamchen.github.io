---
title: Monthly Read Posts in Mar 2017
categories: PROGRAMMING
date: 2017-04-02 16:58:24
tags: [monthly read posts, c++, lambda, appending file, oauth 2.0, multithreading, stl]
---
[Generate lambdas for clarity and performance](http://playfulprogramming.blogspot.hk/2017/01/generate-lambdas-for-clarity-and.html)

Generate a class of lambdas with auto-return-type deduction.

唯一比较遗憾的是特性需要 C++ 14 的支持，C++ 11 里估计需要用 `std::function<>` 来做 workaround

---

[Appending to a File from Multiple Processes](http://nullprogram.com/blog/2016/08/03/)

经验问题，写客户端的 logger 轮子时应该会经常需要考虑到这个问题。

文章针对的是 POSIX 系统，不过不同系统在一些具体策略/细节上也有区别。

另，Windows 上这个场景有直接的 API 调用保证。

不过有点比较奇怪，chromium 的 base::logging 只提供了 Windows 下的多进程无锁 appending，POSIX/Mach 下依然用的是线程同步锁和全局锁，可能考虑的原因是实现起来坑比较大？

---

OAuth 2.0 Protocol

1. [oauth2 authorization](http://tutorials.jenkov.com/oauth2/authorization.html)
2. [principle-of-oauth2](https://taozj.org/201605/principle-of-oauth2.html)
3. [oauth_2_0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)

第二篇文章基本可以看作第一篇的翻译，但是很多地方都没有翻译出来，所以推荐主要看第一篇文章，把第二、三篇作为补充材料

---

[Inheritance Is The Base Class of Evil](https://channel9.msdn.com/Events/GoingNative/2013/Inheritance-Is-The-Base-Class-of-Evil)

C9 上的一个 Tech Talk，speaker 是 Adobe 的技术大佬，但是我觉得有标题党之嫌。

Talk 中展现出来的哲学观挺有意思的，比如那句 *There is no polymorphic type, just polymorphic use of types*。

---

[Learning STL Multithreading](https://channel9.msdn.com/Shows/C9-GoingNative/GoingNative-53-Learning-STL-Multithreading)

虽然标题是 STL Multithreading，但是里面的核心内容基本都是原理性的，通用的。

对于 mutex/condition-variable 这类比较高级的 synchronization primitives 和低级的 atomic variables 均有涉及，并且也给了实践建议。

话说右边那个胖胖的小哥的口音听起来真的是容易懂...

另外有个挺有意思的细节，快到最后的时候胖小哥提到了 `async` 和 `future`，他说希望大家能优先考虑这俩玩意儿，但是说了几句话又欲言又止，最后无奈地说 *we will discuss these later*。。。估计他想了想，就目前标准给的设施和实现情况，要做到 task-based paralleilism，那坑大的都可以把自己埋进去了。