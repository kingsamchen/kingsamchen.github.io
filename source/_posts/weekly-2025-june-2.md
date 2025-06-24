---
title: 一周杂记 in Week 2 June 2025
categories: CODE-LIFE
date: 2025-06-16 23:36:32
tags: [杂记]
---
本周（06/09 ~ 06/16）是六月份第二周，陪产假假期的最后一周；因为周一是陪产假最后一天，所以直接算进一周了

## Life

\#1

这周也是间隔去月子中心陪媳妇儿和孩子，每天都见证娃一天天长大，老父亲实在有点感动想哭。

而且我觉得我家这娃有点非同寻常，对周围环境的感知力要明显强很多，能认识自己的爸爸妈妈，也能迅速感知自己是不是存在风险，一有风险就哇哇大叫 😂

这周娃又游了一次泳，因为稍微有一些经验了，所以这次非常顺利，表情也非常自然放松，在媳妇儿的引导下还能自如地划水转身，很难想象这是一个只有二十来天的宝宝。

不过这周也出了一个意外：月子中心的阿姨之前觉得她蛮好的，照顾宝宝也专业，但是周日半夜不知道是不是太累了没有耐心，奶瓶喂奶的时候宝宝不愿意喝，她就比较粗鲁的硬往里塞，也没有言语沟通让宝宝安心，导致娃连续两次在母乳之后没有吃到足够的奶粉冲泡的奶，而且因为感到害怕大哭。

而且之前媳妇儿说过宝宝没睡醒就不要强行叫醒她喂奶，但是阿姨没有理会，依然在半夜间隔2-3小时叫醒宝宝喂奶。

媳妇儿说看到宝宝害怕大哭自己一夜没睡，心里非常难过，叠加自己的意见阿姨不听，于是第二天一早我还没睡醒就找我说她要换阿姨。

我说不管之前怎么样，既然对秋羽不上心，那就要换。而且我自己也是那种没睡够被强行叫醒会发很大火气的人。

反映了之后周一下午新阿姨就到位了，媳妇儿和阿姨做了沟通，确保双方对于娃的一些照顾意见达成一致。

下周是媳妇儿和宝宝在月子中心最后一周了，周六有满月 party，希望别再出什么乱子 😂

\#2

这周大部分时间都在下雨，梅雨天气真的太难受了...

好像下周转晴之后会因为台风/热带风暴的影响继续会有几天雨，有点无语啊...

另外就是不知道今年梅雨季啥时候能过去

\#3

周三晚上蹭了一下公司的观影活动，去西溪博纳看了碟中谍。因为 iker 有事，所以我承担了领票和发票以及报销的事情。

在家自己抽时间看完了逆行人生和 the day of the jackal s01

- **碟中谍8** 4/5 开头节奏稍微有点慢了感觉一些意义不大的镜头有点多。另外一个是AI BOSS大部分停留在噱头上，本质上还是传统谍战片套路。不过深海潜水那部分戏的新鲜感还是不错的，考虑到系列结尾，加一点分
- **逆行人生** 4/5 绝对没有舆论说的那么糟糕，相反徐峥的鸡贼让电影各方面都至少中上水准。拿海报说事得自己去看看药神的海报，智力基本属于被人带着跑的。这片最大的问题除了徐峥鸡贼只愿意停留在舒适区外，剩下就是核心目标观众其实是中产，但是中国没这么多中产，加上表现手法的舆论引导问题，大多数人的注意力都跑到送外卖了
- **The Day of the Jackal S01** 4/5 经济真是不景气连接两单都没给尾款，前脚刚帮别人干完活后脚就要被清算，hit-man的辛酸。duggan和bianca本质都是那种为目的不择手段的人，duggan估计真有反社会人格冷血到丝毫不在意collateral damage 小雀斑把这部分拿捏的真不错。可怜我帅hunter好不容易有新剧为了救黑又硬女主领便当了

## Work

\#1

**Boost.Asio C++ Network Programming Cookbook | 1. The Basics**

**Boost.Asio C++ Network Programming Cookbook | 2. I/O Operations**

**Boost.Asio C++ Network Programming Cookbook | 3. Implementing Client Applications**

**Boost.Asio C++ Network Programming Cookbook | 4. Implementing Server Applications**

**Boost.Asio C++ Network Programming Cookbook | 5. HTTP and SSL/TLS**

**Boost.Asio C++ Network Programming Cookbook | 6. Other Topics**

- 花了几天翻完了
- 这本还是很适合之前没接触过 asio 人入手，废话少，一些基础点覆盖的不错，哪怕我之前有一些经验也能学到不少。缺点大致两个，第一个是内容还是略浅一些，io_service/context 的模型没有讲太多，而且多线程的做法采用的是更适合 Windows/IOCP 的 multiple threads running one io_service。第二个缺点是有些 sample 写的不是那么好，至少用法上不如 asio 自己的 examples。asio 这套一个比较大的问题是如果没有良好的周边配套，例如 coroutine/fiber，不按照一个推荐的 pattern/idiom 去运用很容易跑偏

**CppCon 2022 | Take Advantage of All the MIPS - SYCL & C++ - Wong, Delaney, Keryell, Liber, Chlanda** https://www.youtube.com/watch?v=ZxNBber1GOs&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=130

- 讲 sycl 这套的，不是很懂

**CppCon 2022 | C++ Concurrency TS 2 Use Cases and Future Direction - Michael Wong, Maged Michael, Paul McKenney** https://www.youtube.com/watch?v=3sO4IrWQPnc&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=23

- concurrency ts 2 主要的就是 hazard pointer 和 RCU 这两个高级底层同步工具
- talk 里面讲了一些 hazard pointer 和 rcu 的（示例）用法，反正仅供了解

**CppCon 2022 | Using Incredibuild to Accelerate Static Code Analysis and Builds - Jonathan "Beau" Peck** https://www.youtube.com/watch?v=M7zMl2WOp6g&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=21

- Incredibuild 他们家的软广
- 他们家的东西感觉不够平民化

**CppCon 2022 | Lightning Talk: 10 Things an Entry-Level Software Engineer Asks You to Do - Katherine Rocha** https://www.youtube.com/watch?v=RkH8P1RgYIs&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=144

- 新人工程师必知必会…

**CppCon 2022 | Lightning Talk: Adventures in Benchmarking Timestamp Taking in Cpp - Nataly Rasovsky** https://www.youtube.com/watch?v=-XU2silGr6g&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=143

- 告诉我们涉及系统/硬件相关的 bechmark 一定要在自己本地做
- syscall `clock_gettime()` 内部也是利用的 `_rdtsc` 指令，但是因为要多做一些事情，所以大致上慢一倍

**CppCon 2022 | Lightning Talk: Developer Grief - Thamara Andrade** https://www.youtube.com/watch?v=xI9YEp0G_JQ&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=140

- 程序员打气视频…

**CppCon 2022 | Lightning Talk: Using This Correctly it's [[unlikely]] at Best - Staffan Tjernstrom** https://www.youtube.com/watch?v=_1A1eSriCV4&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=139

- 怎么感觉内容有点少啊，这就结束了？

**C++ Weekly - Ep 483 - Stop Using rand, Start Using Random** https://www.youtube.com/watch?v=kjogmOXkipw&t=12s

- 看了视频才知道 `std::random_device()` 还能传字符串参数，而且还是 implementation-defined…
- https://en.cppreference.com/w/cpp/numeric/random/random_device/random_device.html

**C++ Weekly - Ep 334 - How to Put a Lambda in a Container** https://www.youtube.com/watch?v=qmd_yxSOsAE

- in general  每个 lambda 示例有单独的类型，所以 vector 存 lambda 不太实用
- 但是 jason 展示了一个 trick 可以借助 capture 让 vector 存一组功能一致但是有略微有不同的 lambdas
- 不过 jason 提了这个 trick 很容易 odr-violation，知道就行，尽量别用在实际代码中

**C++ Weekly - Ep 333 - A Simplified std::function Implementation** https://www.youtube.com/watch?v=xJSKk_q25oQ

- 一个不考虑各种优化细节，例如小对象优化 .etc，的实现版本
- 整天还是非常不错的，可以学习一下 type erasure 以及对 std::function 实现有一个大致的认知

**C++ Weekly - Ep 332 - C++ Lambda vs std::function vs Function Pointer** https://www.youtube.com/watch?v=aC-aAiS5Wuc

- 解释了一下这几个区别，算是科普内容

**C++ Weekly - Ep 331 - This Game Teaches C++!** https://www.youtube.com/watch?v=snQhhWE1xR4

- 推销 Jason 自己写的一个交互式学习 C++ 通过改代码来玩的游戏
- FYI：没有亲自体验

**The gotcha of the C++ temporaries that don’t destruct as eagerly as you thought** https://devblogs.microsoft.com/oldnewthing/20221005-00/?p=107248

- 核心就这句：The `->` operator returns a temporary `LockedData` object, and the rules for temporary objects in C++ are that they are destructed at the end of the *full expression*.
- 锁这种语义上就不是特别 trivial 的东西不应该搞得这么骚气和 convenient

\#2

偶然间在知乎上看到最新的 execution proposal 去掉了使用 tag_invoke 来实现 CPO 的做法，大致了解了一下新做法

只要不依赖对称性，例如 `operator+` 这种可以允许自定义类型是 lhs 或 rhs，则可以利用 member function 做 customization，不用那么麻烦。

跟着写了一个 sample：https://github.com/kingsamchen/Eureka/pull/39

BTW：让我想到很久之前有个 cppnow 的视频吐槽过这个，找了一下是 https://youtu.be/qTw0q3WfdNs?list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&t=3767

\#3

最近看完了两本 ASIO 相关的书，总感觉 gap 还是太多，所以打算把 ASIO 的 official examples 加到自己的 Eureka/learn-cxx 里并且每个都过一遍，学习一下作者提供的思路。

目前已经看完了 cpp11 目录下的接近 1/4 的例子，有不少收回，等回头 cpp11 目录的都整理完之后有时间的话会写一篇总结。

只能说 ASIO 设计的真的太太太灵活了

---

好了这周就这样，下周见
