---
title: 浅析 shared_ptr：序论
categories: PROGRAMMING
date: 2018-03-13 09:03:14
tags: [shared_ptr-internals, source-code-study, shared_ptr, weak_ptr, c++]
---
单看标准库而言，`shared_ptr`/`weak_ptr`（后文除特指外，不再同时带上 `weak_ptr`） 一开始作为 TR1 的一员引入，低调行事多年后自 C++ 11 开始成为标准库正式成员。

在历史意义上，引入 `shared_ptr` 不光规范化了 resource ownership 作为 abstraction conception，同时解决了困扰广大 C++ programmers 多年的难题：*如何知道一个对象已经被析构了*。

此外，`shared_ptr` 的某些独特实现技巧，也提供了一些神奇的 idioms，例如
- deleter 不作为自身类型的一部分
- 可以正确的析构 cast 到基类的子类对象，即使基类没有提供 virtual destructor
- .etc

前几天偶然有个想法，既然自己用了这么久的 `shared_ptr`，那为什么不去看看它内部是如何实现的呢？能涨涨姿势也是好的嘛。

为了让目标更明确，不至于迷失在浩瀚细节中，我整理了一下当前几个比较重要且有意思的点，作为代码阅读的 targets：

1. How `shared_ptr<T>(new T())` differs from `make_shared<T>()`
2. Why virtual dtor is not necessary to correctly destruct a casted derived object instance
3. How custom deleter works and why it is not part of the pointer type
4. How `enable_shared_from_this` works
5. How reference counting works
6. How `weak_ptr` relates with `shared_ptr` (promotes `weak_ptr` to `shared_ptr`)
7. Thread-safety of `shared_ptr` instances

考虑到存在多方的实现和平台的常见程度，目前选择了 4 个实现版本：
- MSVC STL：Windows 上使用 Visual Studio 的版本
- Libstdc++：Linux 上的标配
- Boost：先驱者
- Chromium Base：严格来说 Chromium Base 的实现和标准库的完全不同，放在这里只是因为我对 chromium 这个项目有一种特殊的情感/好感。这个团队是真的知道工程化团队的重要性以及能够推进团队工程化，代码一致性极高，很少出现炫技的情况（望向 Facebook 和 Microsoft）。即使部分代码的架构和实现不能算是 C++ Best Practices，也不能掩盖他们整体的工程能力。

后面会为每个实现版本写一篇单独的 post，至于最后有没有一篇 summing up，就看后面有没有时间，或者这个必要了 :-)
