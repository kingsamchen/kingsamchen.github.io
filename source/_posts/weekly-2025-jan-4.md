---
title: 一周杂记 in Week 4 Jan 2025
categories: CODE-LIFE
date: 2025-01-26 23:30:23
tags: [杂记]
---
本周（01/20 ~ 01/26）是一月份第四周，本周五已经开启了春节休假旅程 😊

## Life

\#1

第一个好消息是乐乐终于“完成”了绝育，这回折腾的真是一波三折。

之所以给“完成”加引号是因为一开始要给她绝育结果发现了脚上有伤，所以就一直休养了两周才把伤口弄好。

第二次要绝育的时候断食断水结果乐乐直接把肚子里不知道存了多久的火腿肠外衣和树叶都给吐出来了（这得是饿了多久啊。。。）为了安全起见，拍了CT发现胃部真没东西了才继续

然后第三次的时候发现她的腹部有疤痕，疑似已经绝育过了。但是如果按照正常流程开个口子发现已经绝育了创口就会很大对她现在的状态恢复也不好；所以最后花了1888搞了一个微创腹腔镜进去检查；结果发现两侧卵巢右侧没有弄干净...

不过不管怎样这回终于使尘埃落定了。

帮她把住院钱交到了农历初五我回来，到时候如果能找到领养就领养，找不到就和小蛋他们做朋友。

2月份打算把和小蛋一起混的小狸给绝育了 🫡

\#2

周五请了一天假，带着媳妇儿去了社区医院把档案卡啥的都拿回来了，然后去了边上的市妇保做了一些基础检查和三维超声。

媳妇儿很嫌弃三维超声出来孩子长得难看... 🤣

我说这才22周啊，发育都不完全，急啥。。。

\#3

因为周五要带媳妇儿去检查，所以索性也请假了，算上下周周日周一和节后4天，我的17天春节假期算是开启了 😊

\#4

本周观影

- **隔壁的房间** 4/5 色彩和房间布局是真的有意思/出众，对待死亡的观点也挺有意思。看完更想看看破地狱了，对比一下东亚的死亡观
- **胜券在握** 3/5 演员的表演在线，但是剧本实在太拖后腿了。要么就走打工人VS公司这个路子，要么就走热血番的套路，两边都想要结果就搞得两头哪都不沾，整体效果非常虚

\#5

进入假期后玩 darksiders: gensis 玩的有点疯，好几天作息不规律了都

而且看了一下有个成就要考验解密中的跳跃落点这些，估计非常难那，应该全成就无望了。

所以先放一放，先把生活弄回正轨再说...

## Work

\#1

**CppCon 2022 | Refresher on Containers, Algorithms and Performance in C++ - Vladimir Vishnevskii** https://www.youtube.com/watch?v=F4n3ModsWHI&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=83

- 核心还是 contiguous memory container 相比 node-based 能带来的优势
- 如果需要有序性，sorted vector在中等规模以下小数据量比 std::set 更好
- 如果不需要有序性，各种 flat_set/map 在适应的规模下性能更好

**CppCon 2022 | Lightning Talk: The Lambda Calculus in C++ Lambdas - David Stone** https://www.youtube.com/watch?v=1hwRxW99lg0&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=82

- 纯搞笑向… `![](){}` 会变成指针判定然后配合连续两个 `-` 就转换得到了 1 然后就开始整活了

**CppNow 2024 | Zero-Cost Abstractions in C++ - High Performance Message Dispatch - Luke Valenty** https://www.youtube.com/watch?v=DLgM570cujU&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=55

- 呃..有点难评，这个优化大部分都是 micro-optimization 而且这个是针对嵌入式的场景
- 不过利用 boolean algebra 那段感觉有时候可以参考一下
- 还有就是自己模拟 PEXT 指令输出 hash 这个

**C++ Weekly - Ep 463 - C++26's Safer Returns** https://www.youtube.com/watch?v=T4g92jtGkXM

- 内容很短：C++ 26 开始，compliant 的编译器要对 return reference to tmp variables 出发编译告警

**C++ Weekly - Ep 404 - How (and Why) To Write Code That Avoids std::move** https://www.youtube.com/watch?v=6SaUwqw4ueE

- 核心内容其实是：如果能用到 RVO，那么比构造后再 move 更好

**C++ Weekly - Ep 403 - Easier Coroutines with CppCoro** https://www.youtube.com/watch?v=TWoZ9SGIE9o

- 介绍了一下 cppcoro
- 现在是有社区 fork 的专人维护版
- 不过在网络IO这块的性能估计还是不太行

**C++ Weekly - Ep 402 - Reviewing My 25 Year Old C++ Code (IT'S BAD!)** https://www.youtube.com/watch?v=7kqxYZKm64A

- Jason 自己的老项目的 review
- 对新手比较友好

**C++ Weekly - Ep 401 - C++23's chunk view and stride view** https://www.youtube.com/watch?v=3ZeV-F1Rbaw

- ranges::chunk 可以把一个大 range 按照 chunk size 划分成多个chunk
- 一些数据处理场景会用到

**const all the things?** https://quuxplusone.github.io/blog/2022/01/23/dont-const-all-the-things/

- 有点意思，尤其对于 local vars 是否应该 const 这点，可能会无意间 inhibit implicit move
- clang-tidy 有个 const-ness 的 check，感觉值得一起比较一下

**A Good Way to Handle Errors Is To Prevent Them from Happening in the First Place** https://www.fluentcpp.com/2022/02/25/a-good-way-to-handle-errors-is-to-prevent-them-from-happening-in-the-first-place/

- 这篇看下来感觉意义不大，发生错误的时候要 fallback 到 happy path 本身就是一种 error handling，并没有 prevent errors from happening
- 出现 errors 可以不 handle 但是一定是有 report 的，没有report你怎么知道该 fallback 呢，最多是把这些 errors 内部消化了不往上扩散而已

**Supervising in C++: how to make your programs reliable** https://basiliscos.github.io/blog/2022/02/20/supervising-in-c-how-to-make-your-programs-reliable/

- 做了一些 actor model 的科普，然后推广作者自己写的 cpp-rotor 框架  -’’-

**Storing references of pointers in containers in C++** https://www.sandordargo.com/blog/2022/03/30/vector-of-references-of-pointers

- TL;DR 用 `std::reference_wrapper<>`
- 之所以要搞的这么鬼畜就是因为老代码的各种奇葩呗，理解

\#2

纯浪了一周 🤡

---

好了这周就这样，下周见
