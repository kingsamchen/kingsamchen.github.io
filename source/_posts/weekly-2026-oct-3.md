---
title: 一周杂记 in Week 3 Oct 2026
categories: CODE-LIFE
date: 2026-10-19 22:21:57
tags: [杂记]
---
本周（10.12 ~ 10.18）是10月第三周，本周天气开始反复，不过最热也只30出头。

## Life

\#1

周一清晨7点多就被 pager duty 叫醒了，当时还纳闷我也不是 oncall 啊，怎么 warroom 会拉到我

进会之后大概明白了是有个客诉问题比较重大，Roro 看到我刚好有个MR修了那个问题，想问我大概是什么情况。

最后大家一交底，发现是公司 key rotation 之后，OPS没有按照 runbook，把一个用来加密的key给 current -> previous，而是直接换了一个新的...

虽然没啥大问题，但是这一天的睡眠完全坏了 😂

\#2

周五中午想去乐刻做点有氧，刚上跑步机爬坡没多久跑步机自己停机了，还好速度不快不然我估计要摔到了。

然后头顶的灯也全灭了，这时才反应过来健身房停电了啊。。。

店长检查了一下总闸发现没有跳闸，电问了银泰物业，物业说线路问题，正在检修 😅

没办法，稍微做了一下力量就走了 🤣

\#3

这周除了再刷 POI 的解说视频之外没啥新鲜事。

另外就是秋宝又搞事情了，一个眼疾手快把醋碟打翻了，醋洒了一地，万幸碟没掉地上，否则碎了收拾起来更麻烦 😅

## Work

\#1

**CppCon 2023 | Lightning Talk: Is Polymorphism in C++ Really That Bad? - Paul Tsouchlos** https://www.youtube.com/watch?v=BujbOFNHHIY&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=72

- 继承/虚函数（运行时多态）这套并没有那么差，该用就用

**CppCon 2023 | Is C++23 std::mdspan a Zero-overhead Abstraction? - Oleksandr Bacherikov** https://www.youtube.com/watch?v=9fRnSQkpNGg&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=71

- mdspan 在矩阵加法方面比起 BLAS 并没有做到 zero cost abstraction
- 里面提到了 Linux + MSVC 传输结构体走寄存器的限制；Linux 6个传参寄存器MSVC只有四个，并且如果结构体超过16 bytes或者不是 trivial copy 那么 pass by value 时也不会走寄存器
- 里面提了一个 trick，但是不是非常 user friendly

**CppCon 2023 | Abstraction Patterns for Cross Platform Development Using C++ - Al-Afiq Yeong** https://www.youtube.com/watch?v=Et0ofYRi3fw&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=73

- 从游戏开发者的视角：如何处理跨平台的项目
- 从基础的 #if..#define 到基于运行时多态的多个平台实现版本，再到利用 pimpl/handle 配合 build system
- 后面还设计了一些 hot swapping 的 tips

**C++ Weekly - Ep 554 - Trailing Return Types - Why?** https://www.youtube.com/watch?v=41YdzJ-Jx48

- 解释 c++ 为什么要引入 trailing return type

**C++ Weekly - Ep 70 - C++ IIFE in quick-bench.com** https://www.youtube.com/watch?v=d4nmNYTM1j8

- Immediately-invoked function expression 能给编译器提供更好的优化机会

**C++ Weekly - Ep 71 - Hidden C++ 17 - Part 1 (Fixed Video Quality)** https://www.youtube.com/watch?v=HeyB-DkAqWo

- 标准库 regex_constants::multiline 但是严肃的工程就不应该用 std::regex 吧
- std::shared_ptr<T[]> 记得当年 shared_ptr 刚出来的时候还专门分析过为什么不支持 array 🤷‍♂️
- std::X_pointer_cast 智能指针用的 casts
- std::as_const 这个好用
- std::from_chars/to_chars 标准库提供的比较高效的 to_string + parse numbers 的基座

**C++ Weekly - Ep 72 - Hidden C++17 - Part 2** https://www.youtube.com/watch?v=K2q39qt1gx0

- std::shared_mutex 读写锁
- 一堆 math 的函数

\#2

这周给 fawkes 修了个大的：之前的 cookie 部分处理有不少的不对/坑，这次经过多次和 GPT 老师的交流，都给填上了。

PR 见 https://github.com/kingsamchen/fawkes/pull/44

\#3

这周发现一个坑，如果你的后台服务编译的时候开启了 ASAN，并且在后台运行的时候打开了比较深度检测，进程使用的 RSS 会随着后台代码不断执行而累计。

这部分累计基本都是 asan runtime hold，并且默认他 hold 的内存占比会比较高

我们的 vm 这样的服务比较多，就会导致内存一直在高水位占用...

最后的解法是不要把选项开的这么狠...

---

这周就这样，下周见~
