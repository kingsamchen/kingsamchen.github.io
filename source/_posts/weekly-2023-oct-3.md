---
title: 一周杂记 in Week 3 Oct 2023
categories: CODE-LIFE
date: 2023-10-23 23:08:04
tags: [杂记]
---
本周（10/16 ~ 10/22）是十月份的第三周。

## Life

\#1

因为之前把小米筋膜枪摔坏了，所以这次打算买个小米手环的时候连带把筋膜枪一起买了。

结果筋膜枪到货的时候发现无法开机，一开始还以为是没电了，充电半天之后还是无法开机...

还好 JD 走换货流程比较省事，只不过后面换货又要一段时间了。

小米手环还是挺好用的，带着在健身房跑个步和踢球都没有太明显的不适感觉。现在主要用来检测心率和睡眠，顺带看一下一天运动能消耗多少热量，虽然感觉都不太准，不过有个大致的范围对我来说就足够了。

\#2

因为上周庆祝老婆 SCI 的那顿饭不是很让人满意，所以这周五和周六分别吃了一顿盒马海鲜大餐和大渔。

不得不说是真的过瘾 😎

不过代价是运动量得 double double 了

\#3

因为约了周日的科一线下学习课和鉴定考试（其实就是科一模拟考，得过了85分才能真正的预约科一考试），所以这一周一直在刷驾校一点通。

周六还做了几乎一天的题目，把500道精华题都做了一遍。

周日线下学习的地点离家还不算太远，地铁40分钟，出地铁站走几步就到了，课程也还可以，其实就是简要的过了一些科一的考点。

模拟考试也很顺利的通过了 😎，不过可能太久没考试了，一个模拟考就给我整的紧张的不行，考试过程中心率直接飙到90多...😅

因为科一无法再周末考，并且最快也必须要在鉴定考试通过的7天后，所以预约了 10.30 的科一考试，到时候请一天假。希望顺利 🥱

\#4

这周看了两部电影

**坠楼死亡的剖析** 4/5 这部是今年的金棕榈展片，和同事一起在公司看的。一开始以为是另一个平行宇宙的消失的爱人，看完发现是另一个平行宇宙的婚姻故事

**行骗高手 Sharper** 3/5 整体比较稳，但是节奏没有做到起伏同步，尤其最后反转的时候居然和前面没有任何变化。

## Work

\#1

本周学习总结

**code::dive | Asynchrony with ASIO and coroutines**

- code::dive 这个 conf 的一个对比在 ASIO 中使用 callback/lambda 和 coroutine 实现网络编程的分享
- 比较基础但是对比也比较直观

**CppCon 2021 | What's New in Visual Studio: 64-bit IDE, C++20, WSL 2, & More - Marian Luparu & Sy Brand**

- 远超预期的 talk，本来以为只是很无聊的蜻蜓点水的讲一下 VS 过去一年的迭代，妹想到 Tartanllama 现场用 VS 写了 coroutine-based xorshift random generator，还演示了和 ranges 连结使用
- 甚至覆盖了 vcpkg 管理依赖和 cmake presets

**凤凰架构 | 6. 分布式共识**

- 简单讲了一下 Paxos, Multi-Paxos 和 Gossip 三个分布式一致性协议

**Building Microservices | 6. Workflow**

- 核心是分布式事务，稍微过了一下 2PC，但是作者并不推荐使用分布式事务
- 相反，作者推荐使用 saga pattern，并且介绍了 orchestrated sagas 和 choreographed sagas

\#2

前段时间学习并总结了一个还算安全靠谱的用户密码创建且传输到 server 保存的流程。

打算亲自实践一下，并且这次用上 vcpkg 作为依赖管理以及 cmake presets 为构建基础

\#3

感觉 anvil 需要作进一步的 improve，目前大致列了这么几个点

- cmake minimum required → 3.21
- move pch files into `$PROJECT/build/pch`
- support vcpkg
- make cpm optional
- preset ?

不会一次做完，应该和其他的东西穿插着做

---

好了这周就这样，下周见
