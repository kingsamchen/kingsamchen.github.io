---
title: 一周杂记 in Week 4 Feb 2025
categories: CODE-LIFE
date: 2025-03-04 21:24:44
tags: [杂记]
---
本周（02/24 ~ 03/02）是2月份的最后一周，周末正式进入三月份

## Life

\#1

这周遇到了入睡障碍，好几天晚上躺床上个把小时硬是没睡着。大脑也不知道为啥一直处于警戒状态，完全没法放松下来。

连续几天都是到了后半夜差不多三四点实在不行了爬起来吃了褪黑素才慢慢睡着。

和媳妇儿讲了一下大概，她说根本原因估计是我内心焦虑导致的。

做了几轮心理舒缓建设后，周末两天睡眠终于算是正常了...

\#2

周三下午楼下的邻居出差回来，于是我也不再需要每天给福宝铲屎喂粮了。

不过福宝可能连续多天在家里没法出去，闷久了导致有点焦虑抑郁情绪，邻居说当天就发现他频繁上厕所。

一开始以为是尿路感染了，带去了医院做了一通检查，查下来没有任何异常。医生说可能就是单纯的焦虑情绪导致的...

\#3

老张在US那边的叙事一直都是：他自己很努力，产品做的糟糕完全是因为杭州这边的人不努力。

所以在被 Eric 连续多次 complain 东西做了五年了为什么还这么差之后，老张终于觉得自己应该整顿外包了，给 hz mail team 每个人都安排了 one one

不过估计前面的人吸收了老张相当一部分骂人的火力，到我这里的时候已经完全都是客套话了。

摊上一个这样的 head 没啥办法，只能对待阿兹海默症患者一样哄着他。

老张管理上非常不喜欢女性，但是我觉得老张本质上心理情绪和女性一样，都是弱者思维：我没有问题，问题都是你们的。我怀疑就是这种同类思维导致他对女性雇员非常排斥。

\#4

周六中午和老婆吃了顿大渔，好久没吃这一顿感觉非常完美。而且第一次开车去的西溪天街，停车居然非常轻松，空位很多，直接给体验大大加分。

吃完后开车去了青山湖想趁着好天气散个步，结果嘛，我和媳妇儿都是对风景三分钟热度...

而且周六这天太热了，包里的一瓶水都喝完了还额外买了一盒上口爱。

最后加起来开车两小时，逛了半小时...

我觉得我们都不是那种喜欢旅游的人，风景啥的真的没啥吸引力，撑死5分钟就厌了。

\#5

周日抽了不少时间，把 Prototype2 给全成就解锁了。

![](img/prototype2-all-achievements.jpg)

因为选的是保留普通模式的等级和技能进入的 Hard 模式，所以其实之前主线流程就推的非常快。

Hard difficult mode 就 Alex Mercer 比较难打，死了几次，但是好在可以在最近的一个阶段重新打。所以多打几次也就通关了拿下 Hard 模式成就。

不过有一说一，这种ACT游戏玩多了之后发现 prototype 系列的打击感确实一般。

\#6

周日想着天气好，猫猫们应该都出来晒太阳了。

带着航空箱本来想带着皮蛋去医院绝育，结果一到位置居然发现了乐乐...

那就 first thing first，花了点功夫先把乐乐塞进了航空箱，带去医院打了第二针疫苗。

不过因为乐乐也不傻，这样被忽悠了一次之后估计第三针应该不太能打了。不过医生说这种成年猫一般两针也差不多了，第三针打不上也问题不大。

解决完乐乐的事情之后带着航空箱回到了猫猫营地，用钓猫竿花费了大半个小时终于趁着皮蛋玩钓猫竿的时候一下逮住了。

不过带到医院之后发现这家伙应激反应特别大，在诊间里上蹿下跳飞来飞去，一边飞还一边漏尿...还好尿都溅射到医生身上了 🤡

和医生讨论之后放到专门给狗准备的隔间里，下周一准备手术；希望这两天能有效减缓应激，这样可以在医院休养几天再回去。

\#7

周四和 iker 约了西溪博纳的鬼才知道，为了方便开车去的。看完电影后还顺带开车送他回去，当作自己开车兜风了。

本周观影

- **诡才之道 鬼才之道** 3.5/5 题材有点意思，但是深度上差一些。不过你要说这片风格就和它的主旨一样，只追求顺其自然当个废物也没啥不好也行...张榕容是真的漂亮

## Work

\#1

**CppNow 2024 | Detect C++ Memory Leaks with ALSan: Attachable Leak Sanitizer - Bojun Seo** https://www.youtube.com/watch?v=9f5hd-8suVE&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=39

- 利用 ebpf hook alloc/free 的函数，每次 allocation 的 bookkeeping 都会挂接成树，不过 leak 用的是他们自己的一个 unreachable 的算法
- 这个思路和做法有点意思，并且主要针对的是有时候没法用 lsan 跑到可以复现的位置

**CppNow 2024 | Testability and C++ API Design - John Pavan, Lukas Zhao & Aram Chung** https://www.youtube.com/watch?v=DiQF7W58Fjs&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=40

- 实话说有点失望，因为都是老生常谈的那些内容，没有特别新的 insight

**CppCon 2022 | Back to Basics: Standard Library Containers in Cpp - Rainer Grimm** https://www.youtube.com/watch?v=ZMUKa2kWtTk&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=87

- 科普教育级别的概览，有时间就看

**CppCon 2022 | Introduction to Hardware Efficiency in Cpp - Ivica Bogosavljevic** https://www.youtube.com/watch?v=Fs_T070H9C8&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=90

- micro optimization 方面的东西，他们家博客也非常不错
- make memory access sequential or easier to predict
- make code enable auto-vectorization
- make data fit in data cache, employ CPU data prefetch (using custom allocator if necessary, i.e. perfect layout of linked-list)
- optimize data members layout

**C++ Weekly - Ep 389 - Avoiding Pointer Arithmetic** https://www.youtube.com/watch?v=YahYVRS1Ktg

- 第一个思路是用 `std::next()` 但是要注意第二个参数可能要 cast 成 `ptrdiff_t`
- 剩下的思路都是使用 `std::span` 的变体

**C++ Weekly - Ep 388 - My constexpr Revenge Against Lisp** https://www.youtube.com/watch?v=NQEE0k9i7FA

- 作者做了一个基于 constexpr 的 compile-time evaluator

**C++ Weekly - Ep 387 - My Customized C++ Programming Keyboard!** https://www.youtube.com/watch?v=LwxBLG8aGlo

- 客制化键盘，按键可以对应成 `const` `constexpr` `[[nodiscard]]` 这些关键字
- 实话说节目效果大于实际意义

**How do I decode a #pragma detect_mismatch error?** https://devblogs.microsoft.com/oldnewthing/20220427-00/?p=106537

- 教学如何解读 MSVC 专属的 detect_mismatch 的错误
- 这个 pragma 是 MSVC 用来解决 ODR-violation 的

**C++20 Ranges: The Key Advantage - Algorithm Composition** https://www.cppstories.com/2022/ranges-composition/

- 展示了一下 ranges/views 最基本用法
- C++ 23 的 library 才把 `to()` 这部分补全

**How can I force a WriteFile or ReadFile to complete synchronously or hang, in order to test something?** https://devblogs.microsoft.com/oldnewthing/20220425-00/?p=106526

- 利用 named pipe 来做到 read / write call hang 住并且有预期的结束
- 这个思路在 Linux 下也可以用

**Make declaration order layout mandated** https://www.sandordargo.com/blog/2022/05/04/cpp23-P1847R4-Make-declaration-order-mandated

- 解释了一下 C++ 23 不允许改变 member declaration order 在编译时候布局得 paper
- 目前如果同一个 access level 下的 member 被分散到几个block，编译器可能会 reorder 他们

**Mysterious Memset** https://vector-of-bool.github.io/2022/05/11/char8-memset.html

- 本质是 pointer aliasing 阻碍了优化
- std::u8string 能让编译器确信输入的 `int*` 不会有额外的 side-effects

**Should I pay attention to the warning that I’m std::move‘ing from a trivial type? Part 1** https://devblogs.microsoft.com/oldnewthing/20220512-00/?p=106651

- 第一种方法是定义空的析构函数把类变成 non-trivial，第二种是专门定义一干这个事情的函数，然后内部 `//NOLINT` 掉
- 但是严格来说，尽量避免 move trivial types，因为很容易导致 use after move 的行为，并且设计上如果考虑到需要复用 moved-from objects，最好提供一个 reset 这种明确的语义来准备做 reuse；这块我记得有个 talk （cppcon2020或者2021）提到过

**Speeding up Pattern Searches with Boyer-Moore Algorithm from C++17** https://www.cppstories.com/2018/08/searchers/

- 简单介绍了一下 `std::search()` 提供 bm 和 bm-horpool 的 searcher 优化

\#2

先抽了个时间把之前看的介绍 C++ 20 chrono/datetime 部分的 feature 自己用代码过了一遍

PR: https://github.com/kingsamchen/Eureka/commit/f390127224c4a26712d5869353272a847c6a4153

chrono 这套确实挺优雅，但是理解门槛会高一些。

\#3

重新看了一下 esl 的 cmake files 大概有一个初步的想法，但是做起来估计 cornor cases 会不少。

粗略的想法是：

1. 把 `esl_common_compile_configs()` 改成调用统一，内部区分 INTERFACE 和 non-INTERFACE，因为 INTERFACE target 实际上非常特别；而 header-only libs 其实有不能算少
2. 把 language standard 换成针对 target 的 `target_compile_feature()` 来设置。
    因为对于一个比较大的 project 可能会针对某些个 target 单独试验新 language std 的需求，以方便来做 gradual adoption
    不过因为这个其实对 esl 这种 lightweight lib 来说没啥意义所以优先级其实不是很高。有做这个想法纯粹是因为当初想着有机会的话可以 remake 公司项目...
3. target folders 这个用函数抽象掉
    这个功能其实只针对那些生成 IDE 配置（例如 VS 或者 XCode这种）的场景才有效，但是每次写起来都是挺烦的

后面会抽一些时间来试着搞一下，但是不保证一定会做 🤡

---

好了这周就这样，下周见
