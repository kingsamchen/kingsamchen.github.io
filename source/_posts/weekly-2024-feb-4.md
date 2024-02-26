---
title: 一周杂记 in Week 4 Feb 2024
categories: CODE-LIFE
date: 2024-02-26 21:16:48
tags: [杂记]
---
本周（02/19 ~ 02/25）是二月份的第四周，结束了春节假期开始回到工作中。

## Life

\#1

这周周一周二请了两天假，周三开始回去上班。

然后发现办公室没几个同事...算上 WFH 的估计还有1/3的人在休假，一直到周五工作群才开始活跃起来 😁

\#2

初三初四的时候爷爷就一直咳嗽，那会儿我爸说带他去医院检查一下他不同意，觉得吃点药就好了。

结果到了这周一的时候我妈发微信说爷爷晚上洗澡的时候晕倒了，还好小姑听见了动静通知了全家人速度送到医院了。

拍了胸片和一些检查结果给媳妇儿看了一下，说是肺部感染导致的呼吸困难。

虽然媳妇儿说这个症状看起来不是很严重，但是考虑到我们那的人民医院的水平实在令人堪忧，以及爷爷今年已经八十多了加上是个多年的老烟枪，肺功能天然要差很多，所以心里不免有点担心。

隔了几天打了个电话又问了一下现在状况，说还在住院，但是情况比之前要好一些。老人家恢复比较慢，估计还得再观察一段时间才能出院

\#3

节前媳妇儿考了雅思，那会儿我觉得虽然媳妇儿复习过程不是很努力，发挥过程中也有点磕碰，但是6.0总归还是可以的吧？

最近两天终于要出成绩了媳妇儿变得特别紧张，每天都让我查好几遍系统...

周三终于出成绩了结果一看只有5.5 🤦‍♂️ 而且听力和阅读居然一个4.5一个5.0，我自己都觉得有点离谱...反倒是写作6.0口语5.5还算符合预期

成绩不达标又能咋办呢，这次赶紧报班重新认真的准备一个月，一个月后再考一次呗...

不然申请医学博士门槛都达不到这路都没法往下走啊 😑

## Work

\#1

本周学习总结

**Design Patterns in Modern C++ | 5. Singleton**

- 这张提到的使用 singleton 会影响 lifetime 以及 testability 我是非常赞同的
- 配合 boost.DI 的用法感觉可以研究一下

**深度学习入门 | 4. 神经网络的学习**

- 讲了损失函数，数值微分，以及基于数值微分的梯度下降学习法

**CppCon 2021 | Back to Basics: Concurrency - Mike Shah**

- 不错的基础介绍性 talk，感觉非常适合给团队新人（new-grad 或者从其它语言转来的）
- thread/jthread/mutex/guard/cv 这些日常用的都覆盖到了

**CppCon 2021 | Implementing static_vector: How Hard Could it Be? - David Stone**

- 这个 talk 主要讲如果要实现一个 static_vector （栈上的，有固定的 capacity）并且这个 static_vector 要尽可能支持 constexpr 的话会遇到的困难
- 中间讨论了很多非常 low-level non-trivial 的细节，比如如何使用 aligin storage，memcpy 性能问题 .etc
- 对于喜欢底层实现的人非常值得一看

**So You Think You Know Git - FOSDEM 2024 https://www.youtube.com/watch?v=aolI_Rz0ZqY&t=2s**

- 一些 git 冷门技巧的分享，干货不少
- 作者是 github co-founder，演讲也非常幽默

**Why `fsync()`: Losing unsynced data on a single node leads to global data loss** https://redpanda.com/blog/why-fsync-is-needed-for-data-safety-in-kafka-or-non-byzantine-protocols

- 核心就是说 apache kafka 默认不用 `fsync()` 存在单个节点挂掉导致整个集群丢数据的问题
- 算是 redpanda 给自家的软广吧

**The story of one latency spike** [https://blog.cloudflare.com/the-story-of-one-latency-spike/](https://blog.cloudflare.com/the-story-of-one-latency-spike/?utm_source=pocket_saves)

- cloudflare 调查客户反馈的某些 CDN 链接会出现长达 30s 的延迟中记录的过程
- 网络延迟：1s, 3s, 7s, 15s, 31s 是非常明显的 SYN 包丢包重传的延时特征，所以他们猜测和丢包有关，通过随后的 ICMP 测试发现存在有 server 实例处理网络包过慢，并且利用 systap 脚本测试发现了一个可疑函数 tcp_collapse
- 随后研究发现是因为 socket recv buffer 设置的过大，导致某些情况下 payload 太小时系统为了节约资源会将相邻的 packets 的数据包进行合并，人为的制造了 GC，而 buffer 太大导致 splice/merge 耗时过于明显，由此导致的延迟升高
- 解决方案是调整 `net.ipv4.tcp_rmem` 为一个合理的值，即避免太久的 tcp collapse 又不会太影响整体网络吞吐量

\#2

这周终于抽了时间把使用 error_code & error_condition 的 blog post 写出来了，花的时间比预期的要多得多...

数了一下番茄记录，差不多花了两个半小时...

相比下写 code 都没这么久。

code 部分在 https://github.com/kingsamchen/Eureka/pull/19/files#diff-f6e04d2b336cb2ecd3497a52b7912609379202b66398927bc28529db7dfb411f

\#3

虽然深度学习的 chapter 4 看完了但是总感觉没怎么吸收进；按照以前的经验最好的方法就是动手把相关的公式、代码都自己做一遍

所以先暂时停下后续章节的学习，先花点时间配合 jupyter notebook 把 chapter 4 的部分好好复习+总结一下

---

好了这周就这样，下周见
