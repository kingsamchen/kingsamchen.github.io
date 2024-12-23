---
title: 一周杂记 in Week 3 Dec 2024
categories: CODE-LIFE
date: 2024-12-22 23:18:17
tags: [杂记]
---
本周（12/16 ~ 12/22）是十二月的第三周，也是倒数第二周。

## Life

\#1

先说一个这周绷不住的事情。

公司的办公楼是一个村办企业大楼，因为年代久远，建筑质量差而被我们称为电诈大楼。

最近发现一个更蛋疼的问题：8F办公室的新风系统会把楼道的空气抽进来，而只要有人在楼道吸烟，就会导致你在办公室也能闻到混有二手烟的空气。

然而偏偏这个电诈大楼有其他公司，比如婚礼纪和兑吧，有相当多的低素质员工喜欢在楼道吸烟；而这个电诈大楼他妈的居然楼道没有烟雾传感器。

于是我直接 12345 投诉反馈了这个问题，结果没想到杭州的执法居然这么文明，问题转交到古荡街道之后，街道只是告知了物业这个问题，然后就是各种场面话，什么会加强巡逻，劝离抽烟者这些屁话。

所以我自然对处理反馈打了1星，然后回访电话里也是直接说了这个事情。

但是很显然的，这就是没法解决。

所以，我日你妈的，老子以后自己给自己多家几天在家办公。

\#2

周日带小蛋打了第二针疫苗，小家伙还是很乖，没怎么折腾。

打完留观之后就带他回他的窝了。

最近发现有只小狸花特别喜欢和小蛋睡在一个窝里，之前是一直小橘...

不知道这一只橘一只小狸都是啥性别...

\#3

丈母娘这周来杭州参加乒乓球赛，于是周六下午陪媳妇儿查完房之后就开车去丈母娘比赛的球馆等她比赛结束。

结果还没到就说已经结束了，进了决赛拿了第五名，但是说成绩已经突破了。

回到媳妇儿家之后媳妇儿说不太想走远吃完饭，于是小区底下就近还是选择了清真拉面，味道其实还行...

饭后在小区的鲜丰水果第一次买一根40块钱的甘蔗 🤣

甜是甜，但是再怎么甜也不至于一根甘蔗要40块钱啊，还是优惠之后的价格，真的离谱。

鲜丰啊，我估计难活过未来两三年

\#4

本周观影

- **雄狮少年2** 5/5 没看过1参加公司观影活动看的2，评价就是：好看！感觉在看男主不玩无厘头的周星驰电影（青春燃版破坏之王），剧本扎实节奏起伏控制的很好，动作场面设计的很棒，角色特点还算鲜明就是阿娟木讷地有点平了反而不如其他人，十几年前的上海感呈现的很好，广东和上海腔加分

## Work

\#1

**CppCon 2022 | C++ Algorithmic Complexity, Data Locality, Parallelism, Compiler Optimizations, & Some Concurrency** https://www.youtube.com/watch?v=0iXRRCnurvo&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=67

- 科普了一下 CPU cache 得一些基本属性，然后讲了一下 OOP 对比起 Data Oriented 得缺点，但是后面这部分比较水
- 而且比较蛋疼的事科普 cpu cache 得时候又没有科普好，我都忘了 n-way associative cache 是咋个定义了

**CppCon 2022 | A Faster Serialization Library Based on Compile-time Reflection and C++ 20 - Yu Qi** https://www.youtube.com/watch?v=myhB8ZlwOlE&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=71

- 讲的作者自己搞得那个 struct pack 咋实现得
- 核心是利用一个 variadic value initialization trick 来计算 member count，然后用 if … constexpr 配合 structural binding 拿到每个成员的 ref
- 然后是一些优化得 tips

**CppCon 2022 | Reviewing Beginners' C++ Code - Patrice Roy** https://www.youtube.com/watch?v=9dMvkiw_-IQ&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=72

- 讲关于如何对待新手的吧…

**CppNow 2024 | Reintroduction to Generic Programming for C++ Engineers - Nick DeMarco** https://www.youtube.com/watch?v=tt0-7athgrE&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=48

- Great talk
- 从 typescript 开始延伸到 C++ 的 concepts 讲设计泛型接口的时候应该怎么正确的做约束
- 还有一些 engineering tips

**C++ Weekly - Ep 458 - array of bool? No! constexpr std::bitset!** https://www.youtube.com/watch?v=E3sfXAaR1E4

- 介绍了一下 std::bitset 以及 C++ 23 开始全面 constexpr 化

**C++ Weekly - Ep 424 - .reset vs →reset()** https://www.youtube.com/watch?v=HgPfbYfV9eE

- 核心就是 `std::unique_ptr<std::any>` 这种类型里外都有 `reset()` 接口，使用的时候容易弄混淆
- 我觉得是个问题，但是同接口保持一致这点下没有更好的解法，类似的还有 `std::optional<bool>` 这种

**C++ Weekly - Ep 422 - Moving from C++20 to C++23** https://www.youtube.com/watch?v=dvxj39gZ22I

- 这一集没啥干货，就是把一个项目代码从 20 迁移到了 23 做了一些调整而已

**C++ Weekly - Ep 423 - Complete Guide to Attributes Through C++23** https://www.youtube.com/watch?v=BpulWncdn9Y

- 这一期不错，展示了11到23的各种 attributes 的用法

**C++ Weekly - Ep 421 - You're Using optional, variant, pair, tuple, any, and expected Wrong!** https://www.youtube.com/watch?v=0yJk5yfdih0&t=6s

- 这些内容不错
- 这些类型使用上很容易导致 RVO 不生效，要多一次 move construction
- 考虑到 expected 未来可能会大面积使用，如果没有RVO保证的化生成代码得性能可能比使用异常还要糟糕
- 不过我猜 absl/StatusOr 一样有类似的问题

**Observer Pattern in modern C++** http://codingadventures.org/2021/10/30/observer-pattern-in-modern-c/

- 废话太多了，核心就是用 `map<string, vector<std::function<>>>` 做了一个 event system，key 是 event name
- 保证只在一个线程使用的话确实不用考虑线程安全性；另外实践中最好不要搞成全局“唯一”一个 event system，不然维护有点蛋疼

**In C, how do you know if the dynamic allocation succeeded?** https://lemire.me/blog/2021/10/27/in-c-how-do-you-know-if-the-dynamic-allocation-succeeded/

- 感觉也是个老话题了，malloc/new这些内存分配操作只是分配了虚拟内存地址空间，只有实际访问的时候触发 page fault 才会实际分配物理内存
- 所以严格来说检查返回值并不能保证内存分配一定成功；但是反过来想，如果虚拟内存都分配失败了，实际的物理内存也是压根没法进行的

**Three reasons to pass std::string_view by value** https://quuxplusone.github.io/blog/2021/11/09/pass-string-view-by-value/

- 对于 string_view 或者其他 small & trivial values 传值可以避免多一次的间接引用，因为值可以直接通过寄存器传递访问；同时因为不需要取地址所以对象就不用强制存在地址，那么可以不分配到栈上直接走寄存器（对象如果需要支持取地址那必须要放到栈上，因为寄存器没有地址）；传值后新副本可以保证 no-aliasing 可以让编译器更好优化

**A footnote on “Three reasons to pass std::string_view by value”** https://quuxplusone.github.io/blog/2021/11/19/string-view-by-value-ps/

- 上一篇得补注，MSVC X86-64 ABI 下就算 string_view pass-by-value，编译器也会按照 pass-by-ref 得方式生成代码，真离谱啊…
- 不过有些场景下 MSVC on ARM 是可以正常生成优化代码的，最后一个 eliminate aliasing 是 MSVC optimizer 得问题，所以无解
- 但是即使如此，按照 ideal case 写代码

**Is SIMD all you need?** https://zhuanlan.zhihu.com/p/430223278

- AVX-512指令和其他指令**混用**时，AVX-512指令导致短时间内CPU降频（~2ms），从而影响其他非SIMD指令的执行，最终导致整体性能下降
- 当进行某些更加复杂、耗能的计算时（例如AVX-512中的FMA计算），CPU必须要保证自身热能与电能的能耗不超过限制，就是通过自身主动升频、降频，能够在保证能耗限制的同时，尽可能对程序进行优化。
- 需要注意一下结论：将 avx-512 指令相关的处理放到单独的线程/core 上跑，避免影响到其他 cores
- 不过新硬件升级之后或许这个问题会有极大缓解

\#2

这周心血来潮，把之前联想拯救者 y9000x 的系统换成了 Linux Mint 22.

估计因为这机器硬件有点老了，所以装完之后居然没有遇到硬件驱动问题，除了指纹识别好像不知道怎么装驱动开启之外，其他的都很顺畅。

花了不少时间把系统 setup 好了，以后可以作为正儿八经外出干活的设备了。

PS：我在 Mint 上用 Microsoft Edge 做浏览器，用 Clash For Windows 跑梯子，是不是很抽象 🫡

不过因为是原生 linux desktop 了，所以这回终于把 CLion 也装上了，看看原生桌面的话和 vscode 差距几何。毕竟再不用 JetBrains 家的东西，这 all product license 25年中就要白白过期了

---

好了这周就这样，下周见
