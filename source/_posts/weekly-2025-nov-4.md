---
title: 一周杂记 in Week 4 Nov 2025
categories: CODE-LIFE
date: 2025-12-01 20:57:28
tags: [杂记]
---
这周（11/24 ~ 11/30）是11月份最后一周，下周一开始就是本年度最后一个月了。

## Life

\#1

这周五上午请了半天假，带着娃去社区医院体检和打疫苗。

因为是回到拱墅这边房子后第一次社区医院，老婆把我们街道的对口医院弄错了...

不过还好照样可以体检打疫苗，只不过不好做娃的信息归档。

指尖采血娃居然都没哭，给我震惊到了；打疫苗的时候挨了两针也只是很短的哭了一下，不疼之后就情绪正常了。

到家吃完中饭后老婆去医院上班，我选择 WFH 观察情况。

还好娃没有什么明显很大的副作用，除了折腾了一上午导致各种困，几乎睡了一下午。

\#2

和 Iker 约了周四晚上看**铁血战士：杀戮之地**。

因为拍片下降（要给动物城2让路）所以已经没有 IMAX 拍片了，所以找了一个荧幕稍微大一点西湖文化广场的浙影时代。

到了之后发现是在地下，Iker说这是浙影新收购的，以前是博纳...

比较让人恶心的是坐我前面的八婆真的说个不停，逼得我直接发火之后才停下来。

至于影评：

- **铁血战士：杀戮之地 Predator: Badlands** 3/5 还是我的期望太高了，以铁血战士为正面叙事核心完全撑不起超过100分钟的单独电影。一开始以为是个寻求自我认同的片子，结果一半开始就回归到极其传统的类型片套路，到了结尾又是极其别扭的杂糅。

\#3

周日上午和媳妇儿一起去医院，我做个抽血测个血脂和生化，媳妇儿去查房。

本来以为我要在会议室等到中午一起去吃饭，结果老婆查房速度比预计的快，10:40不到就结束回家了。

我看时间还有就约了个团课，想着到家歇一会儿还能上个 BC 美滋滋。

结果开到半路右后视镜被一辆五菱宏光的左后视镜撞歪了，我当时整个人都 ????

确认后视镜问题后我在红绿灯路口追上那辆车，摇下车窗对司机说了这个事；结果他妈那个老登一口杭州话叽里咕噜也不知道说了啥，绿灯后直接就跑了...

我一肚子回到家后检查了一下后视镜，外壳漆面擦伤，而且虽然可以通过车技调整到正确位置，但是每次启动后后视镜都有问题需要调整...

我认真看了哨兵拍的视频，发现那辆货车直接压线行驶轮子都进我车道了。这下更窝火了，和我妈说了之后直接报122，然后等着给对面传哨兵拍到的视频。

没多久交警说下午3点到事发地附近的交警岗亭协调处理，对面也通知了。

后面就是 routine 了，我提供视频，交警核实，最后判定老登全责。老登一开始各种不认，但是最后扯了半天还是同意在认定书上签字，我走老登的保险修我的车。

不过代价就是下午2:30一直耗到5:30才结束，整整三个小时...

剩下来就是下周去修车了...尼玛这又要花不少时间。

## Work

\#1

**CppCon 2023 | Back to Basics: (Range) Algorithms in C++ - Klaus Iglberger** https://www.youtube.com/watch?v=eJCA2fynzME&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=13

- 介绍了不少处理 range 相关的算法和如何利用 ranges 来做优化（这部分少）
- ranges 可以支持 projection 这点真的可以，就算不用 pipeline operator 也有一些使用体验改进
- 另外这个老哥的 talk 是我见过真的非常适合学习的，循序渐进，非常易于理解学习

**C++ Weekly - Ep 507 - Insidious Accidental Lambda Conversion** https://www.youtube.com/watch?v=b3fFxneoHso

- 核心点就是 capturelss lambda 在 if branch 中被 decay 为 function pointer 然后进行了不为 nullptr 的检查（实际上是希望调用函数的 typo）
- 不过这个我觉得和 lambda 倒没啥关系…

**C++ Weekly - Ep 234 - map[] vs map.at()** https://www.youtube.com/watch?v=kDqS1xVWGMg

- 非常有意思的一集
- at 虽然找不到时会抛异常，但是 operator[] 要做的事情更多，所以前者性能反而会高一丢丢

**C++ Weekly - Ep 233 - std::map vs constexpr map (huge perf difference!)** https://www.youtube.com/watch?v=INn3xa4pMfg

- 这里的 constexpr map 其实就是一个 compile time array with linear search，不过一切都可以发生在 compile-time
- 但是 clang 看起来优化不如 gcc；不过这个思路值得借鉴

**C++ Weekly - Ep 232 - C++20's `using enum`** https://www.youtube.com/watch?v=JM8F2DPm_0E

- 介绍 using enum，适合在函数内部大量需要 switch case 时候使用

**C++ Weekly - Ep 231 - Multiple Destructors in C++20?! How and Why** https://www.youtube.com/watch?v=A3_xrqr5Kdw

- 利用 concepts 可以搞出一个单独于 trivial default dtor
- 想了一下 SFINAE 估计也可以

\#2

终于在这周有时间整理了一下老早之前说要搞的 ASIO/awaitable 使用的一些总结的

内容不长，但是最好搭个环境跟着跑一下。

直接看这里吧 {% post_link using-asio-awaitable Using ASIO awaitable the Easy Way %}

\#3

这周把 fawkes 的 CORS middleware 做完了。

这个 feature 最大的坑不在于 CORS 本身，而在于之前设计的 middleware 的 flow没法用。

因为之前 router level 的 middlewares 也是通过 closure 绑定到 route associated handler 里的，所以对于 CORS preflight requests 压根没有 handler 关联到 OPTIONS method 上...

还好这部分改起来不算太难，除了要维持如下两个原则花了一番功夫之外：

1. user handler 中抛出的异常不应当 abort middlewares，无论是 router level 还是 per-route
    因为我个人倾向于在 restful CRUD 业务中合理使用异常来表达错误；当然框架层面一定要保证足够的性能
2. middlewares 中不应该使用异常来表达无法继续，所以 middlewares 中抛出异常被认为是 unexpected/unintended error，后续的 flow 直接 abort

最终的 PR 看这里 https://github.com/kingsamchen/fawkes/pull/13

另外遇到一个比较难受的编译器判断过于严格的问题。

为了方便使用 CORS options，这个类我设计成了 [aggregate](https://github.com/kingsamchen/fawkes/pull/13/files#diff-a6285fca5b9bfdfe5ce90107174d4136f0a09caef236bb2aa52fac481897fea4R66) 这样可以使用 designated initialization。

但是即使成员中 `std::vector<X>` 本身就是支持 default constructible，如果初始化的时候不显式提供 initializer，不管是 gcc 还是 clang 都会报 missing fields 的告警...

没办法，只能手动补上空初始化作为 [workaround](https://github.com/kingsamchen/fawkes/pull/13/files#diff-2c200c6f4b8424e3b73874ec579972f139ac5dec0354efc2a933cdff4ad03e24R79)

---

好了这周就这样，下周见
