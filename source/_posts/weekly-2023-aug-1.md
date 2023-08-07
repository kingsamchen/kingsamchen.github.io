---
title: 一周杂记 in Week 1 Aug 2023
categories: CODE-LIFE
date: 2023-08-07 21:46:52
tags: [杂记]
---
本周（07/31 ~ 08/06）是8月份的第一周。

## Life

\#1

转眼终于到8月份了，炎热的天气慢慢就要熬过头了。

今年的夏天依然不宁静，先是河北大水淹了一片，然后东北继续大水，紧接着山东都开始地震了。

你说这不是天谴吧也着实太离谱了，搁古代这皇帝都得下罪己诏。

不过说起来最近几年啊，每年都不太平，各种奇奇怪怪邪门的事情频出，还是大家小心为上。

\#2

这周看了三部电影

- 《交错卢森堡》 3.5/5 时间多可以看一下，比较温情平稳的家常片，掺杂了一点人生悲剧的喜剧段落。不过我不是太吃这种类型的电影
- _The Proposition_ 2/5 剧情稀烂… 冲着 cast 去的，结果看了个寂寞
- _Something the Lord Made_ 4/5 老婆钦点的周末影院，但是她又是看到一半说好无聊...历史事件改编，讲述黑人“医师” vivien thomas 从医学技工做起和 dr. alfred blalock 一起攻克新生儿心脏畸形治疗手术的故事。时代背景是美国平权运动前，所以电影有主线和暗线。
    我个人还是比较喜欢这种风格的电影

\#3

不知道是不是天气太炎热导致的，最近工作非常没有动力，而且总是一种让人疲倦的感觉

## Work

\#1

本周学习进度如下

**CppCon 2021 | Misra Parallelism Safety-critical Guidelines for C++11, 17, Then C++20, 23**

- Misra 自己内部关于并发的 guidelines，但是没有特别细的解读，就稍微提了一下 overview

**CppCon 2021 | Lightning Talk: Direct Aggregate Initialisation - Timur Doumler**

- C++ 20 开始 relax 了 aggregation 的一些情况，作者称之为 direct aggregate initilaization，i.e. 允许使用 `()` 细节看 https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p0960r3.html
- 不过要注意的是 `A()` 会触发 value initialization

**CppCon 2021 | Documentation in the Era of Concepts and Ranges - Christopher Di Bella & Sy Brand**

- how to document your code 以及 concepts 的到来可以提升泛型代码的 documentation
- talk 中是拿 ranges-v3 举例

**CppCon 2021 | Lightning Talk: Quantum Interpretations of the C++ Object Model - David Stone**

- 又名：趣谈 C++ UB
- program execution 那个梗有点意思…

**C++ 20 高级编程 | 8. 协程**

- 讲的是真的不咋地，还老是莫名其妙中间开分支讲一下不相关的东西…还是回头看其他人写的 posts 吧

**C++ 20 高级编程 | 9. 模块**

- module 速通

**Modern CMake for C++ | 6. Linking with CMake**

- 讲了 static lib, shared lib 和 shared module；使用 target_link_libraries 一些常见用法
- 以及链接器符号选择/覆盖和 ODR

\#2

CMake 中除了常见的动态库 Shared Lib 之外还有一个 Shared Module，这种 target 其实也是动态库，但是主要用于动态加载，例如 Windows 上调用 LoadLibrary() 以及 POSIX 上调用 dlopen。

这种库是不应该直接链接到目标二进制上的。

SO 上也有类似的问题：https://stackoverflow.com/questions/4845984/difference-between-modules-and-shared-libraries

\#3

这周（算是）一鼓作气，把 abseil base/call_once 相关的代码看完了，相关的核心点也做了笔记。

下周可以交替切换到造轮子周期了 🙈

---

好了这周就这样，下周见
