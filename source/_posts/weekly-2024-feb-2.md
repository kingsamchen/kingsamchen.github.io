---
title: 一周杂记 in Week 2 Feb 2024
categories: CODE-LIFE
date: 2024-02-12 14:08:55
tags: [杂记]
---

本周（02/05 ~ 02/11）是二月份第二周，也是中国农历新年开始的一周，02/09 是除夕。

## Life

\#1

节前 02/08 请了一天年假，所以这周工作日只有4天，并且因为马上到年关放长假了，再加上 US 连续几次骚操作弄得 HZ 这边更加不爽，所以工作的热情并不高

不过严格来说，上周日和这周三都是在家工作的，所以嘛有点半休假的意味，所以实质上算起来只上了三天班 🤣

一开始买的周四上午十点不到的高铁，中午到老家。但是老婆觉得有点早了，让我时不时刷一下能不能改签中午出发的，午饭在老婆医院附近的地方吃顿好的。

然而事与愿违，前期反复刷一直无票，而直到8号凌晨发现有班车次居然有两张一等座空出来，于是立马改签，结果发现此时过了凌晨一点无法操作，于是定了一个5点的闹钟，打算一早起来改签。没想到5点起来之后改签一直提示没有足够的余票完成改签，心想估计是撞到缓存了..orz

于是只能倒头继续睡，反正之前的车次也不是不可以。最后还是乘坐了一开始的车次。

不过因为是一等座，所以哪怕是过年期间车厢也还好，整体上比较安静。

实话说我现在坐高铁除非只是去上海这种四十分钟就搞定的，基本都是一等座了，整体体验还是值回票价的。

下周整周都是休假，🤭

## Work

\#1

本周学习进度如下：

**Design Patterns in Modern C++ | 1. Introduction**

- 这张第一部分先简要介绍了一下 C++ 中一些特别的 idioms，比如 CRTP，Mixin，基于 concepts 的 polymorphsim .etc
- 第二部分简要回顾了 the SOLID principle

**Design Patterns in Modern C++ | 2. Builder**

- 讲 builder pattern
- 不过我个人不喜欢这一章里介绍的设计方法，越设计越复杂了

**Tip of Week: Did you know about variadic aggregate initialization?** https://github.com/tip-of-the-week/cpp/blob/master/tips/214.md

- 纯元编程的东西，题目都没看懂要干啥，而且毫无 lecturing 可言，有兴趣的自己看吧

**Variadic expansion in aggregate initialization** https://greitemann.dev/2018/09/15/variadic-expansion-in-aggregate-initialization/

- 这篇应该是上一篇的前置内容
- variadic expansion 非常适合解决 std::array 存储没有默认构造函数的类型的元素时会遇到的各种问题，也是主要使用场景之一

**C++20 Range Adaptors and Range Factories** https://brevzin.github.io/c++/2021/02/28/ranges-reference/

- 简要介绍并且罗列了一下 C++ 20 的 range producer & adapters

**CppCon 2021 | Back to Basics: Designing Classes (part 1 of 2) - Klaus Iglberger**

- Design for readability: spent sometime to find good names for all entiteis
- Design classes for easy change and easy extensions.
- Resist the urge to put everything into one class and design classes to be testable
- For resource management, if can avoid define any one of 5 (i.e. zero), do it; otherwise, define (including `=default` ) them all.

**CppCon 2021 | Back to Basics: Designing Classes (part 2 of 2) - Klaus Iglberger**

- empty default constructors (that doesn’t initialize any member) will hinder zero initialization so better to avoid them
- member initialization order; prefer in class initialization if no need take arg from ctor param
- be wary of const correctness
- reference member with reference_wrapper
- overload resolution comes before accessiblity checks (经典问题)

**CppCon 2021 | Configuration, Extension, Maintainability - Titus Winters**

- 这个 talk 比较有意思，讲的比较发散但是核心还是聚焦于设计的 configurale, customizable, extensibility 的各种取舍上
- 简化一下就是：从目的（intent）出发去设计，前期尽可能简单直观，不要想着一开始就设计所谓扩展性很好的接口，这种接口第一很难设计对第二后期很难改正之前错误的设计
- Titus Winters 之前是 G 家 C++ 工程方向的负责人，演讲比较有意思

\#2

最近终于有时间改进一下 anvil，把新工程模板里的 `build.py` 给去掉了，换上了全新的 `CMakePresets.json` 的模板

自从适应了 CMake Presets 了之后，觉得这玩意儿超级好用，便捷，并且 IDE/Editor 搭配对应的插件之后基本都能感知

不过因为升级 Python3.12 之后老版本的 pip3 直接挂了，费了一番功夫才升级到新版本的 pip3，所以最近正在考虑是否要把 anvil 从 python3 换成 golang

\#3

前几天在 Windows 中用 `scoop update *` 把一些组件都升级了，但是 Python3 升级到 3.12 之后之前搭配的默认的 pip3 因为某个类的不兼容变化直接挂了，一直提示

> AttributeError: module 'pkgutil' has no attribute 'ImpImporter'. Did you mean: 'zipimporter'?

跟着 https://stackoverflow.com/questions/77364550/attributeerror-module-pkgutil-has-no-attribute-impimporter-did-you-mean 里的操作升级 pip3 但是一直失败。

仔细研究了错误之后发现升级程序找不到 `pip3.exe.deleteme` 所以失败了，但是不知道为什么这个文件没有生成。

于是我实验性的复制了一份 `pip.exe` 然后重命名一下，结果再升级就成功了...

真的离谱 😒

---

好了这周就这样，下周见
