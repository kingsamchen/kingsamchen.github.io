---
title: 一周杂记 in Week 3 Jan 2024
categories: CODE-LIFE
date: 2024-01-22 20:54:10
tags: [杂记]
---
本周（01/15 ~ 01/21）是一月份的第三周，距离农历新年还有三周。

## Life

\#1

因为公历新年前考过了驾照，所以一直说要请一小帮同事吃顿饭，然因为各种客观因素叠加，一直拖到了这周才吃上这顿饭。

和同事定好了周一余阿福牛肉火锅，提前定了一个包厢，下班后众人就去包厢开饭了。

不过这次也有俩同事因为意外因素没来，所以估计全员要等到后面提车之后再请吃饭了。

牛肉火锅比较实在，11人吃了800多全吃饱了

就是羽绒服上的火锅味散了一周了目前还是能闻到一点 😂

\#2

因为周五年会加上CTO莅临参加，所以 HZ 这边要求大家把去年 Q4 的团建经费在这周五前上报完结。

虽然看不出团建经费的报销和CTO参加年会有什么关系但是既然公司层面都要求了那只能照办了，考虑到周四有 CDO allhands，周五有 CTO allhands + 年会，所以自然选在了周三。

吸取了上次日料实在太贵性价比一般的教训，这次我们回归了御牛道烤肉，味道属实不错。而且吃完之后楼下就是火星工厂，大家又去火星工厂转了一下。

和suyang、老蔡、老K四人玩了一个多小时的 cyberpunk 2077，又打了一个小时的 switch 上的分手厨房，差不多5点我们就提前撤了。

可能当天玩的有点累，回家歇了一会儿结果直接睡着了睡到了快七点...

醒来后稍微洗了把脸去健身房练了一会儿，恢复点状态

\#3

周四和周五两次 allhands，看到了 doc project 的正式内部 demo，确实做得很好，未来可期。

反观某T0组，基本💊

尤其CTO都在manager内部会上直接点评了某T0组的几个问题，看起来今年 reorg 板上钉钉了。

不知道HZ这边还能不能苟到我RSU拿完 😥

\#4

本周观影

- **银翼杀手2049** 5/5 维伦纽瓦讲故事的能力实在比老雷当年强太多了；当年的版本直接给我看睡着了...

## Work

\#1

本周学习进度

**HBase原理与实践 | 15. HBase 2.X 核心技术**

- 主要介绍 2.x 做的各种优化方案

**HBase原理与实践 | 16. 高级话题（最后一章）**

- 如何使用局部二级索引（依赖 coprocessor 保证 region 事务）和全局二级索引（依赖分布式跨行事务）
- 小米 HBase 团队基于 percolator 协议实现的跨行事务
- 其他的一些杂项

**Building Microservices | 15. Organizational Structures**

- 组织架构应当匹配其对应的工程架构，因为组织架构会影响工程架构，即康威定律

**CppCon 2021 | Your New Mental Model of constexpr - Jason Turner**

- 前半段简单介绍 constexpr 的演进，后半段展示万物皆可 constexpr 化，直接拿一个 ascii → pesci 的例子来做到逐步 constexpr 化
- 这个 talk trick/idioms 方面的东西比较少，主要展示的还是思维模式这块的，即格局打开，格局要大

**CppCon 2021 | Law of Demeter: A Practical Guide to Loose Coupling - Kris Jusiak**

- loose coupling → easy to change code
- Consider classes to have only one reason to change
- law of demeter: consider talking only to your immediate friends
- Better use constructor for dependency injection
    - be wary of carrying dependencies, which often incurs leaky abstraction

    ⇒ consider passing initialized objects instead of parameters to initialize them

    ```cpp
    // better than
    explicit speaker::speaker(std::string name) : name{name} {}
    explicit cppcon_talk::cppcon_talk(speaker speaker) : speaker{speaker} {}

    // leaky abstraction
    explicit cppcon_talk::cppcon_talk(std::string name) : speaker{name} {}
    ```

- DIP(Dependency Inversion Principle): Depends on abstractions, not on implementations

**CppCon 2021 | Understanding and Mastering C++'s Complexity - Amir Kirsh**

- 讲了好些 C++ 里常见的坑，（演讲者认为）这些算是导致 C++ 复杂度的根源
- 本质上就是一些规则都是 implicit 的，并且在不同上下文下适用的规则还不一样
- 另外我虽然知道 `vector<bool>` 的迭代器本质上是个 proxy，但是还真没想过修改式 range-based for 时可以用 `auto&&`

**How NAT traversal works https://tailscale.com/blog/how-nat-traversal-works/**

- TL;DR
- 从防火墙配置和NAT类型导致无法直接进行双向通信开始讲，逐步展开到各种NAT类型和隧道打洞
- 算是 tailscale 家产品的硬广

\#2

这周遇到一个坑，某段代码逻辑是给 `file` 传递一批文件路径得到对应的 mime-types，结果某个测试文件的路径包含了空格，导致 `file` 认为是两个文件，导致自动化测试挂了。

直观上感觉得用 `std::quoted` 但是又不太确定，所以为了快速修复就直接先手动做了 quote

事后认真看了 cppreference，又试了几个demo，确信这种情况应该用 `std::quoted(str)` 包一下

`std::quoted()` 返回的是可用于 stream 的操作对象，所以如果用 fmt 得

```cpp
std::cout << fmt::format("quoted={}", fmt::streamed(std::quoted("this is a test text")));
```

至于为什么不用 fmtlib 的 `{:?}` 是因为它的作用不仅限于加引号，而是会将一些特定的类型按照特定的格式输出，毕竟这个格式叫 debug mode；做的太多了以至于不够纯粹。

另外，如果你拿到的是一个 `std::filesystem::path` 则它的 `operator<<` 操作会自动在内部调用 `std::quoted`

\#3

这周开始学习 absl status 的源码

最近有个想法想在 status 基础上做一个 wrapper 带上 source location，所以刚好顺带先看看 status 的实现代码

---

好了这周就这样，下周见
