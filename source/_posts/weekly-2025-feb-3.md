---
title: 一周杂记 in Week 3 Feb 2025
categories: CODE-LIFE
date: 2025-02-25 00:14:56
tags: [杂记]
---
本周（02/17 ~ 02/23）是2月份第三周，主旋律依然是想躺平 🤣

## Life

\#1

老婆这周发现杭州市区也有一些医院的妇产科开放了月子中心来维持经营。

虽然月嫂/阿姨是外包的，但是医生护士团队都是本院的，这算是打开了一个新的思路 🤔

预计3月份丈母娘上杭州带她们去考察一下媳妇儿家边上的几家医院的月子中心，以便选取一个最合适的

\#2

同一幢楼5F的之前收养糯米（现在改名叫福宝了）的女邻居最近一周多要去美国出差，所以麻烦我每天去她家给福宝放猫粮铲屎。

不过也怪我没提前和媳妇儿讲清楚前因后果，让这事儿乍听听起来很奇怪，导致媳妇儿生闷气了好几天

事后分析了一下确实没把前因后果讲清楚，比如先找的物业管家但是不太合适 .etc，显得这个行为很 weird 🤔

所以这次帮完以后就不帮了，让她找其他楼的小姐姐去帮忙好了

\#3

因为上周生病了上吐下泻一周之跑了一天7km所以这周压力特别大

结果就是这周跑步跑吐血了，总共跑了4次，30.24KM，其中最多的一次13KM。

然而还是留下了22公里的压力给到了下周2月份最后5天…

\#4

这周游戏玩 high 了

虽然口头上说不喜欢 hades 这种 rouge-like 游戏，但是还是刷的太上头了 🤡

另外重新装上了 prototype2 之前的存档都还在，感谢 steam，把剩下的成就除了 hard difficult 和另外一个 fully upgrade 之外的都解了，后面可以抽时间 hard difficult 通关一次就可以白金了

\#5

这周把繁花看完了，电影也看的不少

- **蜗牛回忆录 Memoir of a Snail** 3/5 粘土动画这个方式有点意思 但是故事有点老套了内容上反而新意不多
- **杀手 The Killer** 3/5 这剧本真的捉急，确定不是AI瞎写的吗？浪费大卫芬奇和法鲨
- **繁花** 5/5 花有重开日，人无再少年。逼王拍人和情真的不一般，对演员调教也真的厉害，宝总的多情霸总一点都不油腻。不过最卡我的点是每次看到范总就经常想到我爸，92年我出生家里刚好这年开始做生意，没有宝总这种资本运营和人脉加持，只能走范总的路子一边请吃饭一边求人，折腾了二十多年才一点点攒到了8位数，接着没几年就到现在大环境眼看着就要俯冲向下。。。过去的日子是真的不会再回来了



## Work

\#1

**C++ Templates 2nd | 28. Debugging Techniques**

- 现在可以用 concepts 或者配合 static_assert 来 hard error fast
- 科普了一下 archetype 以及一些用法，比如来确保模板内部实现真的只用到他 claim 的那些 requirements
- impl trace 那个感觉实用性一般
- 另，这本书终于看完了…

**CppCon 2022 | Back to Basics: Declarations in C++ - Ben Saks** https://www.youtube.com/watch?v=IK4GhjmSC6w&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=88

- 基础教学，快速过一遍就行

**CppNow 2024 | Guide to Random Number Generation in C++ - Adrien Devresse** https://www.youtube.com/watch?v=rCuMjENEa4Q&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=37

- 演讲者口音有点重…一开始简单介绍了标准库的 PRNG，然后指出标准库 PRNG 的一些缺点
- 重点是推荐他们自己的一个 CBRNG 和 random123 算法的优势

**C++ Weekly - Ep 391 - Finally! C++23's std::views::enumerate** https://www.youtube.com/watch?v=HuRbLPRh-Nk

- 作用是返回一个 tuple，index + value，算是其它语言的常见功能了

**C++ Weekly Ep 467 - enum struct vs enum class** https://www.youtube.com/watch?v=1CjVTCiY4fc

- 除了 `enum class` 之外 `enum struct` 也可是可以的，并且没有什么明显的区别

**C++ Weekly - Ep 390 - constexpr + mutable ?!** https://www.youtube.com/watch?v=67DenIV45xY

- 这一期有点 orz
- constexpr class 因为 implicitly const 所以成员函数调用都 implict-constness 了，但是可以通过把 member 标记为 mutable 来实现 mutate state…

**Local Time** https://akrzemi1.wordpress.com/2022/04/24/local-time/

- 利用 C++ 20 chrono 提供的 time_zone 可以将 UTC time point 转换为对应的 local time point
- 虽然内容很简单但是文章主要科普的是 timezone 为什么复杂

**Changing std::sort at Google’s Scale and Beyond** https://danlark.org/2022/04/20/changing-stdsort-at-googles-scale-and-beyond/

- 这篇太吊了，不做基础优化的和我一样就看个乐子就行
- 先讲了过去这么多年来 stl 中 std::sort 的实现优化/发展历史，后面零散描写了不少一些优化方案，比如 branchless / double-buffer vectorization .etc
- 然后说了一下 std::sort 的一些坑，unstable，并且comparator 一定要满足四个原则

**Conformance Should Mean Something - fputc, and Freestanding** https://thephd.dev/conformance-should-mean-something-fputc-and-freestanding

- 严格来说和C++没关系
- C 的 fputc 输入是 UMAX_CHARBIT 有些 3rd party embedded standard impl 并不会理会标准，会只写入 8-bit 丢弃其他的部分，并且传统以来都是这样；作者觉得太离谱了然后一直在致力于 conformant to standard

\#2

我上周还是上上周说要恢复个人项目来着？还是当我没说过吧 🤷‍♂️

人生苦短，及时行乐，没准哪天就被裁了

---

好了这周就这样，下周见
