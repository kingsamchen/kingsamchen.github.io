---
title: 一周杂记 in Week 2 Dec 2023
categories: CODE-LIFE
date: 2023-12-18 21:19:58
tags: [杂记]
---
本周（12/11 ~ 12/17）是十二月的第二周

## Life

\#1

本周没啥观影动力，就电影俱乐部和同事一起看了 **五月十二月 May December** 4/5

故事略为单薄但是极具深挖点。不谈gracei和elizabeth或真或演的冲突对决，你看过早的婚姻约束和压抑对男性造成了多么严重的损伤 XD

\#2

周六的时候教练说你觉得 OK 的话可以约下周日 24 号的科二科三考试。

我觉得时间 ok，反正拖着也不是办法就先考吧，大不了挂了后面再考...

周六预约之后周日上午就收到短信提示预约都通过了，科二科三都是周日第一场

所以希望本周周末的考试顺利，当然周六得去富阳考场提前踩点+练习一天

\#3

周六晚上陪着媳妇儿和丈母娘在媳妇儿医院附近的金华砂锅吃了一顿，味道感觉不错。

主打的大骨火锅，一人两根大骨吃得非常过瘾

就是周末这两天降温有点猛，感觉突然冷了不少

## Work

\#1

这周我们组要 release cut 并且因为要赶一个非常蛋疼的 compliance/dlp 相关的 task 连续好几天晚上加班到8点多..

甚至周五 WFH 的时候晚上8点还在和组里的同事在赶最后一点代码...

最终差不多在美国上班前把核心的逻辑都肝完了，算是可以交差了吧。至于代码质量，以后再说吧...

毕竟这进度安排得这么紧也不是我的问题，老张自己得背锅，整个就一离谱

以后这种 task 我才不管老张是不是 promise 了会不会丢 face，反正我不管。按照老张的尿性，你紧赶慢赶最终赶上了，他还以为我们技术真的全公司 T0 啥 task 都能赶呢。

今年下来是越发觉 US 的工程师不太行，尤其是这些大厂出来的

\#2

本周学习

**CppCon 2021 | The Roles of Symmetry And Orthogonality In Design - Charley Bay**

- 设计上最佳的是提供正交性，这样交互的几个部分不存在直接关联
- 设计 API 时最好考虑并提供对称性，可以增加易用度减少复杂度甚至有时候能减少逻辑 bug
- 非对称的特性要特别注意 edge cases

**CppCon 2021 | Real-time Programming with the C++ Standard Library - Timur Doumler**

- 异常机制中，只要不抛异常的部分，因为不涉及动态分配，所以可以认为是 real-time safe
- stable_sort/partition std::execution::parallel* 这些会涉及到动态分配或者额外创建执行上下文，所以不是 real-time safe
- STL 标准容器都不是，除了 std::array；custom allocator 要满足的话必须要提前分配好整块空间，并且只允许 single-threaded（no synchronization required），并且分配回收的管理要是常数时间
- std::pmr allocator 是个有意思的东西
- pair/tuple/optional/variant 都是，但是 boost/varaint 不是（因为支持了 strong-execption-safe，涉及了动态分配）
- 涉及到类型擦除的都不是，比如 std::function, std::any
- 反直觉的情况：try-lock 也不是，因为可能会导致某个线程的 wakeup
- 同步机制上能提供 real-time safe guarantee 的只有 std::atomic 并且得是真正的 lockless 的

**CppCon 2021 | Down the Rabbit Hole: An Exploration of Stack Overflow Questions - Marshall Clow**

- 某些场景下 std::partial_sort 性能（comparisons & swaps）比 nth_element 都要好，例如查一个序列第二小的元素
- 尽量不要对标准库，尤其是继承C的函数，取地址，因为标准只规定实现上只要调用能满足功能即可，没说一定要有完全一致签名的函数存在
- associative(transparent) lookup 是个不错的 feature
- your smart friends are best debugging tool
- 问问题也是个技术活

**凤凰架构 | 15. 服务网格**

- 简单介绍了一下 service mesh

**凤凰架构 | 16. 向微服务迈进**

- 什么时候应使用微服务
- 讲了一些软件工程相关的“哲学”理念
- 本书完结，撒花

**HBase原理与实践 | 7. Compaction 实现**

- 介绍了 minor compaction 和 major compaction
- 以及各种 compaction 策略

**Trip report: Winter 2021 ISO C++ standards meeting (virtual) https://herbsutter.com/2021/02/22/trip-report-winter-2021-iso-c-standards-meeting-virtual/**

- 关于c++标准会的最新进展 c++23；herb介绍了几个他感兴趣的小补丁
    - lambda可以省略括号()，但是如果有mutable又不可以省略括号了；P1102 解决了这个不一致
    - range使用：std::views::join迭代器返回有问题，引入新组件修复
    - 允许更 generalized 的 time_point::clock
    - 加强std::visit处理继承std::variant 的场景
    - 引入std::to_underlying代替 `std::underlying_type_t<T>`

**Implementing Parallel copy_if in C++ https://www.cppstories.com/2021/par-copyif/**

- std::execution::parallel_policy不要求顺序，这样可能有线性的性能提升，但是对于sort，可能需要同步
- 对于copy_if来说，返回新的对象，这个返回结果，多线程写入，必然需要同步。作者给出了几种实现方案
- 1 简单加锁 2 利用算法规避锁，3 分块 其中某些场景下多线程并没有起到加速的作用。
- 第二种方法 用到了[std::exclusive_scan](https://zh.cppreference.com/w/cpp/algorithm/exclusive_scan) 非常有意思

\#3

周日抽了个时间看了一下 abseil 在内部一些场景用的 low-level allocator

大致上搞懂了他的整体设计和 data flow

不过因为细节上设计非常多的 trick，所以没有太 dive in；不过感觉目前的分析程度已经够用了，哪天有需要完全可以回过头来继续深入细节。

毕竟就算现在花了大把时间深入细枝末节，隔几天可能就忘了，毕竟不常用不是

---

好了这周就这样，下周见
