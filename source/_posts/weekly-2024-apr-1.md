---
title: 一周杂记 in Week 1 Apr 2024
categories: CODE-LIFE
date: 2024-04-09 00:46:13
tags: [杂记]
---
本周（04/01 ~ 04/07）是四月份第一周。四月份的日期和星期特别规整 🤣

## Life

\#1

上周周末交了尾款之后就等着提车了，结果没想到周二晚上交付告诉我因为他们公司发票流转问题导致拿不到补贴了。但是他们考虑到我是“优质”用户（韭菜）所以决定换成等额的商城积分给我。

这让我直接就不能忍了，虽然我不差这4000补贴，但是当初买车的时候可是销售主动 promise 一定会有补贴，他们哪里的发票都能开，云云

所以这一出直接让我感觉被耍猴了

和交付沟通了一天多，对面都说只能按照流程走，于是我很生气的撩一下一句实在不行就退车吧。

过了半天交付说可以约时间讨论一下，于是我约了清明第一天假期，拉上了同事 Hawk，也是老款001车主，同时兼任老乡+初/高中学弟 🤣

本来想着协商沟通好，顺带提车，于是也拉上了媳妇儿，清明第一天跑到交付中心。

在 Hawk 大力扮黑脸的火力支援下，对方同意额外在赠送5000积分，那既然如此我也顺给对面一个台阶下。

不过后续验车的时候发现前脸有两处很细小的划痕，如果不是黑色车身的话估计都看不出来；又是一番拉扯之后对面同意在赠送 5000 积分补偿。

这部分谈妥之后连同保险也一起定了，最后裸车+保险落地差不多31W不到

但是因为清明第一天车管所不上班，所以临牌开不出来，约了两天后周六提车。

转眼到了周六，又麻烦了 Hawk 一起去交付中心，办完剩下的手续后就把车往家开。

到小区入地库时候因为车太宽，入口右转实际没选好，差一点点蹭到右侧花坛石阶，还好 Hawk 盯着360及时给我叫停了...

重新调整后成功进入地库，并且溜了一圈之后也找到了车位，随后就是在 Hawk 指导下完成了倒车，最后看还挺完美的

这次提车麻烦了 Hawk 不少时间，虽然请他吃了两顿饭，但是后面打算多点折扣卖他一些积分；后面装了家充我应该只需要保留一点点积分留给以后外出充电就行了

\#2

提车前一天周五晚上和媳妇儿吃了大渔，但是因为是节假日人太多，等了太久，所以开始吃的时候已经是晚上8点多了

更糟糕的是因为吃的有点多，所以半夜肚子消化不良一直没睡着，中间两次起来一次喝水一次吃褪黑素都没啥用。

最后忍不了凌晨三点起来吃了一粒吗丁啉，最后才慢慢睡着。

然后更惨的是媳妇儿虽然在另一个卧室睡觉，但是因为动静有点大把她也吵得没睡好，导致周六她压根不想和我一起提车。。

周六提完车之后吃过中饭，给媳妇儿带了点中饭和粘钩就回家了，在沙发上看了一集 Ahsoka 就困得不行，直接回卧室从一点半睡到了5点...

\#3

本周观影

- **Ahsoka S1** 才刚开始看，但是不太喜欢 Sabin Wren 的选角
- **周处除三害 4/5** “尊者，我真的想洗心革面重新做人你咋就不给我这个机会呢” 中上水准还是有的，剧本要是再打磨一下就更好了。没想到阮经天的表现可以做到这样。
- **科洛弗悖论 The Cloverfield Paradox** 3.5/5 国际章的戏份都有点掉节奏，好出戏

## Work

\#1

本周学习：

**Design Patterns in Modern C++ | 20. Observer**

- 讲了传统的基于接口的 observer 实现和基于 boost.signal2 的实现

**Design Patterns in Modern C++ | 21. State**

- 粗浅的介绍了一下状态机模式，勉强算亮点的是草草介绍了一下 boost.mpm 这个 meta state machine lib
- 之前 cppcon 推荐过一个 boost-ext/sml 现在估计可以看看这个

**Design Patterns in Modern C++ | 22. Strategy**

- 传统OO基于接口的 dynamic strategy 和基于模板的 static strategy

**Design Patterns in Modern C++ | 23. Template Method**

- C++里用高阶函数来实现 template method，所以这章内容比较短，就几页

**深度学习入门 | 5. 误差反向传播法**

- 核心是反向传播来计算导数从而用更有效率的方式计算梯度
- 但是后半部分涉及矩阵的反向传播公式突然就冒出来了搞得我莫名其妙的

**CppCon 2021 | Static Analysis and Program Safety in C++: Making it Real - Sunny Chatterjee**

- 介绍 MSVC 的 static analysis 及其在 Github 上的集成

**CppCon 2021 | Making the Most Out of Your Compiler - Danila Kutenin**

- 讲了很多编译器优化的干货，从SIMD到优化参数到LTO
- 总体内容比较细碎，如果正好在做优化强烈推荐

**CppCon 2021 | Deploying the Networking TS - Robert Leahy**

- 感觉粗细不太合适，有点过于 detailed 但是又不知道具体是要做什么，lecturing 上不如之前看的一个讲ASIO 配合 coroutine 的 talk
- 另外一点是现在这个 networking ts 估计又要撕扯一段时间

**Understanding Consensus** https://bravenewgeek.com/understanding-consensus/

- 讲了一下 2PC 3PC 然后 paxos raft zab 这种基于 replicated state machine 的协议
- 感觉讲的也不是很深入

**Linear Algebra OCW | 1. The Geometry of Linear Equations** https://ocw.mit.edu/courses/18-06sc-linear-algebra-fall-2011/pages/ax-b-and-the-four-subspaces/the-geometry-of-linear-equations/

- 第一节课，主要讲线形方程的几何意义，从 row based picture 到 column based picture，以及介绍 linear combinations
- 然后对应的书好像有好几个小章节，我书才看完 1.1…
- PS：习题课的小姐姐是个中国人，是我喜欢的 type 🤣

\#2

随同深度学习开了线性代数这个坑，而众所周知数学相关的课程都需要多练多感悟，所以后续估计会有不少的时间花在线性代数的习题练习上。

更不用说书本 + 公开课正课视频 + 习题课视频 加起来也够一壶了，还好我现在算是有大量的时间可以花在上面。

\#3

这周终于重新开始继续之前的 absl/status 源码学习之旅，下周会继续，💪

---

好了这周就这样，下周见
