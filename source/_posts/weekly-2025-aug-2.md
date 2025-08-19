---
title: 一周杂记 in Week 2 Aug 2025
categories: CODE-LIFE
date: 2025-08-19 09:32:12
tags: [杂记]
---
本周（08/11 ~ 08/17）是8月份的第二周，炎炎夏日又来了。

这周事情还是太多了，每天忙的不行。

## Life

\#1

这周媳妇儿买的贝亲的奶瓶奶嘴终于到了，给秋秋换上之后目前的反馈是非常不错的。

贝亲的奶嘴设计不容易流出奶，秋秋吃奶的时候不容易呛到，最近一周每次都能稳定喝 130~140ml 的奶，涨势喜人，感觉要真的进一步🐖化了，🤭

不过秋秋的体重还是很标准的，这周八十多天了，体重基本稳定在11斤以内，算是标准体重。

周末两天继续到媳妇儿家奶了两天娃，这娃越看越可爱

\#2

周二晚上又尝试压心率跑了一次，这次感觉非常不错，全程没有超过165bpm，而且超过了30分钟之后才开始超160bpm

周四晚上心率没有控制的周二那么好，感觉和其他因素有关，比如当天气温，健身房温度湿度甚至当天睡觉情况，不过因为心率超过165bpm几分钟后我会立马停止，所以其实跑完之后也没那么累。

现在想想之前跑步之后比较劳累应该是在高心率（>= 170bpm）跑的时间过久。

周五继续和 Hogan 和 Guts 上了一次 BP，还行，不过下周因为要补跑步，所以下周五应该就不上BP了。

周日继续上了一次 BC，这次BC的反应没有这么大了，具体反应看周一周二全身有多少酸痛。

\#3

周三电影俱乐部看了《完美陌生人》同样班底的《关于约会的一切》

周日晚上和媳妇儿一起看了戏台。

- **关于约会的一切 4/5** 确实是成人版的头脑特工队，题材新颖度有些意思，其他的家长里短部分惊喜不足
- **戏台 4/5** 陈佩斯话剧改编的电影，电影化之后话剧感还是很重，这点算是明显的缺点。大部分演员的表演不错，除了六姨太

## Work

\#1

**CppCon 2022 | Personal Log - Where No Init Has Gone Before in C++ - Andrei Zissu** https://www.youtube.com/watch?v=0a3wjaeP6eQ&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=92

- 这个 talk 有点神奇，演讲者面临的问题是公司产线上打出来的日志也要加密/mask，作者选择的方案是把字符串替换为 hash，搭配 LINE 可以在线下 decode 回来

**C-- Weekly - Ep 300 - The Least Portable Programming Language?** https://www.youtube.com/watch?v=V0z9PKFe4jY

- Jason 水了一期，这期主要介绍一个冷门的 C—，介于C和汇编之间
- 然后Jason回忆了一下往昔峥嵘岁月…

**C++ Weekly - Ep 301 - C++ Homework: `constexpr` All The Things** https://www.youtube.com/watch?v=cpdjQiRxEJ8

- 留作业了，把一个现有的图形相关的给 constexpr 化，摊手

**C++ Weekly - Ep 302 - It's Not Complicated, It's std::complex** https://www.youtube.com/watch?v=s_1SymtU0BI

- 介绍 std::complex

**C++ Weekly - Ep 303 - C++ Homework: Lambda All The Things** https://www.youtube.com/watch?v=_xvAmEbK1vE

- 上面那个作业课的后续，这次是 lambda 化

**C++ Weekly - Ep 304 - C++23's 'if consteval’** https://www.youtube.com/watch?v=AtdlMB_n2pI

- 其实是 **C++ Weekly - Ep 308 - 'if consteval' - There's More To This Story** 的前传
- 搭配 EP 308 看
- 反正就是不要用 std::is_constant_evaluated，能用 if consteval 就用这个

**When an instruction depends on the previous instruction depends on the previous instructions… : long instruction dependency chains and performance** https://johnysswlab.com/when-an-instruction-depends-on-the-previous-instruction-depends-on-the-previous-instructions-long-instruction-dependency-chains-and-performance/

- 这篇研究的是微观指令上的优化：如果有很长的指令序列存在数据依赖，会导致指令执行层面的并行度下降，导致性能下降
- 文章中利用引入一个新变量在做到 interleaving，指令依赖很长的时候可以有2x的提升
- x86/64 ISA 上因为有比较大的 on-standby 指令序列，所以没有 ARM 这些架构上这么明显

**Exception Handling in C++ Multithreading** https://www.youtube.com/watch?v=Fm3dlAzEQmg

- 感觉没啥新东西，都是很基础的利用 future/promise 搭配 exception_ptr 来跨线程传递 exception

\#2

这周没时间继续搞 fawkes，因为公司那边的 task 还没忙完，尤其要研究怎么给 spdlog 做一个 wrapper，以支持 structural logging 同时 multi-sink 不影响 console 输出。

spdlog 通过设置 message pattern 可以很方便的把消息转换为 JSON format，但是如果想支持 additional context，例如 `reqid` 这些自定义的 metadata 字段以单独的字段保存，而不是一起塞进 message 里，还是需要自己做一些调整。

周末做了一个 PoC 差不多算是可以比较满意的做到目标效果，下周的目标就是正式 finalize 到公司的代码里

---

好了这周就这样，下周见
