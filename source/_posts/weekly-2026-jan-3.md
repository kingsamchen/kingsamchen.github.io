---
title: 一周杂记 in Week 3 Jan 2026
categories: CODE-LIFE
date: 2026-01-18 23:27:31
tags: [杂记]
---
本周（01/12 ~ 01/18）是1月份第三周。

## Life

\#1

这周 HRV/RHR 指标略有好转，说明确实和情绪心态有强相关

另外我发现 HRV 的合适区间确实会根据历史数据动态调整的...所以意味着最近一个多月我的心脏指标确实劣化的可以...

\#2

周日上午复出再跑步机压心率跑+走练了50分钟，看记录心率区间的时间比还行

就是练腿的时候141KG第一次一组做了20个有点伤到了，可能和呼吸闭气有关系，导致一下有点想吐...慌得不行，还好后来慢慢缓过来了

以后真不能大重量一组强扛着做太多

\#3

周日中午吃完饭丈母娘就要出发参加乒乓球俱乐部的活动，所以一下午就我和媳妇儿在。

结果秋宝今天非常让人头疼，下午即不好好睡觉喝奶也不积极；下午加起来就睡了50分钟，喝奶更是只有几十毫升，老婆各种喂都喂不进去...

明明下午带下楼转了一圈还挺高兴的，但是回来即不困也不吃奶，倒是弄了点香蕉泥全吃完了。。

晚上六点多实在不行让丈母娘早点回家，结果也是没喂太多...

还好晚上8点宵禁入睡到是正常的

\#4

这周开始看 Daredevil Born Again 剧集了，这是版权回归迪士尼/漫威之后新搞剧集，不过号称风格和故事背景会承接 netflix 的三部曲

## Work

\#1

**Asynchronous Programming with C++ | 12. Sanitizing and Testing Asynchronous Software**

- sanitizer 的部分介绍的真不错，非常适合新人入坑
- testing 的部分就一般一些，并且原以为会有介绍如何支持 asio coroutine 的 unit testing，结果发现用来讲解的前面章节设计的玩具 coroutine

**CppNow 2025 | C++ Memory Safety in WebKit - Geoffrey Garen** https://www.youtube.com/watch?v=RLw13wLM5Ko&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=55

- webkit team 的 memory safety 实践
- 启用 bounds checking / pointer check 这一些 runtime check 就有明显的改进，而且可以有没有明显的 performance regression
- 另外他们只用 clang，所以用了不少 clang 专有的 attributes 以及使用一些wrapper 来更好的辅助编译器和静态检查提前查到问题
- 比较麻烦处理的是 UB
- 另外 webkit team 走的另外一个方向是引入 Swift

**CppNow 2025 | From SIMD Wrappers to SIMD Ranges - Part 1 Of 2 - Denis Yaroshevskiy & Joel Falcou** https://www.youtube.com/watch?v=CRe20RdU_5Q&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=57

- 介绍了一些 simd 优化的基础，例如 overlap 处理这些
- 核心是 eve 这个 simd 库，给了很多优化之后和标准算法库的对比
- 另外演讲者认为 std::simd 某些地方不太行

**CppNow 2025 | From SIMD Wrappers to SIMD Ranges - Part 2 Of 2 - Denis Yaroshevskiy & Joel Falcou** https://www.youtube.com/watch?v=20rl8xDaJrg&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=56

- 上面演讲的 part2，更加深入的讲一些优化设计

**Generalized "value" types and prototypal operation for C++ objects** https://ossia.io/posts/valuetypes/

- member name/value 的小成本野生解法
- 感觉不如用更成熟的方案

**The Asio asynchronous model** https://www.open-std.org/JTC1/SC22/WG21/docs/papers/2021/p2444r0.pdf

- 本质上还是13年提出的那个 universal model，只不过基于20的特性做了一些更新

**Comparing WaitOnAddress with futexes (futexi? futexen?)** https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265

- 见表格


    | Aspect | futex (Linux) | `WaitOnAddress` (Windows) |
    | --- | --- | --- |
    | Core idea | “Sync object out of nothing” using a memory location + kernel help only when contended ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) | Same “sync object out of nothing” idea ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) |
    | What it *models* | Conceptually a **semaphore with max count 1** → like an **auto-reset event / single-token semaphore** ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) | A **general** primitive: *wait for a memory value to change*, can mimic many patterns ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) |
    | Memory size | Fixed **4 bytes (32-bit)** ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) | **1, 2, 4, or 8 bytes** (your choice) ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) |
    | Who controls the contents | **Controlled by futex rules**; you can’t freely store arbitrary state in it ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) | **Controlled by you**; typically your own bookkeeping state ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) |
    | Access requirements | Must use **atomic primitives** ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) | **No restrictions** (from the post’s perspective) ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) |
    | Scope | Can be **cross-process** via shared memory ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) | **Single process only** ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) |
    | Operations (user-view) | “**Down**” (claim token / wait), “**Up**” (release token / wake) ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) | “**Wait for value to change**”, and **wake waiters** ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) |
    | Spurious wakes | Yes ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) | Yes ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) |
    | Kernel transitions | Enters kernel **only to wait or wake**; uncontended path stays in user mode ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) | Same behavior: kernel only on wait/wake, otherwise user-mode ([Microsoft for Developers](https://devblogs.microsoft.com/oldnewthing/20170601-00/?p=96265)) |

**神奇的std::atomic<T>::wait()** https://zhuanlan.zhihu.com/p/413660695

- C++ 20 引入的 atomic 可以直接 wait/notify 依赖于系统的内核支持，Windows 上就是 WakeOnAddress
- 不过要注意不管是 WOA 还是 futex 都是会 spurious wakeup 的，而 C++ 20 则主动把这部分给处理了，wait 不会因为 spurious 返回
- WOA 和 futex 区别参考上面这篇 post

\#2

因为 `fawkes::response::text()` 提供了 `std::string_view` 和 `std::string&&` 重载，所以很不意外的，如果提供 `const char*` 就会导致重载决议失败，目前使用的 workaround 是显式指定候选。

在考虑要不要利用 SFINAE/Concept 解决这个经典的问题时候，瞥了一下 esl 的 strings/split 的实现，因为它也有类似的问题。

重新回顾了一下实现之后感觉之前的处理不太对，连 `const std::string&&` 这种奇怪的 case 也主动兼容了，所以把 esl 修正了一下 https://github.com/kingsamchen/esl/pull/27 另外顺带把 esl 的 hook 和 fawkes 对齐了

至于 fawkes::response， 这个想了一下严肃接口几乎没有用 const char* literal 的，所以不应该加，所以结论是保持不变。

\#3

这周末重新拿起 Asynchronous Programming with C++，复习了一下之前看的那三章，然后继续看新的内容。

后面会抽时间慢慢把这本书看完。

\#4

这周本来打算抽个时间做一个 commit 把 fawkes middleware 改造成支持 coroutine，核心是每个函数都要变成 coroutine，因为传染性嘛...

但是周日带娃事情太多所以没搞，下周有时间搞吧。

---

好了这周就这样
