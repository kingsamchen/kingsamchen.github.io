---
title: Monthly Read Posts in Dec 2016
categories: PROGRAMMING
date: 2017-01-04 00:39:01
tags: [monthly read posts, c++, performance, python, iterator]
---
[C++ Performance: Common Wisdoms and Common “Wisdoms”](http://ithare.com/c-performance-common-wisdoms-and-common-wisdoms/)

The post discusses some oftenly-argued performance related aspects of C++.

Topics/objects include:
- C++ Exceptions – (Almost-)Zero-Cost until Thrown
- Smart Pointers and STL: Allocations are Still Evil
- Using boost:: – Case by Case and Only If You Do Need It
- Polymorphism – Two-Fold Impact
- RTTI – Can be Easily Avoided Most of the Time
- On Branch (mis)-Predictions and Pipeline Stalls
- On inline asm and SSE
- On inlines and force-inlines
- On templates
- On compiler flags
- On Manual Loop Unrolling
- “For-free” Optimizations a.k.a. Avoiding Premature Pessimization

All suggestion based on a fundamental pre-requisite: Three things Really Matter for performance.

The first one is Algorithm, the second one is your code being Non-Blocking, and the third one is Data Locality.

The rest of the tricks, while potentially useful, should be used only on case-by-case basis, when a bottleneck is identified. Otherwise it will be a Premature Optimisation.

Note: 作者是一个游戏开发者，上述观点也是结合游戏开发阐述的，因此对相当一部分高性能应用也有参考意义。

---

[Iterables vs. Iterators vs. Generators](http://nvie.com/posts/iterators-vs-generators/)

Key points:

Anything that **can return an iterator** (usually via overriding `__iter__()`) is *iterable*.

Anything that **can be iterated** (usually via overriding `__next__()`, or overriding `next()` in python 2.x) is an *iterator*.

A generator is a **special kind of iterator**; it make you avoid writing `__netxt__()` and `__iter__()`.

Two types of generator:
- Generator function: A function with yield to return value, the return value of the functio call is a generator
- Generator expression: A expression using comprehension-syntax

Note：有 C++ STL 背景基本看这种问题都秒懂...

---

[架构为什么会腐化](http://michael-j.net/2016/12/15/%E6%9E%B6%E6%9E%84%E4%B8%BA%E4%BB%80%E4%B9%88%E4%BC%9A%E8%85%90%E5%8C%96/)

**文中观点**

- 不理解项目业务价值，对于实际需求把握不明确
- 过度设计，导致扩展性差
- 懒于重构

**个人观点**

- 明确/理解需求是第一位
- 过度设计导致扩展性差存疑，实际上过度设计往往来源于需求把握不准
- 初始架构设计很重要，对项目领导者要求较高
- 重构可参考最小滑坡原则（每次离开一段代码，代码都要比刚接手时更清晰整洁）

---

[Competing constructors](https://akrzemi1.wordpress.com/2016/06/29/competing-constructors/)

Competing constructor: two constructors that would take the same sequence of arguments (if one is somehow made invisible), but also that they do different, incompatible things.

Main observations:
- Competing functions complicate resolution for human beings
- Don't write competing constructors, or even competing functions.
- Take care of class template function, one instantiation may turn into a competing function.
- Use tag with overload function can benefit from more clearer semantics