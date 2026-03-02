---
title: 一周杂记 in Week 4 Feb 2026
categories: CODE-LIFE
date: 2026-03-01 22:32:03
tags: [杂记]
---
本周（02/23 ~ 03/01）是2月份第四周，周日已经是3/1，春节假期也结束了，又回到了打工牛马的状态。

## Life

\#1

这周开始23号正月初七已经是最后一个官方休息日了，我自己24号又请年假多休息了一天，方便找找状态 🤡

于是周三25号正式开始上班，结果第一天就累到人要 burn out；一想到周六还要调休上班，我内心想领礼包提前退休的冲动又起来了

不过还好周六是 WFH 的，所以不用看到老崔；不过还是被特么强行安排了一个会，所以还是很不爽

\#2

周四下了一天雨，晚上蹭了老婆医院的药代请的城中香格里拉酒店的自助餐。

自助餐味道确实不错，不过因为我到的比较晚，就吃了二十来分钟就和我媳妇儿一起去听课了 🤣

听了一个 session 大概弄明白了 医药公司 -- 邀请专家演讲 -- 参会医院医生 这个利益链条了

不过听完一个 session 就跟媳妇儿一起回家了 🤡

\#3

周五晚上请了几个同事吃饭，给 suyang 接风洗尘，也欢送 kidd 第二天就去美国出差。

地方是suyang推荐的台州菜，味道还不错，不过海鲜上感觉处理的一般，比老邻舍差不少。

下回我直接请老邻舍的海鲜好了，让 xdm 体验一下正宗温州海鲜

\#4

周日把书房的地毯给换了，这地毯估计用了3-4年了确实该换了。

不过换完之后有点太毛绒绒了有点不太习惯。

下午和 Hogan 冒着雨上了一次拳击课，因为是这一次买的课程的最后一节，所以打了2个半小时，打完直接累到不行。

不过后面会和 Hogan 继续续课，毕竟还是爽的

## Work

\#1

**Asynchronous Programming with C++ | 8. Asynchronous Programming using Coroutines**

- 手写入门级别的 coroutine classes
- 没啥新意，随便翻翻就行

**CppCon 2023 | std::simd: How to Express Inherent Parallelism Efficiently Via Data-parallel Types - Matthias Kretz** https://www.youtube.com/watch?v=LAJ_hywLtMA&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=27

- 介绍 C++ 26 的 simd；一个 simd<T> 是一个 w-维的向量数据（其实就是对应 simd指令操作数）以及相关的一些操作函数生态
- 但是 simd<T> 不是简单的 simd instructions wrapper，设计上是希望和标准库算法这些做结合

**CppCon 2023 | Plenary: Coping With Other People's C++ Code - Laura Savino** https://www.youtube.com/watch?v=qyz6sOVON68&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=26

- 不是纯技术向的 talk，主要围绕的是在组织内的 social/cooperation 这些；但是我觉得这个反而是一个很好的 talk，因为在公司内更多的问题都不是技术问题

**CppCon 2023 | Back to Basics: Debugging in Cpp - Greg Law** https://www.youtube.com/watch?v=qgszy9GquRs&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=28

- 作为 back to basics 系列的话质量非常高，介绍 gdb/lldb，不过作者作为一个老江湖一些相对少用的 gdb commands 也是容易弄错，作者自己也用 gdb-dashboard
- valgrind + sanitizer
- strace/ptrace/ltrace
- 最后演示了一把 gdb 的 time-travel

**C++ Weekly - Ep 189 - C++14's Variable Templates** https://www.youtube.com/watch?v=2kY-go52rNw

- 之前几乎只用来做 type traits 的 value，但是这视频才发现还可以利用特化来做编译期计算…

    ```cpp
    template<int Value>
    constexpr int fib = fib<Value-1> + fib<Value-2>;

    template<>
    constexpr int fib<1> = 1;

    template<>
    constexpr int fib<2> = 1;

    static_assert(fib<10> == 55);
    ```


**C++ Weekly - Ep 188 - C++20's `constexpr` `new` Suppor**t https://www.youtube.com/watch?v=FRTmkDiW5MM

- constexpr function 中可以用 new operator 了，但是scope里分配的资源必须要用 operator delete 回收之后才能退出
- 所以目前是由各种开脑洞的利用，比如利用 std::array 来回倒腾接收最终结果

**C++ Weekly - Ep 187 - C++20's `constexpr` Algorithms** https://www.youtube.com/watch?v=9YWzXSr2onY

- 目前标准库的算法有相当一部分支持了 constexpr，甚至已经可以编译期对 std::array 做排序了
- 不过也有一些，比如 std::sample / std::accumulate 还没有被 constexpr-ize

**C++ Weekly - Ep 186 - What Are Callables?** https://www.youtube.com/watch?v=lWzHLRN56zQ

- 对于任意 callable，甚至是 member fun，用 std::invoke 解千愁

**How do exceptions work in C++ on Linux?** https://pvs-studio.com/en/blog/posts/cpp/1333/

- 针对的是 Linux Itanium(X64) ABI 下 Clang 对于 C++ 异常的支持机制；通过 LLVM 代码 + Godbolt 反汇编的研究
- **抛出 (Throw)**：`throw` 语句会被翻译为 `__cxa_allocate_exception`（在堆上为异常对象及 `__cxa_exception` 头部元数据分配内存），随后调用 `__cxa_throw` 初始化上下文并触发底层的栈展开。
- **捕获 (Catch)**：`catch` 块会被包裹在 `__cxa_begin_catch`（接管异常，更新状态）与 `__cxa_end_catch`（执行完毕后调用析构并释放异常内存）之间。如果是继续抛出 (`throw;`)，则会调用 `__cxa_rethrow`。
- **两阶段的栈展开（Two-phase Stack Unwinding)**：底层 `_Unwind_RaiseException` 会执行严格的两阶段动作
    - **阶段一 (Search Phase / 查找阶段)**：读取编译器预先埋在可执行文件中的元数据表（Exception Handling Tables），结合 Personality Routine（如 `__gxx_personality_v0`），顺着栈帧向上回溯，寻找能够处理该异常类型的 `catch` 块。
    - **阶段二 (Cleanup Phase / 清理阶段)**：确定目标后，执行第二次栈回溯。在此过程中逐个清理沿途的栈帧，严格调用局部对象的析构函数以保证 RAII，最终强行将指令指针（控制流）跳转到目标 `catch` 代码块中。

**C++ Weekly - Ep 185 - Stop Using reinterpret_cast!** https://www.youtube.com/watch?v=L06nbZXD2D0

- reinterpret_cast 会导致 UB
- 给了一种场景的解法：trivial types 通过 memcpy 来处理，编译器优化之后生成的汇编是一样的

\#2

春节期间想了想，还是给 fawkes 加上了 request body limit 的选项，不过不知道后面 by default 1MB request body 这个行为是不是要调整

Commit：https://github.com/kingsamchen/fawkes/commit/7b6e3dc4c2e51936b1976412a0101b888f3448bf

\#3

之前加完对 expect 100 的支持之后发现 wrk 压测下来的性能衰退了5%多，单请求延迟增加了十几us。

研究了一下猜测是把完整的 async_read 拆成了 async_read for header 和 async_read for body 之后多一次处理带来的。

考虑到 idle read (最多能读入 512 bytes)本身可能已经读入了完整的 header，所以其实可以先检查一下 parser 是否 header done 再继续的

改了之后跑了相同的 wrk bench，发现性能比拆分前还要好5%多 🤡

想了一下因为 data 都有了，不会再触发 syscall read，所以这里大头开销还是多了几次 asio scheduler，parser 检查之后小数据量下都给直接优化掉了

Commit：https://github.com/kingsamchen/fawkes/commit/e423ca2b9a9e77135dbf85c7149cc5d05d014c21

\#4

很早之前就对 byobu 窗口分割之后每个 panel 获取焦点后会加粗边框不爽，但是一直拖着没弄。

这次索性直接问了 Gemini Pro 老师，G老师不仅给我科普了整个是 tmux 的行为，还教了我解决方案

```shell
# ~/.byobu/.tmux.conf
set -g pane-border-style        'fg=colour8,bg=default'
set -g pane-active-border-style 'fg=default,bg=default'
```

试了一下果然舒服多了

---