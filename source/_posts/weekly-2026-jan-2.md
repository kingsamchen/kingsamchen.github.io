---
title: 一周杂记 in Week 2 Jan 2026
categories: CODE-LIFE
date: 2026-01-11 22:45:54
tags: [杂记]
---
本周（01/05 ~ 01/11）是1月份第二周。

## Life

\#1

因为丈母娘喜欢炒排骨/大骨和海鲜一类的，所以不粘锅太容易被搞坏涂层。

所以这周买了一个苏泊尔的铁锅，自己照着说明书用食用油给它开了锅。

不过好像开锅的效果一般。。。算了就先这么用吧，反正就算有点粘也不太影响使用，就拿这个来处理一些硬壳类的食材就好。

\#2

这周 HRV/RHR 的指标又开始全面下滑，有点搞不懂为啥。这周我大部分时候跑步机都是 8~9kmh 的配速压心率，除了周五佳明给了一个很极端的乳酸阈值训练方案把我搞得很累之外。。。

所以现在又要调整情绪和生活作息了，看看是不是这部分搞得。

不过周六下午上了第五节的拳击训练营，这次打的太多，最后直接来了一个15组的完整动作训练，搞得强度爆表，其中有组还脑抽踢腿的时候提到教练腰子，不过还好最后时刻收力了。。。

下周先做一个偏轻松点的恢复性训练吧

\#3

周日上午开车送媳妇儿去宋城，她约了高中同学一起。

结果过了没几个小时，媳妇儿一直吐槽说一点意思都没有，街道都是那种仿古商业街，之前类似的去过好多次了，没啥兴趣。

演出她觉得很隆重很盛大很像春晚但是她不喜欢...

不过媳妇儿高中同学倒是觉得好看好玩，所以也还成吧。

媳妇儿说她同学之前在房企后来被暴力裁员了连工资还欠了几个月马上就要仲裁开庭了，而且她21年高点在上车了一套老小区的老房子...

哎

\#4

周日送完老婆回来之后就开始加班了，老崔非要赶着上HA，只能放到周末。

于是几个人还要拉着US的一起从早上9点开始搞，一直搞到下午3点多。

到了晚上看到消息说 calendar 的业务有问题，最后整个都还回滚了，下周末接着弄...

\#5

过去两周遇到多次电脑突然就黑屏死机，Windows 各种内核提示相关的错误。

想了一下觉得要么是 vmware 的问题，要么就还是之前我开了 DOCP超频的问题。

于是先升级了 vmware 到 25h2，然后刷了主板的 firmware 和 BIOS。

结果到了周日傍晚又特么黑屏死机了...

算了算了，直接关掉了主板自带的超频方案，直接切回 AUTO 了。。。

## Work

\#1

**CppCon 2023 | Single Producer Single Consumer Lock-free FIFO From the Ground Up - Charles Frasch** https://www.youtube.com/watch?v=K3P_Lmq6pw0&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=17

- Lockless SPSC FIFO queue
- fifo1: simplest/puriest spsc, using push-cursor & pop-cursor; however it has data race
- fifo2: using atomics for two cursors, great but have performance improvement
- fifo3: using padding to avoid false sharing, also use non-seq-cst memory order
- fifo4: introducing cached cursors with
- fifo5: pusher/poper encapsulation
- 另外 cached cursor 在 **CppNow 2025 | Beyond Sequential Consistency - Leveraging Atomics for Fun & Profit - Christopher Fretz** 这个分享里也有，而且很多实现感觉都差不多
- 实际项目还是直接用口碑好的库吧

**CppNow 2025 | Post-Modern Cmake - From 3.0 to 4.0 - Vito Gamberini - C++Now 2025** https://www.youtube.com/watch?v=K5Kg8TOTKjU&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=54

- 非常棒的很新的 CMake 相关的分享，source-tree/build-tree/install-tree 三部分的划分很有意思
- source tree 就是 `target_sources()` 这部分，最近几个版本支持的 FILE_SET 是一个很有意思的特性，回头可以专门研究一下，这部分应该是为了取代 `target_include_directories()`
- install-tree 的内容太多了，而且有个 CPS(Common-Package-Specification) 的 proposal 在

**C++ Weekly - Ep 212 - An RPG In C++20 - Part 2: Finding Errors As Soon As Possible** https://www.youtube.com/watch?v=sZBVLDbJRwg

- enable clang-tidy + cppcheck，不过 uninitialized variable 不是很好检测，尤其取了指针传到函数里，编译器和静态分析都不知道你这里面是不是直接往地址写

**C++ Weekly - Ep 211 - C++20's Bit Manipulation Functions** https://www.youtube.com/watch?v=8EyZ6mRDlx8

- 引入了一些位操作的便捷函数，例如判断 power-of-2，上下圆整到 power-of-2
- 更加安全简单的左移右移
- 还有基于直接指令的 popcnt，count-zero/one 这些

**C++ Weekly - Ep 210 - An RPG in C++20 - Part 1: Getting Started With SFML & Dear ImGui** https://www.youtube.com/watch?v=av0SYeUxVMU

- 直播写代码 part 1（毕竟逆序）

**C++ Weekly - Ep 208 - The Ultimate CMake / C++ Quick Start** https://www.youtube.com/watch?v=YbgH7yat-Jo

- 在 Jason 自己的 https://github.com/cpp-best-practices/cmake_template 基础之上根据自己需要调整 CMake
- 最核心的应该是推介自己的这个 repo 吧

**C++ Weekly - Ep 209 - An RPG in C++20 - Part 0: The Plan** https://www.youtube.com/watch?v=kwFcRvxy0D0

- 直播写代码第一弹，这部分主要是订立目标

**A UNIVERSAL MODEL FOR ASYNCHRONOUS OPERATIONS** https://isocpp.org/blog/2013/09/new-paper-n3747.-a-universal-model-for-asynchronous-operations-christopher

- 这篇是 Chris Kohlhoff 在2013年提交的一篇提案，旨在说明用 std::future 作为 async operations 的基础模型是不合适的，并且提出了一个可以适配各种特定模型的 universal model，这个 universal model 也是目 boost 1.56 附带的 asio 开始使用的模型
- 网络操作比起内核同步来说并不慢：从 host-a 发一个 64-byte udp 包到 host-b，10GbE 网络，基本只需要2ms；而相同硬件条件下唤醒并切换到一个阻塞在 std::future 上的线程也差不多需要 2~3ms
- future-based asynchronous model 先天性能有瓶颈
- future-based model 使用时，async op 往往和 caller 不在一个 execution of context，可能出现 caller 还没来得及设置 continuation async op 就完成了，所以为了保证正确性，future model 需要额外的 cooridination（10-15 ns per use）；而使用 callback 整个流程就非常纯粹，不需要额外的 coordination
- 如果是 composed async-ops，那么上面这个 coordination overhead 会显著增加，而底层通信通常都需要 composition of multiple async-ops
- async-op 签名如果直接用 std::future 则会缺少扩展性，例如如何适配三方 future 类型
- 引入 handler_type 和 async_result 两个 class template，通过 template specialization 来适配不同的方案，并且相对原来的 callback 没有额外的开销

\#2

这周把 fawkes 的 connection-level timeout 给实现好了，整体还是比较容易的，除了一丢丢的小坑

目前支持

- `idle_timeout` 建链后允许 idle 多久，其实就是上次请求结束后收到下次请求数据的最大间隔；不过有些语言/框架实现上，建连之后的第一次请求等待时间不算 idle
- `read_timeout` 请求最多能读多长时间，避免对端一直卡着 slowries attack
- `serve_timeout` 一次请求完整的最长 serve 时间，包含上面的 `read_timeout`；这个和很多语言/框架的 timeout 语义是一样的

PR见 https://github.com/kingsamchen/fawkes/pull/19

本来想把 per-operation cancellation 也一起做到这个 PR，但是研究了一下发现 ASIO 支持这部分的方式还不少，选择哪种还需要研究一下

\#3

这周把 fawkes 之前关掉的 lambda coroutine tidy check 又打开了，并且认真研究了一下 lambda coroutine

fawkes 的使用没有啥问题，所以单独 `// NOLINT(xx)` 掉

具体内容看 {% post_link the-lambda-coroutine-fiasco The Lambda Coroutine Fiasco %}

\#4

虽然 fawkes 使用 lambda coroutine 的方式没有内存问题，但是对于 user-handler 的存储到有问题。

之前是直接无脑的 forward-capture 到 closure 然后存到 `std::function<>` 里，后来想到如果给的是一个 lvalue，那么地下存的其实是 lvalue-reference，如果这个 lvalue 恰好又 goes out of scope 那岂不就挂了。

于是想到研究一下 ASIO 对于 completion_handler 的处理，发现人家是如果进来的是 rvalue/rvalue-reference 就 move，lvalue-ref 进来的就 copy

一个作证是创建一个带 `std::unique_ptr` function object，那么这个 object 就是 movable but non-copyable，直接拿这个去测例如 `asio::steady_timer::async_wait(handler)`：

- 如果是右值，不管是 pr-value 还是手动 move 的 expiring value 都能用
- 如果是左值，直接编译失败，提示 copy ctor 是 deleted

这个设计相对来说是很合理的，所以 fawkes 也改成这样了。

PR 见 https://github.com/kingsamchen/fawkes/pull/20

---

好了这周就这样
