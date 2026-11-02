---
title: 一周杂记 in Week 5 Oct 2026
categories: CODE-LIFE
date: 2026-11-02 22:20:03
tags: [杂记]
---
本周（10.26 ~ 11.1）是10月份的第五周，也是最后一周，难得又有一个月可以水5周。

## Life

\#1

因为这周六拳击教练有培训，所以我想了一下直接把课提前到周三下午，我可以 WFH 然后下午请小半天假。

难得周二工作强度不大，给周三 WFH 提供了非常好的休息状态，于是下午3点准时出发去乐刻上训练营

这次训练营又是强度爆表，而且2个半小时才结束，真正练习时间多了半小时。。。不好的地方就是 HRV 直接拉到了 48，晚上真的是太兴奋了。

另外一个坑是出发的时候电驴脚蹬掉了，还好银泰附近就有一个修电驴的，训练完之后到了店里花了不到十分钟就修好了，连同打气就花了12块钱。

不过这周 HRV 还是继续拉跨，这感觉每到秋季空气不好的时候就会这样，囧

\#2

jiancheng 这周回国了，于是大家约了周三晚上的饭局。

我训练结束到家休息了一会儿之后就出发了，饭店离家不远，骑个车差不多15分钟就到了。

味道还可以，就是川菜确实有点辣；不过大家主要还是扯淡为主，毕竟我们这群人的爱好就这点，233

\#3

周三晚上秋宝踢被子加上有点降温，周四早上一起来就发现秋宝有点感冒了，开始流鼻涕。

后面几天症状稍微有好转但是还是断断续续流涕 😅

\#4

周四团建，依然选了老套路：御牛道吃饭，然后去火星工厂玩耍。

在火星工厂和 Jed 打了一会儿双人成行，还是接着之前和 suyang 的章节后续，不过后续的章节难度上升了，我们打了快两个小时才打完一章 🤡

\#5

这周终于抽空把 POI 解说最后的看完了，最后几集隔了这么久看，依然非常刀子

看完之后给 UP 充了50的电，算是稍微意思一下。

## Work

\#1

**CppNow 2025 | Runtime Polymorphism with Freedom & Performance - External Polymorphism & Type Erasure** https://www.youtube.com/watch?v=-3Hu29GTdKg&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=23

- 原 talk 很长但是感觉前面都不是核心，中段开始吐槽 std::function 的实现不好，比如 small object optimization 不太透明，并且就算有 SMB，体积超限了依然会有 heap allocation，而且必须要 copyable
- 后面是展示他们自己做的一个 zoo library 里的 AnyContainer

**CppNow 2025 | C++ as a Microscope Into Hardware - Linus Boehm** https://www.youtube.com/watch?v=KFe6LCcDjL8&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=25

- 这个 talk 是真挺不错的，主要是因为 C++ 的 zero-cost abstractions 可以用作一个工具/媒介去研究对应生成的汇编以及内核+硬件层面的一些运作机制
- 虽然大部分东西都已经知道了，包括那个 mov 指令是 turning-complete 🤡

**CppNow 2025 | Introduction to C++ Function Template Partial Ordering - Matheus Izvekov** https://www.youtube.com/watch?v=yY2qiL2oI2Y&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=24

- 函数模板重载决议的时候，more specialized 胜出，然后解释了一下编译器如何判断这个 more specialized 其实就看哪个模板能接受的范围更小（narrower set）
- 类偏特化的模板选择也是类似的方式（我觉得这个算是更重要的点）
- 后面则是设计比较恶心的 non-deduced context 部分了，一般也不会遇到，不同编译器自己的行为也有不一样的地方

**C++ Weekly - Ep 556 - C++23's is_implicit_lifetime** https://www.youtube.com/watch?v=kJ9dJfuUCHg

- 这个 type trait 用来检测某个类型是否支持 implicit lifetime
- 那什么是 implicit lifetime 呢，这个主要是为了给 C 那部分打的补丁

    ```cpp
    void* p = malloc(sizeof(int));
    *reinterpret_cast<int*>(p) = 42;  // Not UB
                                      // int* lifetime starts implicitly
    ```

**CppCon 2023 | Lightning Talk: You Should Use AddressSanitizer - Brody Holden** https://www.youtube.com/watch?v=1RxMPEVBMJA&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=78

- 介绍各种 sanitizers

**C++ Weekly - Ep 66 - Variadic fmin for C++11** https://www.youtube.com/watch?v=Mzi5SHi13MM

- 省流，利用 std::initializer_list trick 来替代 fold expression

**C++ Weekly - Ep 65 - C++11's std::fmin** https://www.youtube.com/watch?v=VXgTKrSS1-c

- 利用 variadic template + fold expression 实现 variadic_fmin

**C++ Weekly - Ep 64 - C++11's std::min (and my version)** https://www.youtube.com/watch?v=z_fnMdzfWAQ

- 标准库的 min 有 initializer_list 的重载以支持多个参数，但是如果编译器优化不掉隐藏的 array就会比较麻烦
- jason 表演了一下用 variadic template 模仿了一个
- 另外注意 min 返回类型是 `const T&`

**C++ Weekly - Ep 63 - What is Negative Zero?** https://www.youtube.com/watch?v=18v-fb_TepU

- 浮点数的 .0 区分 +/-
- 那如何知道符号呢，用 `std::signbit` 不用自己抠 bit 了

\#2

这周让 OPS 帮忙把基于 fawkes 的 v2 api server 部署到了 QA 环境，但是还需要修改 ngx 的规则才能把流量转到迁移好的 handlers 上，所以就一直先忙其他的。。。

这周在 review 过程中又修了几个 fawkes 的问题：

- [Fix reset connection in accept-queue causes silent exit- #47](https://github.com/kingsamchen/fawkes/pull/47)
- [Optimizes connection-info construction- #48](https://github.com/kingsamchen/fawkes/pull/48)
- [Introduce rst avoidance delay for body-limit errors](https://github.com/kingsamchen/fawkes/pull/49)

\#3

这周抽空让 AI（Opus 5.5） 评估了一下 scheduler 项目加上 pch 的编译提效收益

答曰效果一般，大概只有5%-7%的优化，而且还要调整 ccache 才能兼容 pch，整体上不如 ccache 的调优。

所以最后就变成拿 jenkins job 搭了一个 ccache warmer，调整了一下单机 ccache 的容量限制和参数

最后发现 cache warm 的时候确实效果拔群

---

好了这周就这样，下周见
