---
title: 一周杂记 in Week 1 Dec 2023
categories: CODE-LIFE
date: 2023-12-11 23:20:54
tags: [杂记]
---
本周（12/04 ~ 12/10）是12月第一周

## Life

\#1

上周周日甲流确诊重度症状，虽然吃了神药之后第二天就退烧了，但是过去一周基本还是处于恢复期，时不时还会出现有痰咳嗽的情况

回想起来去年得新冠的时候虽然第二三天就恢复正常了，但是也是持续了好久的咳嗽，所以这次估计也要花不少时间来恢复。

周六跑了一次跑步机有氧 5km，10.1kmh 的配速没法一次性坚持下来，在 24mins 得时候停下来歇了一会儿，感觉属于恢复期的正常表现？肺部应该也在慢慢恢复中

\#2

这周公司没有观影俱乐部，也没有额外看电影，就看了日剧 **重启人生 ブラッシュアップライフ** 3/5 没觉得比起同类型的影片值得高出平均2分的质量，而且引入多人重启设定之后你也没说怎么解决脑裂问题啊

怎么说呢，我看电影/电视剧可以接受一些天马行空的设定，比如超英电影、各种怪兽电影，但是起码你的设定要能够自洽。作为一个自认 outstanding engineer 我观影还是非常在意这一点的

\#3

这周没练车，因为就剩下最后一节课了，估计要确定了考试时间才会安排练习

## Work

\#1

本周学习进度

**CppCon 2021 | Why does std::format do that? - Charlie Barto**

- 分享了 MSVC team 实现 std::format 的一些细节
- 中间提到了不少基于 msvc 用户所做的 tradeoff，比如 msvc 的实现用了不少 type erasure 的法子来增加一些实现的独立性

**CppCon 2021 | Asserting Your Way to Faster Programs - Parsa Amini**

- 这个 talk 感觉有点仓促，很多地方都没有展开
- 从 contracts proposal 到目前业界的 assert 使用，感觉听起来不知道重点在哪，一些点讲到了就立马切走了

**CppCon 2021 | C++20 on Xilinx FPGA with SYCL for Vitis - Ronan Keryell**

- 完全看不懂…

**C++ Weekly - Ep 258 - The Awesome Power of C++20's std::source_location**

- 用几个示例深入浅出 source_location

**HBase原理与实践 | 6. HBase 读写流程**

- hbase 的读和写的流程，写的比较详细
- 有兴趣的可以看仔细点，反正我对这块兴趣不大…

**Building Microservices | 11. Security**

- 分布式服务中的安全议题
- 着重讲了 Authentication 和 Authorization，还带了一嘴 JWT

**Why Uber Engineering Switched from Postgres to MySQL https://www.uber.com/en-SG/blog/postgres-to-mysql-migration/**

- 数据结构是 Immutable
- PG 直接用 WAL 数据作为 replication stream，因此数据包含非常 low level 的 on-disk bytes 语义，i.e. physical level 而不是紧凑的 logical change
- 对于 uber 的场景导致如下几个问题：
    - 写变更会有明显的放大效应；如果表有很多二级索引，放大效应会更明显
    - 遇到了一个会导致同一个主键索引记录的不同 ctid 都被认为 active 的bug，导致 data corruption 并且影响了很多应用；而且因为 replication 机制，这种问题会被放大
    - WAL replication 可能会被 even read transaction block，导致明显的 lag；并且因为lag超过一段时间 PG 会强行 kill transaction
    - 同样因为 WAL replcation 工作在 Physical level，跨版本的 replication 也不可能；平滑 PG 版本升级太困难

**The Part of PostgreSQL We Hate the Most https://ottertune.com/blog/the-part-of-postgresql-we-hate-the-most/**

- 深入介绍和分析了 PG 的 MVCC 实现及其带来的一些实际问题
- PG MVCC 实现带来的几大问题
    1. version copying 会带来大量无用的数据复制
    2. table 会存在大量 dead tuples
    3. 次级索引存在更新的写放大，并且数量越多产生的 overhead 越大
    4. vacuum 管理想做好不容易

\#2

这周花了不少功夫，终于把基于 asio/corotuine 的几个练手东西写好了

期间踩了好多坑，还好目前为止算是比较顺利，也学到了不少的东西

PR 可以看这里 https://github.com/kingsamchen/Eureka/pull/18

后面应该会是专门花点时间研究学习一下 CXX coroutine 的一些原理性的东西

另外我觉得 ASIO/coroutine 目前完成度还是不太高，虽然作者 Chris 确实很天才把各种抽象设计的很灵活，但是面对一些复杂场景总感觉用起来不如 golang 那么符合直觉。

asio/experimental 里的那堆东西得看看后续的发展

另外因为 executor 提案的问题，S&R 会成为未来的标准 executor，导致 Chris 退出 networking proposal，后续对这部分的实现估计不太明朗...

---

好了这周就这样，下周见
