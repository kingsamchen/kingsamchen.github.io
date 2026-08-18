---
title: 一周杂记 in Week 2 Aug 2026
categories: CODE-LIFE
date: 2026-08-17 22:44:28
tags: [杂记]
---
本周（8/10 ~ 8/16）是8月份的第二周，这周天气受到台风尾流影响，整体不太行。

## Life

\#1

公司行政团队在周末的时候发了群消息说，如果周一有大雨，大家可以在家办公。

于是周一早上起来发现雨似乎不是很大，再一看群也没说居家的事情。

左等右等都准备出门了，发现K指导在群里说了，然后下面立刻一堆随大流的；那我自然不能抛弃队形啊，于是也蹭了一天居家。

不过这周受到台风尾流影响，有几天还是下暴雨的。

\#2

不过周六中午和周日都没怎么下雨，所以这周末的运动还是照常的。

周六中午在跑步机上练了半小时的爬坡，待我坚持3个月，看看减脂效果如何。

周日单独一对一上了一节拳击课。

不得不说一对一还是很不错的，虽然有点累，因为不能换着休息，但是时间多了之后可以针对技术动作做一些细致的调整。

后面准备继续续课了

刚好练完拳击休息十来分钟在上一节核心，一天运动量直接打满。

\#3

周五早上突然接到我妈电话，说媳妇儿表哥没了，丈母娘今天要紧急回去，她来临时换班。

我第一反应：? 啥就没了?

和媳妇儿确认后给我妈买了一张中午就能到的票，然后丈母娘买了傍晚回去的票。

傍晚的时候媳妇儿问我今晚最后一班的商务座还能买到吗？我一看发现其实已经没了，只是缓存数据而已。

于是媳妇儿周六一早4:30醒过来收拾，5点出发，赶6点第一班高铁。

本来说让我送，我也早早睡觉了，不过事后看手表实际上11点半才睡着，五点醒来其实也没睡够。还好媳妇儿说可以打到车，让我继续睡。

火化出殡下葬搞得很快，周六中午已经结束回祠堂吃饭了，媳妇儿也买了下午的车，只不过因为大雨晚点，一直到19:10才到，开车到家也8点了。

不过实话说，年纪大了还是要多注意自己的身体指标，不然真的容易噶

## Work

\#1

**CppNow 2025 | Achieving Peak Performance for Matrix Multiplication in C++ - Aliaksei Sala** https://www.youtube.com/watch?v=CeoGWwaL8CY&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=33

- 听起来大致是把朴素的矩阵乘法优化成近似 BLAS 效率的实现
- 不是这个领域的，所以站在我自己的角度是：那为啥不直接用 BLAS lib？

**CppNow 2025 | Overengineering max(a, b) - Mixed Comparison Functions, Common References, and Rust's Lifetime** https://www.youtube.com/watch?v=o2pNg7noCeQ&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=32

- 这个 talk 有点费脑子，不过核心意图还是明显的，就是试图通过一个自己实现的泛型 max 来说明当前 C++ 的 max 存在明显的类型安全+内存安全的问题，使用上容易踩坑，进而推导出 reference 的引入对语言的发展产生的负面影响

**C++ Weekly - Ep 98 - Precision Loss with Accumulate** https://www.youtube.com/watch?v=vLozydgjHrc

- 省流，gcc/clang 默认不会告警 double → int 的 precision lost；而 msvc 默认就会

**C++ Weekly - Ep 97 - Lambda To Function Pointer Conversion** https://www.youtube.com/watch?v=Cmk0Tlo1eCA

- 众所周知 stateless lambda 可以转换为 function pointer
- 所以比起 `std::vector<std::function<>>` 我们使用 `std::vecotr<fptr>` 有时候更有优势，至少可以作为一种思路

C**++ Weekly - Ep 96 - Transparent Lambda Comparators** https://www.youtube.com/watch?v=P6_6bXPGYy8

- 核心是利用 visit 常用的 overloaded pattern 让 transparent comparator struct 支持多个 lambda 作为 comparator 重载

**C++ Weekly - Ep 95 - Transparent Comparators** https://www.youtube.com/watch?v=BBUacofxOP8

- 介绍 transparent comparator
- C++ 20 之前只有 map/set 支持 transparent comparators

**C++ Weekly - Ep 94 - Lambdas as Comparators** https://www.youtube.com/watch?v=dvLKY-oWqn0

- 用 lambda 作为 set/map 的 comparator
- 需要注意 lambda default ctor 是 deleted，所以要走 copy comparator via ctor arg 的方式

\#2

因为 alma9/ubi9 上编译器升级到了 gcc-15，所以打算把之前对于 mold linker 的使用给 modernize 一下，但是踩了一点坑，这里记录一下。

- gcc-12 开始就认得 mold，所以可以使用 `-fuse-ld=mold` instruct 编译器使用 mold；但是刚好alma9/ubi9 上从源包就可以装到 CMake 3.31，所以更省事

    ```makefile
    set(CMAKE_LINKER_TYPE MOLD)
    ```

    MOLD 是固定值 https://cmake.org/cmake/help/latest/variable/CMAKE_LINKER_TYPE.html

- 但是要注意的是 gcc 会寻找的是 `ld.mold` 所以光吧 `mold` 这个装到 $PATH 是没用的，需要保证 $PATH 可以访问到 `ld.mold`

所以最终的操作步骤变成：

1. 还是把 mold 安装到 `/opt/mold-${vesrion}`
2. 创建 `/usr/local/bin/mold` 和 `/usr/local/bin/ld.mold` 符号链接到 `/opt/mold-${version}/bin/mold`

这里目录带版本是方便 vagrant provision 的时候就能升级到新版本

\#3

这周基于 ubi-9 的 Aug Release 已经部署到了 Go 环境，也验证了解决内存问题的方案的效果

所以这个困扰了一个多月的问题也算解决了，后面可以逐步把产线上 api server 的 mem spec 降下来了

不得不说，现在 AI 实在太强了

\#4

这周另外一个大动作是把项目的 cpp standard 升级到了 23

而且果不其然遇到了不少的编译错误，主要是标准升级后三方库开始增强约束导致的，最明显的是 fmtlib 的 format string 现在默认做 compile-time checks，所以要求必须是 compile-time evaluation

另外一个比较明显的差异是 nlohmann-json 的 `operator==` 的隐式转换因为实现上切换到了 `operator<=>` 所以隐式转换只支持 trivial types

像其他的什么 date library 和 C++20 calendar datetime 冲突都是小事了。

这里再次夸奖一下现在的AI，我差不多确认了目前这几种编译错误之后，就让 cursor/grok 跑了一个 loop，自动编译然后自动修复错误

---

这周就这样，下周再见。下周估计事情多
