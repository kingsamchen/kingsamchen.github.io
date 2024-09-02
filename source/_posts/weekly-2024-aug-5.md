---
title: 一周杂记 in Week 5 Aug 2024
categories: CODE-LIFE
date: 2024-09-02 23:08:27
tags: [杂记]
---
本周（08/26 ~ 09/01）是8月份的第五周，也是最后一周

## Life

\#1

组里的一个同事 Roro 这周终于要 transfer 去 San Jose 了，我们给他搞了一个比较盛大的欢送会。

私下几个比较熟的同事一起吃了一顿饭，并且周四我请了组里的同事一起吃川遇，给 Roro 送行。

考虑到目前组里的情况，transfer 去湾区有点当战地记者的意思了，刚好可以给我们传递一下美国那边的第一手信息，看看“倒张运动”有没有可能进行 😅

另外一个点是让 Roro 试探性地看看有没有可能在美帝留下来，如果机会不错的话，后面我也可以考虑带媳妇儿一起转去美国，当然前提是这身份问题能解决

\#2

本来定了这周五晚上和媳妇儿还有丈母娘一起看《异形》，但是因为丈母娘周末几天都有乒乓球赛，所以没有时间。

看了一下日历，和媳妇儿一通商量之后就改到下周周末了，反正时间有的是。

周日是9/1，媳妇儿又要去富阳那边支援顺带度假了。

周日一大早（ 7:30 am）就开车送她到了富阳那边的学校，这次因为说是军区有领导下来视察，所以媳妇儿和几个护士被安排到招待所待命，每天给领导们量量血压，做一些基础检查。其他时候就闲的基本自由时间，不过考虑到不能离校而且附近偏僻程度，基本也和坐牢没啥区别了...

不过招待所的居住环境不错，几个单人间，客厅还有电视，看装修差不多有点三星级酒店的样子了 🤣

\#3

本周观影

- **边境杀手 4/5** 剧情主线简单，但是叙事基本功扎实流畅，镜头语言很棒。女主本质上是一个观察者视角，代表用法律合法解决毒贩的理想主义，但是对于本身就在法律约束之外的势力它们对法律天然免疫却害怕另外一股秩序力量

## Work

\#1

**CppCon 2022 | How to Use C++ Dependency Injection to Write Maintainable Software - Francesco Zoffoli** https://www.youtube.com/watch?v=l6Y9PqyK1Mc&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=23

- 不是寻常那种介绍 DI 框架的分享，而是从工程设计角度出发，引入依赖时如何避免不正确的依赖
- 提到的 piping 的概念有点意思

**CppCon 2022 | C++23 - What's In It For You? - Marc Gregoire** https://www.youtube.com/watch?v=b0NkuoUkv0M&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=25

- yet another introduction talk to C++ 23 features

**CppCon 2022 | import CMake, CMake and C++20 Modules - Bill Hoffman** https://www.youtube.com/watch?v=5X803cXe02Y&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=26

- 讲了很多 CMake 最近的改进和新 features
- 重头戏是 CMake （在2022年）对 modules 的支持状况

**C++ Weekly - Ep 273 - C++23's Lambda Simplification (With Commodore 64 Example)** https://www.youtube.com/watch?v=fPWWo0MVK34

- 其实可以压缩一下，因为讲的就是 lambda 简化形式，更多场景下可以省略掉无参数括号

**C++ Weekly - Ep 274 - Why Is My Pair 310x Faster Than `std::pair`?** https://www.youtube.com/watch?v=3LsRYnRDSRA

- universal reference 的构造函数性能其实不高，而且生成的代码不容易被 inline
- 一个类似的情况是之前有个 post 探讨过 vector 的 push_back 和 emplace_back，绝大多数情况下 push_back 的性能要更好

**C++ Weekly - Ep 275 - Trust Your Standard Library in 3 Simple Steps** https://www.youtube.com/watch?v=atAd8gzaM1g

- 大体上就是标准库还是非常值得信赖的

**Different ways to achieve SFINAE** https://www.sandordargo.com/blog/2021/06/02/different-ways-to-achieve-SFINAE

- 简单介绍了一下能实现/运用 SFINAE 的方式和基本场景

**Using C++20's concepts as a CRTP alternative: a viable replacement?** https://joelfilho.com/blog/2021/emulating_crtp_with_cpp_concepts/

- 某些功能场景上 concepts 可以替代 CRTP 但是不能完全替代，涉及到成员函数使用和 ADL 的时候 CRTP 更加自然
- 另外我觉得这俩本来就不在一个维度的东西啊…
- 不过 deducing this 到可以让 CRTP 变得很简单…

**Creating other types of synchronization objects that can be used with co_await, part 4: The manual-reset event** https://devblogs.microsoft.com/oldnewthing/20210312-00/?p=104955

- 系列第24篇
- 这篇主要是给 awaitable event 增加一个 initial state 和 reset 的支持

**Creating other types of synchronization objects that can be used with co_await, part 5: The auto-reset event** https://devblogs.microsoft.com/oldnewthing/20210315-00/?p=104964

- 系列第25篇
- 实现 auto-reset event，引入的 calc_claim 是核心

**Creating other types of synchronization objects that can be used with co_await, part 6: The semaphore** https://devblogs.microsoft.com/oldnewthing/20210316-00/?p=104971

- 系列第26篇
- 加上 semaphore 支持；semaphore 本质和 auto-reset event 基本一致

**Creating other types of synchronization objects that can be used with co_await, part 7: The mutex and recursive** https://devblogs.microsoft.com/oldnewthing/20210317-00/?p=104973

- 系列第27篇
- 这篇开始实现 awaitable mutext，和 auto-reset event 实现类似
- 另外因为 standard mutex 有 thread ownership 的概念，而 coroutine 不太需要这个，所以 mutex 才和 auto-reset event 没啥区别，recursive mutex 也基本没啥意义

**Creating other types of synchronization objects that can be used with co_await, part 8: The shared mutex** https://devblogs.microsoft.com/oldnewthing/20210318-00/?p=104977

- 系列第28篇
- 实现 shared mutex，核心是用成员的 -1, 0, > 0 (shared count) 来表示三种状态

\#2

之前说要把 nlohmann-json 的 serde 给 formalize，放到一个专门的 repo，并且调整一下实现 serialization/deserialization options 的策略，这周终于开始搞这个事儿了

因为不带 options 的部分直接用之前的实现就行，所以这部分改改宏的名字就好了，比较麻烦的是在思考用啥更好的方式来实现 options

仓库目前是 private 状态，等到全都写完了再 make public 🤣

---

好了这周就这样，下周见
