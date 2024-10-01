---
title: 一周杂记 in Week 4 Sep 2024
categories: CODE-LIFE
date: 2024-09-30 23:07:05
tags: [杂记]
---
本周（09/23 ~ 09/29）是9月份第四周，也是9月份最后一周。下周二就是 10.1

## Life

\#1

上周日在富阳媳妇儿值班房和媳妇儿一起挤着一米宽不到的小床将就了一晚，第二天一早就一起开车回家了。

然后有点蛋疼的是在富阳国道的时候因为变道可能是蹭到泥头车还是大货车，右后车门那块有一点划痕，不过还好面积不是很大也不是太明显，只能先将就了 😅

感觉Q3有点蛋疼啊，已经连续刮了两次了，以后开车得更加注意了 😂

另外虽然周一上午有点堵，但是我们8:30就返程了，硬是在 10:00 daily sync 前到家了，笑死

\#2

周三周四周五休了三天假，和媳妇儿还有丈母娘一起跑到千岛湖度假了。

为了游玩方便，选的千岛湖希尔顿，不过媳妇儿不愿意再多花点钱升级湖景房，所以有点蛋疼的山景房担心虫子从阳台飞进来所以一直不敢开落地窗。

酒店整体居住还是舒服的，而且提供的自助早餐确实味道不错，就是中餐有点拉垮了，而且第一天傍晚在附近（景区）选了一家大众点评好评NO.1的店吃鱼头简直给我们三都无语到了，味道非常一般，而且鱼头肉非常柴，问题是价格还不便宜。。尼玛真坑

第二天一早坐渡轮去了黄山尖俯瞰整个千岛湖，这部分景象说实在是不错的，不过看一会儿也就腻了，而且周二天气一般，远眺始终有种雾蒙蒙的感觉。

倒是希尔顿的水上乐园是挺刺激的，就是不便宜，三个人加起来两个付费项目四五百了，还是在一个项目提供了优惠方案的情况下。免费项目一个都不好玩，而且这些项目的水估计都没怎么换，感觉挺脏的

酒店室外游泳池还是不错的，虽然下午下了一会儿雨，但是泳池出奇的干净。不过我一个旱鸭子花了半小时也没学会游泳，只能套着游泳圈晃悠

晚上开车看了啤酒小镇，就一个几十米的小街，而且博物馆尼玛还要七八十，还好没去；又转到骑龙巷，吃了点小吃，味道算是比较正常。

第三天吃完早餐休息了一会儿，十点多就退房开车回家了。

不得不说，杭州到千岛湖的高速体验还是不错的，基本两个小时内能到，不一直追求最左快车道的话，我的电动纳智捷舒适模式差不多30度电不到。

周五回到媳妇儿家稍作休息之后就“偷偷”带着媳妇儿去附近的省妇保挂了个号做个检查

然后医生说还太早了，下个月再来吧，然后开了一些叶酸...

所以先低调保持吧 😁

\#3

周五回到自己家的时候发生了一个插曲，我把车停错车位了，419被我看成了416停上去了...

傍晚物业管家联系我的时候我还一愣，下到地库发现确实停错了。因为419的人比较急，所以先让他停我自己车位了，后面找时间换回来...

停错位置那会儿我和我媳妇儿居然都没任何感觉有哪里不对...Orz

\#4

周日调休需要上班，去了办公室，组里有大半的或提前休假或在家办公，整个办公室都没几个人...

\#5

这周周四晚上在酒店和媳妇儿重新看了一遍《夏洛特烦恼》

周末在家看了 **异形2** 5/5 这一部明显带有更多的卡梅隆色彩，叙事上也更紧凑，但是幽闭恐惧的色彩比起老雷的处理就少了一些。有些地方甚至有点给我在看终结者2的感觉


## Work

\#1

**C++ Templates 2nd | 14. Instantiation**

- on-demand instantiation，不需要知道实例大小或者不需要访问某个成员时甚至不触发实例化
- laziness instantiation & partial instantiation，编译器的优化，如果发现可以用不到的成员不触发 substitute；注意，class template 这种 by default 都是 full instantiation.
- POI (Point of Instantiation) 的挑选机制，以及为什么同一段设计模板实例化的代码用自定义类型可以编译但是用 int 这种内建 trivial 就不行（因为没有 associated namespace 供 ADL 执行）
- 模板采用 inclusion model，所以为了保证 ODR 要考虑 instantiation duplicated 的问题，三种现在在用的或者曾经用过的方法 1) greedy instantation & discard others 2) queried instantiation 3) 比较浪费时间的 iterated instantation
- 利用各种手法，不管是 manual instantiation 还是 explicit declaration 来优化模板的编译。但是我总觉得这快太微操了，除非场景特定，否则收益可能没有成本来的大

**CppCon 2022 | Cute C++ Tricks, Part 2.5 of N - Code You Should Learn From & Never Write - Daisy Hollman** https://www.youtube.com/watch?v=gOdcNko2xc8&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=40

令人意想不到的 cute tricks 集锦下篇又来了

- 将 concept 运用到 template partial specialization 配合 explicit lambda template 来模拟 partial specialized concept
- 利用 partial specialization 的 specialized 级别来做 overload…
- 还有一些利用 concept 实现的类型 quiz，感觉内容有点类型体操的意思了
- 不过不得不讲 concept 的表达能力比起以前 SFINAE 那套真的是强了几个维度

**CppCon 2022 | Back to Basics: C++ Testing - Amir Kirsh** https://www.youtube.com/watch?v=SAM4rWaIvUQ&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=42

- GTest & GMock 新手入门…?

**CppNow 2024 | C++ Coroutines at Scale - Implementation Choices at Google - Aaron Jacobs** https://www.youtube.com/watch?v=k-A12dpMYHo&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=12

- Googe 内部对于 standard coroutine 的实践，作者是内部库 k3 的作者
- 涉及的变量声明周期问题，利用 k3::Future 来管理可能会出现问题的情况，k3::Future 内部会做 shallow copy
- 为了保证 bounded stack usage，k3 的理念是避免 coroutine run on top of any other coroutine，即每个 thread of context 只有一个 active coroutine
- k3 的 cancellation 设计取舍：callee exists to serve the caller，这个是因为 google 后端服务绝大多数都是 RPC 场景

**CppNow 2024 | C++ Reflection - Back on Track - David Olsen** https://www.youtube.com/watch?v=nBUgjFPkoto&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=9

- 介绍目前支持声比较大的 compile-time reflection proposal
- 目前有 EDG/clang/NV 三家编译器对这个 proposal 做了实现支持，godbolt 上可以体验

**C++ Weekly - Ep 284 - C++20's Safe Integer Comparisons** https://www.youtube.com/watch?v=iNeHHczBTIs

- 介绍的 https://en.cppreference.com/w/cpp/utility/intcmp 这个
- 以后涉及 unsigned/signed 的整数比较直接交给标准库拉

**How to Parallelise CSV Reader - C++17 in Practice** https://www.cppstories.com/2021/csvreader-cpp17/

- 利用标准库的并行算法来提升吞吐
- 不过我觉得在没有确定 executor 的情况下用这个有点蛋疼，鬼知道 backend 的实现是咋样的

\#2

这周抽了点时间研究了一个简单的 coroutine-based thread-pool 的实现，并且自己也跟着理解重写/抄了一遍代码

fire-and-forget 的支持的已经写完了，带 continuation 的部分估计下周十一假期刚好可以过一遍

这次趁着这个仿写以及调试，期望可以对 coroutine 机制有一些更直观的理解

---

好了这周就这样，下周十一假期见
