---
title: 一周杂记 in Week 4 May 2023
categories: CODE-LIFE
date: 2023-05-30 00:02:19
tags: [杂记]
---
本周（05/22 ~ 05/28）是五月的第四周。

## Life

\#1

周二和同事一起找了个苏州菜的餐馆吃了顿饭，因为有两个同事那两天前后生日。

十个人一边吃一边扯淡，聊得挺热乎，不过菜倒是感觉一般。

那个餐厅是专门的商务餐厅，类似名人名家的，但是菜性价比很低，而且量很少。

\#2

本赛季 NBA 西决终于结束了，掘金场场5打8淘汰了湖人。

虽然我是湖人/科比球迷，但是这个赛季末段联盟抬湖人进季后赛乃至西决场面实在太难看了。

支持掘金天降正义，另外希望负goat老摊赶紧离开我湖

\#3

这个周末本年度英超也结束了，我厂不负众望拼接这最后阶段掉链子把冠军拱手让给了曼城 🤣

感觉又回到了当年败给莱斯特城，冠军拱手相让的情况 🤦‍♂️

\#4

这周电影看了 John Wick 4 (4/5)，和同事在公司电影俱乐部看的。

到这部我感觉基哥都打不动了，很多动作明显没有甄子丹干净利落。

并且这部叙事明显太冗长了，节奏也不再是连贯的一气呵成。同事说毕竟多安插了一个叙述人物 😅

## Work

\#1

本周学习进度

**CppCon 2020 | How to Cook a Chicken - Sy Brand**

- 纯属搞笑来的

**CppCon 2020 | Build all the things with Spack: a package manager for more than C++ - Todd Gamblin**

- 介绍的 spack 包管理

**CppCon 2020 | Why Conan (part II, 5 reasons to use Conan in 2020) - Diego Rodriguez-Losada**

- 介绍 conan

**CppCon 2020 | How To Profile Compilation Duration? - Thomas Lourseyre**

![](img/compilation_profiling.png)

**CppCon 2020 | Why C++ for Large Scale Systems? - Ankur Satle**

- 其实就是目前业界用 C++ 的系统介绍

**CppCon 2020 | cppinclude - Tool for analyzing includes in C++ - Oleg Fedorenko**

- 介绍 cppinclude

**CppCon 2020 | If You Build It, Will They Come? - Javier Estrada**

- 一起建设 Reference

**CppCon 2020 | How many languages a (C++) expert should speak ? - Thomas Lourseyre**

- 尽可能接触一些其它语言，否则很容易变成所有东西看起来都像钉子的情况。选择接触的语言最好能引起你的兴趣和好奇心

**CppCon 2020 | What is an ABI, and Why is Breaking it Bad? - Marshall Clow**

- 现有标准的一点点改动都可能带来 ABI breaking，抛开类型变化，新增虚函数这种，talk 里举了一个给 `std::pair` 拷贝构造函数使用 `=default` 都会导致 ABI break。
并且如果你的使用场景不是每次都从头构建，那么避免 ABI break 是非常需要考虑的

CppCon 2020 就正式完成了，下周会开始刷 CppCon 2021

**C++ 20 高级编程**

- 3. 概念约束
- 4. 元编程介绍

**现代C++白皮书 (All Done)**

- 10. 2020 年的C++
- 11. 回顾

现代C++白皮书 也看完了

**Posts**

- https://boyter.org/posts/how-to-start-go-project-2023/
- [分布式ID生成方案](https://colobu.com/2020/02/21/ID-generator/)

这两篇都推荐

\#2

这周抽了点时间把 abseil-cpp 的 base/attribute 和 base/casts 的源码过了一下

---

好了这周就这样，下周见
