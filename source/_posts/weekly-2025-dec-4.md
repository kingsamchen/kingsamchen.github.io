---
title: 一周杂记 in Week 4 Dec 2025
categories: CODE-LIFE
date: 2025-12-29 22:10:58
tags: [杂记]
---
本周（12/22 ~ 12/28）是12月份的最后一周，也是2025年的最后一周。

下周四是26年新年元旦，并且因为下周26年的天数以微弱优势胜出，所以下周归属26年第一周。

## Life

\#1

因为上周遇到了雾霾天，考虑到秋宝，动了买好点的空气净化器的心。

研究了几天，最后买了 blueair 4.0 放在秋宝房间，以及 8880i 钢铁大白放客厅。

都到了之后我给老婆吹嘘 8880i 多牛逼，可以主动抑菌杀毒，结果被老婆一顿教育说太干净不利于宝宝免疫系统的形成，这个功能不能开。

我转念一想，这机器5k多，一个滤网1200多，有一半的价值在主动抑菌杀毒上，不让用的话我还选这个不是冤大头？

还好包装箱都没拆封，直接走售后退了，换了 new classic pro 9i，机器便宜2k，滤网便宜一半，而且普通污染物的清洁能力翻一翻。

两个机器都就绪后心里安心多了。

PS：cp9i 对于 TVOC 类的检测过于灵敏，丈母娘在厨房煮挂面料酒倒的多，客厅另一头的cp9i就直接进入了 red alert 开始疯狂转...不过也是个好事

\#2

这周三 WFH 中午想去乐刻健身一下，结果骑到银泰停车的时候电驴脚撑断了...

天空下着小雨，我满大街找可以重新焊接的店，还好在附近找到一家（实际上就是某次半夜回来给轮胎打气的店）

花了20师傅给冲洗焊接好了，完了还不忘说我这款雅迪的脚撑设计得非常不合理...

因为最近 HRV 和 RHR 指标不是很好，最近两周运动量锐减，而且看时间12月份90公里目标是完不成了，虽然年度1000公里月初就达成了

不过周日又上了一节拳击训练营，练的比较多，强度也不小，感觉有点累。

\#3

这周是 perf review week，然后让我知道了几个让我非常不爽的事情，也更加确信了阿里出来的管理者都是伪君子的事情。

本来以为傻逼老崔今年至少会保持体面，明年才会开始对我们原团队的人动手，没想到这次就急不可耐的开始搞人了；而且作为之前好几次当中硬刚过他的人，我又一次直接跑到了清洗名单前列。

今年真的太有意思了，一样的配方一样的味道。老崔和老张简直是异父异母亲兄弟，只不过一个真小人，一个伪君子。

这事情也间接导致这周 HRV 和 RHR 又开始剧烈波动，刚开始好转的状态又急速掉头向下。

想了一下，如果没有什么转机的话，26年大概是现在这个团队的最后一年了。需要提前规划想想路子了

\#4

这周唯一的 comfortable thing 就是逃离鸭科夫了吧，确实好玩，叔叔这回找的团队没毛病。

嘎嘎搜刮拯救不爽！

## Work

\#1

因为要给 fawkes 加 timeout/cancellation 所以重新看了 Chris Kolhoff 几年前的 talking async 的两期视频

**Talking Async Ep1: Why C++20 is the Awesomest Language for Network Programming** https://www.youtube.com/watch?v=icgnqFM-aY4

- 展示了 coroutine 在 ASIO 中的用法
- 以及使用 || 和 && 管理 parallel operations

**Talking Async Ep2: Cancellation in depth** https://www.youtube.com/watch?v=hHk5OXlKVFg&t=17s

- 异步操作的 cancellation 存在race：发出 race 的时候前一个操作已经完成了并且调用了 completion token，所以 cancel 的时候需要设置一个 indication/flag 给 completion token 表明前面已经 cancel 了，不要再发**后续**请求

    在这期里最开始，Chris 用的 socket 是否打开作为标志

- ASIO 提供了一个非常 low level 的 cancellation slot 机制来实现绝大多数 cancellation，不过除非特别需要，应该用这个机制之上建立的 higher level abstraction
- parallel group 是其中之一，可以控制 wait for all 还是 wait for one，可以搭配 timer 来实现 timeout；同时如果使用 coroutine，提供了非常直观好用的 `||` 和 `&&` 操作符重载（上一期有用到）
- 后面还展示了 cancellation slot 的高级应用，以及 cancellation type；不过对于 web 服务来说可能不需要区分那么细

**C++ Weekly - Ep 220 - C++20's [[likely]] and [[unlikely]] With Practical use Case** https://www.youtube.com/watch?v=ew3wt0g99kg

- 比较囧的是用来展示的示例代码没有明显区别

**C++ Weekly - Ep 218 - C++20's std::to_array** https://www.youtube.com/watch?v=IcQl1Oh9AvQ

- 终于有这玩意儿了，把 c-style array 转换成 std::array
- 以前用 std::array 一个蛋疼点是 size 需要手动给定，如果指定类型的话，现在不用了

**C++ Weekly - Ep 219 - RPG In C++20 - Part 4: Reading SFML Joystick States** https://www.youtube.com/watch?v=jUh9q_qWhlM

- 直播写代码系列

**C++ Weekly - Ep 217 - Doom vs C++: Round 1: Initial Results**  https://www.youtube.com/watch?v=Nand3PEV1p4

- Jason 把 crispy doom 给移植成C++，同时做一些 modern cpp replacements

**C++ Weekly - Ep 216 - C++20's lerp, midpoint And Why They Are Necessary** https://www.youtube.com/watch?v=gwHL4EOb-R8

- 介绍 std::midpoint 用来安全的计算中点（经典的溢出处理）
- std::lerp 是计算线性插值，数值计算或者游戏中应该经常用，我不是很懂

\#2

这周研究了一下知乎上优化虚函数调用的这个[回答](https://www.zhihu.com/question/647830693/answer/1897684167896043873)，学习了一下核心技法。

值得注意的是这个 idiom 应该算是流传甚广，不光 ASIO 大量使用，下面有个回答本质上也是这个 idiom。

这个 idiom 核心是压缩虚表只考虑之有一个运行时多态接口的情况，所以性能上

- 传统虚函数需要 load vtable via vptr -> load vfnptr -> invoke 两次 memory load + 1次 virtual function call，并且 virtual call 几乎不能被内联（devirtualization 目前过于苛刻）
- 这个 idiom 只需要 load pfn -> invoke pfn -> invoke real-impl 一次 memory load 两次 function call，并且第二次 call 通常都能被内联，因为函数上下文编译期都是可知的
- 这个 idiom 的 pfn 存储的位置里 object 边上，所以 cache 友好

根据给出的实测报告，高频繁调用下，这个 idiom 可以节省 5%~10% 的时间，并且实际操作越轻量，这个提升越明显

如果函数实际要做事情比较重，virtual functions 自身的开销其实是可以忽略不计的

\#3

这周因为狗逼的事情比较多，所以 fawkes 目前没有进一步的进展

---

好了这周就这样，下周（明年）见
