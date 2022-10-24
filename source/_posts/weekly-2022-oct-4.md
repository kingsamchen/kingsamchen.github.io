---
title: 一周杂记 in Week 4 Oct 2022
categories: CODE-LIFE
date: 2022-10-23 22:48:38
tags: [杂记]
---
本周是十月第四周。

## Life

\#1

这周帮14F的邻居养了他老婆的朋友的加菲猫3天，终于在周三早上老婆回来前把猫交还回去了。

这一段真的是狗血...

而且事实证明我对品种猫真的无感，除了这加菲猫让我想起了当年的阿花...

不过老婆起的“阿周”这名字倒挺适合猫的

\#2

周三和同事看了 Top Gun 2: Maverick，片源还是我提供的

是话说我还蛮喜欢这部主旋律的，人家确实也拍得好不是。算上情怀和家庭分，我能给到 8/10

周日因为众所周知的事情，看了 Ambulance，结果心情更糟糕了...

卖拷贝实在太令我失望了...我从头看到尾心里都是一阵莫名奇妙，5.5/10 不能再多

\#3

这周六和周日双双见证了历史。

这个周末可能是一个时代的结束，也是另一个时代的开端。

自由已死

## Work

\#1

这周花了一些时间学习 absl 的 Mutex，但是这部分代码逻辑实在太杂揉了。

因为 absl 团队约等于自己微操的基础上实现了 reader-writer lock，又把 condition variable 直接揉进去了，导致我想看最基础的 mutex lock/unlock 逻辑都会有一堆的干扰

虽然最后基础的逻辑看完了，但是并没有 eureka 的感觉，反而只是觉得乱。

另外一个问题是，我压根没用过 absl mutex，所以很多内部逻辑完全对应不上它的功能。

所以想了一下不浪费时间，先不继续学习其余部分，ROI 太低了，不如多研究一下平时用的比较多的模块。

\#2

本周学习进度

- Software Engineering at Google | 13. Test Doubles 看完
  这部分内容不错，起码说明我个人不喜欢 Mock 反而是一个正确的方向
- 大规模分布式存储系统 | Chapter 6 分布式表格系统
  Google Megastore, Windows Azure store 的架构；只能说谷三篇确实是标志性的论文
- 网络编程实战 这个课程确实给我带来了一些意外的收获
  反正每天只占用十几分钟，也不算浪费
- CppCon 2020 | Just-in-Time Compilation - JF Bastien
  丝毫 get 不到点，而且基本是长篇幅的做 introduction…看其他评论好像说这个 talk 是半历史介绍性质的
- [Branch predictor: How many "if"s are too many? Including x86 and M1 benchmarks!](https://blog.cloudflare.com/branch-predictor/?utm_source=pocket_mylist)
  Cloudflare 出品的微操优化文章，讲多少个分支会对分支预测有影响

\#3

因为上周发现了公司项目用的一个 smtp client 的 RFC 实现问题，所以打算给作者提一个 issue/PR

在这个之前需要先在本地搭一个 postfix 环境。因为不想直接装在系统里，所以是期望通过容器运行

不过因为我只是希望支持 local delivery，而市面上的 docker image 大多都是冲着 relay server 去的，所以花了不少时间都没找到合适的...

希望下周能顺利找到一个可用镜像

---

好了本周就这样，下周见
