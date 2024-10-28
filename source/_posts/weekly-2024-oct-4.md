---
title: 一周杂记 in Week 4 Oct 2024
categories: CODE-LIFE
date: 2024-10-28 20:49:30
tags: [杂记]
---
本周（10/21 ~ 10/27）是十月份的第四周。

## Life

\#1

周一晚上跑去旁听了小区业主对业委会自动门违规招标的质询会。

大致情况就是这一届业委会要强推自动门改造，并且以差不多70W的小区经营收入基金对自动门改造单位进行招标，并且这几天就是公示结束期。

由于之前小区业主的反对被强压，所以很多人怀疑这次改造有猫腻，业委会有人可能想捞油水，于是大家一边争取业主支持，一边自发调查内情。然后果不其然发现此次招标有很多不符合规定并且有蹊跷的地方。

质询会上火药味有点重，但是因为业委会一方自身存在违规，所以也理亏。

最后结论是暂缓自动门改造，把业委会重心放到外墙修缮上。也算是业主监管一方获得了预期诉求。

然后更有意思的是最新的消息是，这一届业委会几乎所有人都主动辞去职务，社区同意后小区会开始下一届业委会的选举工作。

\#2

周三又到了喜闻乐见的季度团建，并且因为 Zoomtopia 的福，这次人均经费给到了300.

考虑到天气和大家的项目偏好，这次的节目是去转塘的一个小户外露营基地自助烧烤和休息。

因为比较远，所以我这次也当起了司机，带上了三个同事。

这次烧烤还凑合吧，不算太烂，肉是山姆买的，但是整体体验肯定不如在御牛道这种地方吃

吃完后一群人打起了德扑，我们这些不想打牌的就随便找了点小游戏玩了玩，然后一起扯淡吐槽老张 🤣

待到四点半多有点起风降温了，就又带着同事往回开了。还好没怎么遇上下班高峰

现在看看自己有车的话这种中远途的团建确实方便一些。

\#3

本周观影

- **异形4 Alien: Resurrection** 3.5/5 这部只有纯科幻动作片的观感了…
- **铁血战士 Predator 4/5** alien是幽闭恐惧，predator是未知敌人恐惧，一些设定现在来看都是一流的。把探讨深度的要求放低，这就是非常标准而且完成度很高的惊悚动作片

## Work

\#1

本周学习

**C++ Templates 2nd | 18. The Polymorphic Power of Templates**

- 介绍了以 template 为基础的 static polymorphism 以及和传统基于继承虚函数的 dynamic polymorphism 的比较

**CppCon 2022 | Optimizing Binary Search - Sergey Slotin** https://www.youtube.com/watch?v=1RIPMQQRBWk&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=52

- 优化目标是比 std::lower_bound 要更快
- branchless 的二分版本再数据量小的时候延迟比标准库的小，但是数据量大的时候反而不如标准库
- 作者给的终极方案是在 sorted array 上构造一个 B+ tree 模型，然后利用 b+ tree 的多子少层的优点，配合 SIMD 来做极致优化。这个方案再少数据量和大数据量下延迟都非常优秀，比 absl/b-tree 都高出不少，但是比较依赖 modern hardware

**CppCon 2022 | HPX - A C++ Library for Parallelism and Concurrency - Hartmut Kaiser** https://www.youtube.com/watch?v=npufmMlGOoM&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=53

- 介绍他们出品的 hpx 这个库，支持目前几乎所有 algorithms 头文件里的算法的并行版本，并且 支持传统 executor 和 S&R 两种执行模型
- 可以权当了解

**CppCon 2022 | Lightning Talk: Dependency Injection for Modern C++ - Tyler Weaver** https://www.youtube.com/watch?v=Yr0w62Gjrlw&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=57

- 设计上如果类提供 std::function 来接受外部的操作设置，那么就可以利用这点来做 mock 的 injection，大意就这样

**CppCon 2022 | Embracing Trailing Return Types and `auto` Return SAFELY in Modern C++ - Pablo Halpern** https://www.youtube.com/watch?v=Tnl7FnwJ2Uw&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=54

- Trailing Return Type 这种风格好处多多，大意就这样 😂

**CppNow 2024 | C++ Should Be C++ - David Sankel** https://www.youtube.com/watch?v=qTw0q3WfdNs&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=21

- 质量很高的一个对话式的 talk
- Rust 的出现和成熟不会 obviate c++，就好像工具箱里多一样工具不会让你扔掉现有工具
- 真正对 C++ 有负面影响的是 standard committe，现在人太多了而且每个 member 都想夹杂代表自己背后集团利益的私货，会让语言变得不必要的复杂

**CppNow 2024 | Investigating Legacy Design Trends in C++ & Their Modern Replacements - Katherine Rocha** https://www.youtube.com/watch?v=DvM8oKA1YiM&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=17

- 选取了几个常用场景，比如 singleton, sfinae, polymorphism, data flow 从03开始到11再到20之后的 pattern 演变
- 我觉得这种路子来介绍新特性还挺不错的

**C++ Weekly - Ep 451 - Debunking bad_alloc Memory Errors (They're actually useful!)** https://www.youtube.com/watch?v=-f5HmDR0GGY

- 核心内容是，虽然内存 overcommit 的时候系统会把进程 kill 掉，但是 bad_alloc 这个异常还是有存在的必要
- 第一种情况是申请了超过地址空间的内存，但是因为没有 commit 所以系统不会杀掉进程，这个时候会抛出 bad_alloc
- 第二种是处理 gpu 或者 shm 的时候如果出现某些问题也会触发 bad_alloc

**C++ Weekly - Ep 444 - GCC's Implicit constexpr** https://www.youtube.com/watch?v=CHc39_qCgMU

- gcc 的 `-fimplicit-constexpr` 会自动将 inline 并且满足 constexpr 条件的函数变成 constexpr
- 仅当了解即可

**C++ Weekly - Ep 148 - clang-tidy Checks To Avoid** https://www.youtube.com/watch?v=oxpsHk1yq88

- 其实就是吐槽 Google 系一堆项目的 check rules 很奇葩

**Comparing Floating-Point Numbers Is Tricky** https://bitbashing.io/comparing-floats.html

- epsilon 只在两个浮点数都比较小（小于1）的时候好用，为了解决其他的情况，引入 ULP (units of least precision）的概念，直接比较两个数之间的ULP（距离）
- Boost 也有类似的函数，但是因为没有用 type punning 所以性能不如 post 里的这套实现、
- 不过实际中用的时候建议还是用 boost 吧，别手搓了

**Automate your C library type-based overload resolutions with C++17** https://mklimenko.github.io/english/2021/08/17/c-library-plusifier/

- 核心做法是把这一系列 C 函数（指针）打包进内部的 tuple，然后在编译期遍历 tuple 用 std::is_invocable 来检查当前 tuple element 对应的函数指针能否适配参数
- 不过既然都用了 std::is_invocable 的话，其实 tuple 遍历可以直接配合 std::index_sequence 做 fold，写起来应该更简单
- 最后是我个人其实不太 prefer 这个方案，因为 C 是没有重载概念的，所以他的函数参数不一定会考虑到C++的重载决议，有可能存在 type promotion 而导致选错函数的情况，并且这种情况不一定好 debug

**C++ coroutines: Getting rid of our reference count** https://devblogs.microsoft.com/oldnewthing/20210416-00/?p=105115

- 因为不能用 std::noop_coroutine_handle 所以直接用 `(void*)2` 来代表 abandoned state
- 然后分情况拆解状态去掉 incr/decr count 的调用

**Pragma: once or twice?** https://belaycpp.com/2021/10/20/pragma-once-or-twice/

- 作者站 header guard macro，我个人现在偏好 once
- 文章里提到的 pragma once 的缺点对我来说压根不是问题，比如 duplicate files 会认为是两个文件；这个不是标准啥的
- 想法我觉得一些极端情况下 header guard macro 能用反而是他的缺点

**Exploring the C++ Coroutine** [https://luncliff.github.io/coroutine/ppt/[Eng]ExploringTheCppCoroutine.pdf](https://luncliff.github.io/coroutine/ppt/%5BEng%5DExploringTheCppCoroutine.pdf)

- 只是一个演讲的 slides 所以如果没有对应的视频的话就显得割裂
- 内容比较全，基本点都覆盖到了

**Exploring MSVC Coroutine** https://luncliff.github.io/coroutine/articles/exploring-msvc-coroutine/

- 作者先写的这篇然后才是上面的 slides，内容大部分都比较接近，不过这篇是针对 MSVC 的早期实现的版本
- 写的时间有点久了，而且感觉作为学习文章的话，learning curve 不太友好

\#2

抽时间看了 [coop](https://github.com/jeremyong/coop) 的实现，感觉整体不如之前的那个 coroutine-based thread-pool

如果想学习的话，还是之前那个 post + sample 好

\#3

在 {% post_link weekly-2024-sep-1 一周杂记 in Week 1 Sep 2024 %} 里提到的 SFINAE 重载的问题，听 cppnow 那个 talk 的时候有关中说到一样的情况，并且给的方案是把 SFINAE 参数换成 non-type template parameter

一查 https://en.cppreference.com/w/cpp/types/enable_if#notes 发现直接就有写，囧rz…

```cpp
template<typename T, std::enable_if_t<std::is_integral_v<T>, bool> = true>
void func(T) {
    std::cout << "T=integral\n";
}

template<typename T, std::enable_if_t<!std::is_integral_v<T>, bool> = true>
void func(T) {
    std::cout << "T=non-integral\n";
}
```

\#4

抽了不少时间给 esl 做了两个 PR。

第一个 PR 涉及到 pipeline flow 的调整。

第一个是调整了 sanitizer，单独放到一个 cmake module 并且 Windows 上不管那个模式都默认关闭。

因为 Windows/MSVC 上比较麻烦，还需要设置 PATH 才能让程序找到需要的 asan 的 dll，所以显然这是不合适 CI 默认开启的

第二个是把原有的 clang-tidy 的 option 改了名字，并且 Linux 上默认关闭。因为现在可以在 linux 上直接用 run-clang-tidy 运行 tidy，不用吧这部分放到构建的时候，所以意义不大。

Windows/MSVC 上因为没法生成靠谱的 compile_commands.json 所以给 release prest 保留了。

另外我试了直接用 VS/CMake 模式打开 esl，但是这种个情况下生成 compile_commands.json 和 MSVC 自己在 clang-tidy 流程下生成有区别，会误报一些错误。所以研究下来也不靠谱

至于 clang-powerful-tools 提供的那个 ps1 脚本，目前看 issue tracker 还有很多问题没有解决。而且我自己本地发现这脚本也基本没法用...

然后就是去掉了 MSVC 内建静态分析，因为这玩意儿感觉有点多余，而且误报率太高了。有 tidy 之后就差不多了

PR 见 https://github.com/kingsamchen/esl/pull/20

第二个MR是加上 ignore_unused，顺带实现了 FORCEINLINE

见 https://github.com/kingsamchen/esl/pull/21

我现在基本是把 github PR 当作 gitlab MR 在用... 😂 另外自从前面把 github action 搭建好之后，提 PR 的感觉是真他妈爽阿。

---

好了这周就这样，下周见
