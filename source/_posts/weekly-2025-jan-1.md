---
title: 一周杂记 in Week 1 Jan 2025
categories: CODE-LIFE
date: 2025-01-06 00:40:30
tags: [杂记]
---
本周（12/30 ~ 01/05）是2025年1月份的第一周，happy new year!

## Life

\#1

周一周二请了两天年假，所以算上上周末算是元旦五天乐。

周一的时候因为媳妇儿要上班，自己在家太无聊中午都没人吃饭所以还特意跑到办公室和老蔡还有 Hogan 去小狗吃了面。

吃完回到办公室之后歇了一会儿喝了杯自制金橘气泡水才回家。

周二 12.31 在 24 年压哨最后一天 Steam 上买了500多块的游戏，算是给24年的游戏之旅加一个脚注。🤣

周三元旦的时候基本和媳妇儿在家度过的，甚至都没出门，因为嫌弃人多

周六带媳妇儿回了趟她在奥体的家，然后拉上了岳母一起开车去临浦那边转悠了一下，看了一下一个临浦特色老街。

感觉整体也就那样子，不过比那种商业古街还是好一些，毕竟不是故意做旧。这老街让我想起1990年代的灵溪 🤔

周六晚上吃完饭之后因为媳妇儿家连个电视都没有（我一直不懂为啥媳妇儿这么拒绝给她家安电视），所以我们又开车去了附近的57公社。

讲道理这57公社其实就是商业街，比起白天的老街还要差劲，所以我和媳妇儿都感觉一般。倒是丈母娘玩的挺开心

\#2

周一下午回来的从睡眠恢复时候发现电脑的D盘又掉了...但是这次重启之后反而很顺利盘就回来了

继续 google 了一下发现这尼玛居然是 ASUS 主板和（疑似）AMD内存超频后的老毛病了，至少好几年前就有类似的反馈了...

找了半天都没有发现什么 solid solution，思索片刻之后，我决定关闭 overclocking，内存降低到 5600MHz 我还是可以接受的，毕竟升级BIOS之前开 EXPO 都只锁到了 6000Mhz，但是这掉盘我实在不能忍啊

尤其掉的还是开发盘，跑 vm 的那种...

不过讲道理如果掉的是系统盘就有意思了咯...

关掉睿频之后目前到现在都没有再出现掉盘的情况，所以先持续观望吧

\#3

周六早上给小蛋他们放粮的时候发现一直三花，主动找我要吃的。这只三花我不确定之前是否见过，但是这次这么主动我感觉有戏可以带去绝育。

所以我先用随身带的猫条为了她，和她稍微拉近一下关系，并且我发现我尝试摸她之后她并没有跑走，顿时感觉希望大增。

于是我和保洁大哥说帮我看一下三花，别让她跑远，然后我立马跑回家拿上航空箱。

比较幸运三花还在那块位置，于是我拿出猫条和罐头引诱她。

可能她已经吃饱了，对食物不怎么感兴趣了，但是还是主动找我靠近；我顺势一把抓住后颈肉，成功逮住。

不过把她放到航空箱倒是花了一番功夫，这里还得谢谢保洁大哥帮忙，不然三花挣扎几下没准就跑了。

带到医院后按照老流程，check-in 体检 + 驱虫。

体检下来说炎症指标有点高，这两天得先把炎症压下来才能手术。

一开始以为只是普通炎症，结果第二天医生说三花左前肢脚掌肿得厉害，估计是这个地方感染了。然后到了下午医生给我说还没给她做引流那个地方就爆脓了，自己爆开了...

然后看了触目惊心的几张图片，左脚掌直接一个腐烂的破口

现在只能每天清创消毒然后等腐肉自己掉了慢慢长新肉，估计这一下得在医院住上十几二十天了 🤣

三花自己倒是情绪稳定，而且只要不碰她伤口部位，她会主动让你给她摸摸

周日看小蛋的时候遇到常喂他的那个阿姨，聊了一下她觉得这只三花大概率是别人遗弃的，呃...

哦对了，入院的时候我给她起了个名字叫：乐乐，希望她以后每天开心，知足常乐

乐乐真的颜值很高，希望后面绝育之后能给她找到领养

![](img/20250104_023515731_iOS.jpg)

![](img/20250105_093530886_iOS.jpg)

![](img/20250106_121644740_iOS.jpg)

\#4

这周重启了家庭影院

- **热辣滚烫** 3.5/5 故事性一般，就是起承转合每个地方都没啥大问题但是加起来就缺了一些味道，几个配角的角色特点和刻画倒是挺鲜明的

## Work

\#1

**C++ Templates 2nd | 24. Typelists**

- 类型的元编程
- 大量抽象内容

**CppCon 2022 | Simulating Low-Level Hardware Devices in Cpp - Ben Saks** https://www.youtube.com/watch?v=zqHvN8xpuKY&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=76

- 用 class 模拟嵌入式的 device register 和 UART
- 重载 operator new / delete 可以让对象构造在固定总线上，同时语义使用传统 C++ 语义并且不发生实际 memory allocation
- 剩下的就是如何让对象使用起来更加自然

**CppNow 2024 | Functional C++ - Gašper Ažman** https://www.youtube.com/watch?v=bHxvfwTnJhg&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=50

- 内容挺多的，深度也足够，就是我在这方面实际经验少
- optional / expected 这些设施支持 monadic operations，如何基于这个操作实现 functional operations
- 但是讲道理我觉得可读性不如传统的 procedural，可能是 fp-style 接触的还是太少了

**CppNow 2024 | C++11 to C++23 in the C++ Memory Model - Alex Dathskovsky** https://www.youtube.com/watch?v=VWiUYbtSWRI&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=53

- 这篇干货好多，不仅仅涵盖了 memory model
- 第一次理解了 consume ordering

**C++ Weekly - Ep 460 - Why is GCC Better Than Clang?** https://www.youtube.com/watch?v=4P32EFClwuo

- 世界又变了，gcc 在生成综合性能更好代码这点上已经超过 clang 了… 😂

**C++ Weekly - Ep 416 - Moving From C++11 to C++14** https://www.youtube.com/watch?v=_Rq8gWimRcA

- 这一期比较老了所以是从 11 to 14
- 基本就是 more constexpr / relaxed auto return type 这些，毕竟 lang spec 上都是一个 minor update

**C++ Weekly - Ep 415 - Moving From C++98 to C++11** https://www.youtube.com/watch?v=84Zy1D8MWaI

- 这个应该是项目升级第0期了吧…
- 到 C++ 11 是有很多地方可以调整的，noexcpet enum class lambda .etc 太多了

**C++ Weekly - Ep 414 - C++26's Placeholder Variables With No Name** https://www.youtube.com/watch?v=OZ1gNuF60BU

- C++ 26 开始终于可以用 `_` 表示 placeholder variables 了
- 看效果应该是编译器处理的时候替换成了生成的unique name

**C++ Weekly - Ep 413 - (2x Faster!) What are Unity Builds (And How They Help)** https://www.youtube.com/watch?v=POYVF6urMwg

- 可以通过 `CMAKE_UNITY_BUILD=ON` 来开启全局所有 targets 的 unity build；也可以针对某个 target 的 property 设置 `UNITY_BUILD=ON/OFF` 来开启关闭
- 大工程可以考虑，尤其编译机单核主频性能很强的时候
- 另外 unity build 因为把多个 srcs 文件聚合在一起编译所以如果代码有 odr-violation 也可以顺带查出来

**Designated Initializers in C++20** https://www.cppstories.com/2021/designated-init-cpp20/

- designated initializer 除了要求目标是 aggregate 之外还有其他要求，需要注意

**Underseeding mt19937; introducing xoshiro256ss** https://quuxplusone.github.io/blog/2021/11/23/xoshiro/

- 标准库提供常用的 mt19937 seeding 的速度太慢了，作者自己 port 了一个 xoshiro PRGN，seeding 和 generating rng 都很快
- 但是 underseeding 是因为 `random_device` 只能输出 32-bit，所以其实另外一个问题；seed_seq 的输出也是只有 32-bit
- 有个 proposal 是让 PRNG 直接接受 random_device 实例，内部自己确定怎么 seeding 不过这个似乎还在 in progress

**Conditional Members** https://brevzin.github.io/c++/2021/11/21/conditional-members/

- 这篇 TMP 相关的 tricks 不少
- 对于 conditional member functions, SFINAE 对 copy-ctor 这种 special functions 是无效的，因为需要引入 template；而 concepts 有效

    ```cpp
    // special member functions
    template <typename T>
    class Optional {
    public:
        Optional(Optional const&) requires copy_constructible<T>;
    };

    // normal member fucntions

    template <input_range R>
    class adapted_range {
    public:
        constexpr auto size() requires sized_range<R>;
    };
    ```

    但是 concepts 本质上也没有 conditionning function，只是让函数不参与重载决议

- conditional_t + empty struct + [[no_unique_address]] 实现 conditional member variables
- the only way to really have a true *conditional* member in C++: you have to inherit from either a type that has that member or a type that does not have that member.

**What if I told you, you don't have to run your unit tests?** https://baduit.github.io/2021/10/24/compile-time-unit-test.html

- 对于 constexpr / consteval 的可以直接用 compile-time checks 这样只需要编译能过，不需要跑 tests

**Constructors and evil initializers in C++** https://jmmv.dev/2021/11/cpp-ctors-vs-init.html

- 核心就是“真的不要再写两段式构造啦”
- 如果不能用异常，那就用 factory method 把 invariance 这部分包一下
- 讲道理我觉得这个部分应该是常识了

\#2

这周玩的比较多，🤭

---

好了这周就这样，下周见
