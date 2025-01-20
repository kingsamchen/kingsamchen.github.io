---
title: 一周杂记 in Week 3 Jan 2025
categories: CODE-LIFE
date: 2025-01-19 23:57:03
tags: [杂记]
---
本周（01/13 ~ 01/19）是一月份第三周，节前工作的倒数第二周了 🫡

## Life

\#1

马上要过年了所以一听说可以组织团建了立马定了周二去良渚的玉鸟集附近。

一大早开车来公司，晚了就没停车位了；中午带着同事到了附近一家饭店，味道不错就是吃多了有点偏咸，吃饭算下来人均一百，还是很便宜的。

吃完之后就去了玉鸟集，那边确实适合下午散步和放松。

有些同事选择保留节目 —— 打德普，我和另外几个同事在草地上玩桌游，宝石商人，还算可以，整体下来不无聊。

玩到差不多四点半赶在下班高峰把同事送到公司后就自己回家了。

\#2

这周终于确认媳妇儿的博士申请初筛过了。

浙大卡的还是挺严的，初筛据说不超过1/3能通过；好在媳妇儿之前SCI和英语成绩都做了充分准备，所以初筛涉险过关。

算是个好开头吧

（不过媳妇儿最近想躺平了说不想读博士了想去心脏康复科摸鱼...）

\#3

乐乐这周恢复神速，脚上的伤口已经基本没有大碍了。

所以下周一早就可以安排绝育手术了。

我也提前把外卖箱和毛毯下单了，等到了之后就改造成第二个保暖猫窝。如果乐乐后面找不到领养的话就和小蛋他们养在一起好了。

\#4

本周在公司看了一部电影，家庭影院也重启了（为了支持家庭影院，自掏腰包冲了腾讯视频SVIP...）

- **达荷美** 2/5 这金熊奖至少一半是愧疚分吧，除了辩论部分其他的啥也不是，辩论部分也没啥心意。还好就68分钟
- **哪吒：魔童降生** 4/5 剧本不错，人物弧线刻画得还挺好。不知道为啥我看的时候脑子里一直浮现《边城浪子》的剧本

\#5

最近打 Darksiders: Genesis 好欢乐 😳

## Work

\#1

**C++ Templates 2nd | 25. Tuples**

- tuple 从类型上和 typelist 没有本质区别，在基础上增加了 runtime operations

**CppCon 2022 | Structured Networking in C++ - Dietmar Kühl** https://www.youtube.com/watch?v=XaNajUp-sGY&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=79

- 硬核内容，直接用 S&R 的概念把一个同步的 echo server 改造成 asynchronous
- 实现上对于 S&R 的构造是作者经过他自己的理解简化过之后的，感觉如果真的要学习 S&R 的话可以参考一下
- 不过目前对 S&R 来实现网络库还是存一点怀疑，感觉可能会有 over-complicated 尤其是性能上不一定靠谱

**CppCon 2022 | Lightning Talk: Finding the Average of 2 Integers - Tomer Vromen** https://www.youtube.com/watch?v=rUt9xcPyKEY&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=84

- 整数的 midpoint 直接用 `std::midpoint()` 即可可以解决溢出的问题，但是这个 talk 主要讲的是如果要考虑浮点数的话就是一个很麻烦的问题，因为 int64_t 的 midpoint 的个数都已经无法让 double 表示了
- 当然这个 talk 是一个开放探讨（搞笑）的，不用太当真

**CppCon 2022 | Using Modern C++ to Revive an Old Design - Jody Hagins** https://www.youtube.com/watch?v=Taf5eqUZAA0&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=78

- 讲的有点飘，没太get到点，大致一下
- hornor pipeline design，不要随意动 pre-conditions，尽量把一些调整融入 pipeline 的一些分支流程上
- concepts（talk 里代码示例用的是 expression SFINAE, though）能够带来一些实现上的更优解？

**CppNow 2024 | How Bloomberg Built a Reusable Process For Consensus on Their Massive C++ Codebases - Sherry Sontag** https://www.youtube.com/watch?v=wf1NsYrIzDs&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=57

- 核心是管理相关的软技能，组内如何达成一致啥的
- 没时间没兴趣可以不看，这东西不太好参考

**C++ Weekly - Ep 462 - C++23's Amazing New Range Formatters** https://www.youtube.com/watch?v=G6hhZGUE9S4

- C++ 26 开始 `std::print` 能够直接打印各种复杂的 range containers 了
- 及其利好

**C++ Weekly - Ep 408 - Implementing C++23's constexpr unique_ptr in C++20** https://www.youtube.com/watch?v=p8Q-bapMShs

- C++ 20 constexpr-ize string 但是没有 unique_ptr 的原因是因为漏了加这个提案…
- 走了一遍实现发现很简单只需要给各个函数加上 constexpr 就行 - - 毕竟最麻烦的 memory allocation 已经在提案里解决了

**C++ Weekly - Ep 407 - C++98 Code Restoration** https://www.youtube.com/watch?v=A5haG_UCbRI

- 一个 C++ 98 老项目改造起底
- 我基本跳着看完的…

**C++ Weekly - Ep 406 - Why Avoid Pointer Arithmetic?** https://www.youtube.com/watch?v=MsujPM2wDmk

- pointer arithmetics 很容易导致越界访问
- 尽可能使用 span string_view 这些

**C++ Weekly - Ep 405 - Dogbolt: The Decompiler Explorer** https://www.youtube.com/watch?v=h3F0Fw0R7ME

- 推荐的 https://dogbolt.org/
- 可能更适合搞逆向的人，对我来说 goldbolt 已经足够用了

**Little C++ Standard Library Utility: std::align** https://lesleylai.info/en/std-align

- `std::align` 可以把指针按照给定的 alignment 做 offset，如果可用地址空间不够了就返回 nullptr
- 另外 `std::bit_cast` 可以用作 integer ↔ pointer 转换

**T* makes for a poor optional<T&>** https://brevzin.github.io/c++/2021/12/13/optional-ref-ptr/

- `optional<T&>` 不支持的后果是整体 syntax 会变得不一致，需要很多 workaround
- `T*` 不管在语法还是语义上和 `optional<T&>` 都相差甚远（作者认为）
- 作者把 `vector<bool>` 这个陈年旧账也搬出来说明这个问题

**Almost Always Unsigned** https://graphitemaster.github.io/aau/#almost-always-unsigned

- 观点有点极端了，而且里面很多用来反驳的观点感觉是稻草人，至少我自己平日里用 signed 的时候从来不是这个原因
- 不过这篇倒是学了一些 overflow/underflow 安全判定的 tricks

**How To Implement Reflection With an Inline Macro** https://buildingblock.ai/reflection

- 作者对 boost.describe 的体验感觉一般，自己做了一个 inlined reflection macro
- 但是实际上我觉得作者这套方案更丑，直接把原来的 struct members 都给模糊了

**How we used C++20 to eliminate an entire class of runtime bugs** https://devblogs.microsoft.com/cppblog/how-we-used-cpp20-to-eliminate-an-entire-class-of-runtime-bugs/

- C++ 20 引入 consteval 之后可以利用一些 trick 然后用 fmtlib 就能实现 compile-time format-specifier validation