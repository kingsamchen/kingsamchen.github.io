---
title: 一周杂记 in Week 4 Mar 2025
categories: CODE-LIFE
date: 2025-03-30 23:55:05
tags: [杂记]
---
本周（03/24 ~ 03/30）是三月份最后一周，下周二就是4月1号了。

本周气温腰斩转冷，真的是有点不太适应。

## Life

\#1

老婆20年买的 iPhone X 这周终于提示电池需要维修了，考虑到这手机真的已经难堪大用而且最近也有国补，所以索性走国补买了一个 iPhone 15 128G。

不过为了凑国补的500优惠，还下载了一下云闪付，不过发现也不麻烦，关联一下现有信用卡就行。

走了国补之后只要4099，还是很优惠的。

不过因为国补所以电商担心你倒卖，是需要限时激活确认的。

走 iPhone 自带的数据克隆转移把数据和应用也很轻松地还原到了新手机，而且各方面都有明显提升，老婆很满意。

\#2

这周报税的时候想走一下房贷减免，好不容易把所有材料填完发现我没有额度了。。。

研究了一下发现是老婆几年前给她名下的房子减税的时候没搞清楚把我们家额度都用完了。。。

不得不讲政府也太抠了，夫妻双方各有一套房的情况还得侵占对方额度

\#3

趁着国补给老婆家买了一个云鲸逍遥001MAX，周六下午就送到了，稍微研究了一下之后就 setup 好了。

周六晚让丈母娘体验了一下。

整体上丈母娘还是很满意的，还把大部分钱都转给我了。（之所以是大部分是因为我也记错了价格，把3600记成了3200）

国补这波还算是给力的，去年十一给老家买的石头P20也要3900多呢。。。

\#4

这周 Hades 把 16 热也通关了，感觉难度确实不是很大。不过32热就不考虑了，太花时间了；现在距离全成就就剩下一个 max all keepsakes 了这个纯花时间刷

所以估计未来一周左右就能全成就了

\#5

本周就周三电影俱乐部一起看了电影

- **我仍在此 Ainda estou aqui** 3.5/5 这拿奖确实有点过了...除了视角从一个家庭女性出发以家庭为侧面叙述那段历史之外，没有太多能让人留下深刻印象的地方。


## Work

\#1

**CppCon 2022 | Purging Undefined Behavior & Intel Assumptions in a Legacy C++ Codebase - Roth Michaels** https://www.youtube.com/watch?v=vEtGtphI3lc&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=95

- 十几年的老代码因为各种原因肯定有UB的，所以只能逐步的解决
- 静态工具+动态 sanitizer + CI，总结下来这几套
- 中间穿插一些作者自己经历的故事到还挺有意思

**CppCon 2022 | Observability Tools C++: Beyond GDB and printf - Tools to Understand the Behavior of Your Program** https://www.youtube.com/watch?v=C9vmS5xV23A&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=97

- perf for cpu profiling，perf 还有一些细致的分析
- heaptrack for memory allocation
- https://github.com/jlfwong/speedscope interactive view
- strace 抓 syscall，但是这部分我觉得实用性一般，有时候 trouble shooting 倒是还可以用

**CppCon 2022 | Lightning Talk: Who is Looking for a C++ Job? - Jens Weller** https://www.youtube.com/watch?v=0xI0H7djNOc&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=98

- 北美 C++ 就业的一些市场数据
- 感觉北美招聘这块是有点问题的，招C++得基本都是大厂了，然后大厂招人又是基本只leetcode算法…

**CppCon 2022 | Sockets - Applying the Unix Readiness Model When Composing Concurrent Operations in C++** https://www.youtube.com/watch?v=YmjZ_052pyY&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=99

- 又是 bloomberg 的分享，在 raw socket 上座一层 epoll 的封装，最后讲了一下如何把这套接入 S&R
- 那套封装我觉得实际上不一定好用

**CppCon 2022 | C++ for Enterprise Applications - Vincent Lextrait**  https://www.youtube.com/watch?v=4jrhF1pwJ_o&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=101

- 感觉是在卖他们家的solution，但是东西看着有点牛逼
- 用语言级别的实现做了一个 SQL 类似 ORM 的东西，但是一些DB的约束直接反映到类型约束上了
- 最后 Bjarne 还给背书了 🤡

**CppNow 2024 | Unit Testing an Expression Template Library in C++20 - Braden Ganetsky** https://www.youtube.com/watch?v=H4KzM-wDiQw&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=44

- 作者写了一个 tok3n 的 compile-time parser combinator 的lib，然后需要能在 compile-time UT 的框架，然后调研了主流框架
- snitch 这个有点意思，compile-time 的 test case failure 可以不 fail compile 转而是在 runtime report
- 另外是 catch2 对 compile-time ut 支持比较好，其他的 lib 就一般般，约等于没支持

**CppNow 2024 | How do Time Travel Debuggers Work? - Design and Implementation of a Time Travel Debugger - Greg Law** https://www.youtube.com/watch?v=NiGzdv84iDE&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=46

- 演示宣传他家（undo）的 time-travel debugger
- 另外我发现 windbg 居然支持的这么全啊。。。

**CppNow 2024 | Glean: C++ Code Indexing at Meta - Michael Park** https://www.youtube.com/watch?v=BAxoeCEfb1I&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=53

- 介绍得 meta 在用的 code indexing search tool
- 后半段很大部分都是在讲 dsl 怎么 tuning 来处理一些鬼畜得 macros，这部分感觉意义不大
- 另外他们不是 from scratch，C++ part 还是利用了 clangd 做了 integration

**C++ Weekly - Ep 472 - C++23's static lambdas?!** https://www.youtube.com/watch?v=M_AUMiSbAwQ

- auto l = [](int i) static { return 42 + i; }; 这种 static lambda 对应使用 static operator() 的 function object
- 优点在于几乎不需要怎么优化力气就可以省掉一个 register 传递 this，因为可以直接 call 对应的函数

**C++ Weekly - Ep 374 - C++23's out_ptr and inout_ptr** https://www.youtube.com/watch?v=DHKoN6ZBrkA

- 两个都是针对 smart pointer，但是后者预期C函数内部会先 release/delete 资源然后 re-initialize
- 所以后者会试图调用内部对象的 `.release()` 所以 shared_ptr 没法用后者

**C++ Weekly - Ep 372 - CPM For Trivially Easy Dependency Management With CMake?** https://www.youtube.com/watch?v=dZMU3iAPhtI

- 介绍CPM的，这玩意儿我还是 contributor 之一呢…
- 小东西（library）考虑到 self-contained 可以用这个，稍微有点规模的项目还是建议用 vcpkg or conan

**C++ Weekly - Special Edition - Getting Started with Embedded Python** https://www.youtube.com/watch?v=MIM_PTv_VjU

- 介绍一下 micro:bit 这家的嵌入式玩具

**C++ Weekly - Ep 371 - Best Practices for Using AI Code Generators (ChatGPT and GitHub Copilot)** https://www.youtube.com/watch?v=I2c969I-KmM

- Jason 谈 chatgpt
- 现阶段这玩意儿可以当作一个 smart rubber duck，或者用来做 quick get started 得东西

**C++ Weekly - SE - Make Your Own Godbolt (Compiler-Explorer) With GPT-4 in 5 Minutes!** https://www.youtube.com/watch?v=XD3b2HA_7BQ

- 这一期有点意思，用 chatgpt 生成一个 compiler explorer
- 不过明显可以看出一些细节需要 fine tune，但是拿AI这部分来快速上手倒是一个不错的选择

**Exploring Undefined Behavior Using Constexpr** [https://shafik.github.io/c++/undefined behavior/2019/05/11/explporing_undefined_behavior_using_constexpr.html](https://shafik.github.io/c++/undefined%20behavior/2019/05/11/explporing_undefined_behavior_using_constexpr.html)

- 观感一般，因为只是用 constexpr 来”探究“一下一些常见 ub 的原因
- 并没有一个 general 的 solution 可以 compile-time analyze 一段代码是否有 ub
- 不过可以权当了解

**0+0 > 0: C++ thread-local storage performance** https://yosefk.com/blog/cxx-thread-local-storage-performance.html

- trivial 的 tls access 只是多一次 fs 寄存器的访问，性能上没问题，但是
- 如果涉及 non-trivial 需要调用构造函数的对象，那么构造函数能否被 inline 会显著影响到效率
- 动态库尽量不要导出 tls 变量符号；如果不用动态库，不要编译为 PIC

**Using the mold linker for fun and 3x-8x link time speedups** https://www.productive-cpp.com/using-the-mold-linker-for-fun-and-3x-8x-link-time-speedups/

- 分享了他们在切换到 mold 中遇到的一些问题
- mold 作者修问题修的很快

**Throwing Exceptions From Coroutines** https://ibob.bg/blog/2024/10/06/coro-throw/

- 纯属开脑洞系列，不是什么正经的 solution
- 纯当作休闲看看就行

\#2

这周终于又抽了点时间重新拾起 httprouter 的源码稍微“正经”学习了一下。

大概看明白无冲突（router segment tree 为空）时候插入的处理逻辑了；构造了几个 case 跟着调试器走了一遍也基本明白 indices 的作用了。

不过因为处理存在插入时需要split现有path那段逻辑相对麻烦一些所以打算后面再看，先用 C++ 把 empty tree 情况写一遍先

---

好了本周就这样，下周见

