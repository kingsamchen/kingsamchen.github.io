---
title: 一周杂记 in Week 1 Dec 2025
categories: CODE-LIFE
date: 2025-12-08 22:39:42
tags: [杂记]
---
本周（12/1 ~ 12/7）是12月第一周，这周气温工作日略冷，到了周末就回升了，体感反而不错

## Life

\#1

秋猪周一晚上7点多就早早睡了，老婆说应该不会有太大问题，我说很可能半夜就闹醒了，结果一语成谶。

猪猪直接在凌晨两点四十多醒过来，因为此时她一口气睡了7个小时所以把大家都闹腾醒了。

我妈喂奶喂不进去，抱着也哄睡不了，一直折腾了接近一个小时，最后还是老婆把猪放回她自己床，唱了好久的摇篮曲才算给继续哄睡着。

结果自然是所有人，除了她，第二天睡眠都睡眠不佳。

尤其我这种入睡困难的，又花了好久才睡着，第二天直接精神萎靡。

\#2

上周日车后视镜被老登蹭了，这周二本来打算一早开车去极氪家修，因为猪猪凌晨闹麻了，我睡到9点多才起来，于是只能请了2个小时假，开车去极氪家送修。

check-in 整体流程很快，因为损伤也不大，十分钟就搞完了。

不过跟我说要周四才能取车的时候我还是震惊了一下...

另外就是后视镜一直以为搞歪了其实没事，调节之后记忆设置就行....

然后就是周四下午抽了个时间去把车取回来了，看了一下该喷漆的都弄好了，塑料灯件也重新换了一个

\#3

周三公司观影俱乐部活动，“运气好”蹭上了末班车去西溪博纳看动物城2

之所以带括号是因为不是 IMAX 厅，呃

- **疯狂动物城2 Zootopia 2** 4/5 故事性不如1 不过整体娱乐性还是很高的 隐喻嘛也就那样都反复说烂了 看来个人年度最佳还是F1了🤔

\#4

周五公司的 appreciation day-off，感觉就过程了普通周末。

还是一样的带娃，睡眠不够，补觉，跑步 .etc

好像周五在家写代码的时间反而少。。。

\#5

这周六丈母娘来换班，我妈的2个月带娃上班也终于结束了。

周六晚上开车送妈去东站坐高铁回老家。

本来想让她好好休息下，她说家里厂子最近事情多，回去就要开始忙。只能等到过年期间了

\#6

周日10点和 Hogan 第二次上拳击训练营。

打拳真累啊啊啊。。。

不过想说的不是这个，而是打拳的时候为了带拳套把 Garmin 手表摘下来放在一边的瑜伽垫上，结果结束的时候忘了带走。

下午才想起来，然后匆匆忙忙回到健身房发现操房里找不到了...

心急如焚啊，毕竟4600多的手表再买一块也心疼

最后好在店长给力，帮我看监控，发现12点团课结束后一堆瑜伽垫直接在了手表上面给盖住了...知道在哪后就方便了，直接回去顺利取回

oh yeah！

## Work

\#1

**CppCon 2023 | Linkers, Loaders and Shared Libraries in Windows, Linux, and C++ - Ofek Shilon** https://www.youtube.com/watch?v=_enXuIxuNV4&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=14

- 非常有干活的一个演讲，把 Linux/Windows 上的动态链接的一些核心点和坑都讲了
- Linux 动态库设计上默认支持 interposition（overriding a symbol in one binary from another） 所以默认所有符号都是公开，并且因为 -fPIC，函数符号要走 GOT 绕一圈有性能损失（约等于 virtual call 这种多一次 indirect call）；因此一般推荐默认使用 -fVisility=hidden
- Windows 上动态库使用 singleton 会有多副本问题，Linux 则没这个问题；并且 Linux 上动态库的符号是 by-default lazy binding，通过 PLT 完成；Windows 需要手动声明
- 只能说一开始设计的目标问题吧
- 多一嘴，我们项目因为架构问题，现在深受 linux so 循环依赖之苦，而且因为 http server 走的脑残 httpd，导致静态链接不用 -fPIC 也没法用，完全废了

**CppCon 2023 | An Introduction to Tracy Profiler in C++ - Marcos Slomp** https://www.youtube.com/watch?v=ghXk3Bk5F2U&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=15

- 介绍的 https://github.com/wolfpld/tracy 这个 profiling tool
- 看起来是一个比较完善的 out-of-box 的一站式解决方案；不过感觉可能更适合游戏或者 desktop interactive applications 这种

**C++ Weekly - Ep 508 - What if You're Windows Only?** https://www.youtube.com/watch?v=aEuRTzj1qrM

- 核心还是尽量在兼容目标 toolchain 前提下保证 compiler 的新版本，CI 做好，另外虽然 target 是 windows only 但是也尽量考虑跨平台，凡事就怕一个万一
- 其实 linux only 项目也是一样

**C++ Weekly - Ep 230 - Building A High Performance Bit Pattern Match DSL** https://www.youtube.com/watch?v=-GqMLnWuHTU

- 有点意思，感觉可以作为面试题
- constexpr + bitwise operation

**C++ Weekly - Ep 229 - C++20: Why Deprecate The Comma Operator?** https://www.youtube.com/watch?v=qD1aKLux5Fw

- C++ 20 开始传统的 expr1, expr2 这种用法被 deprecate 了
- 目的是为了支持 23 开始的 built-in multi-dimensional array

**C++ Weekly - Ep 228 - C++20's (High Precision) Mathematical Constants** https://www.youtube.com/watch?v=VPC17U-twrY

- C++ 20 在 `<numbers>` 头文件里引入了一些数学常量定义

**C++ Weekly - Ep 227 - RPG In C++20 - Part 6: Dealing With Game Events - 2** https://www.youtube.com/watch?v=wRTWnGse3vo

- 直播写 RPG 游戏

\#2

这周先抽了个时间改造了一下 request/response 的用户接口，尤其是 response 中设置 status code 和 body 的部分。

原先依赖内部实现的 beast 原生接口，但是那些接口的用户友好度确实欠缺一些。

PR 看着 https://github.com/kingsamchen/fawkes/pull/14

整体感觉有好了不少

\#3

做完上面这个之后我想着先做一下 cookie 的支持，感觉应该不会太麻烦，做完了再搞 executor/io-threads。

结果开始搞了之后发现我靠坑比想象中的多。

因为 cookie 区分 server-side set 和 client-side set，并且如果要整合到一个 http request/response 体系中同时给 fawkes http server 和 fawkes http client 用的话，事情就会变得麻烦的多。

并且还要考虑到 RFC 中规定的以及没规定的各种编码/转义/要求等等...

所以目前的打算是先做 server-side 支持为主，也即

- request 能解析 client 发过来的 `Cookie` header
- response 能方便的设置发给 client 的 `Set-Cookie` header（包含那坨 attributes）

等后面开始搞 http client 了再补上欠缺的；不过这也要求设计的时候需要预留一些口子...

这周末算上周五的 day off，吭哧吭哧也没搞完，只能留到下周弄完了。

---

好了这周就这样，下周见
