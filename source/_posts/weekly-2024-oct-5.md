---
title: 一周杂记 in Week 5 Oct 2024
categories: CODE-LIFE
date: 2024-11-04 00:17:19
tags: [杂记]
---
本周（10/28 ~ 11/03）是十月份的最后一周，周末已经是11月份了。

## Life

\#1

我的右手无名指关节部分饱受一个奇怪的痛觉已经好几个月了，所以这周终于下定决心，请了半天假开车跑到媳妇儿医院，让媳妇儿给我推荐一个靠谱点的医生看看。

结果医生稍微问了几个问题就说不严重啊，不需要拍片的，关节部分避免受伤，多休息，有必要上热敷就行；实在疼就上点止痛药...

好吧，那我还能说啥；整体反馈和我媳妇儿之前预期的大差不差，反正回家路上又给她一顿阴阳 😒

无独有偶，周六感觉左膝盖有点刺痛，所以也没有去跑步。

到了第二天周日感觉疼痛感还在，并没有实质缓解，所以为了安全起见，周日上午的团课需要练腿的部分就基本划水过去了；毕竟我现在最怕的就是出啥事 🤣

\#2

媳妇儿医院搞了一个啥学习班，有学分（医生好像每年都要修一定的学分，卫计委要求的），然后她又被要求去做为工作人员出席，而更蛋疼的是地点在九里松的园区。

没办法，周六只能给她当司机了。

为了避免出现意外，提前申请了西湖通，周六一大早七点多就开车往景区走。早上的话景区交通还是通常的，基本二十分钟就到了。

回家也算比较畅通，除了返程这段路实在有点七拐八拐的难开。

蛋疼的点在五点半结束之后去接她，那个时候不管哪条路线都是猪肝色的...

我趁着灵溪隧道还没有太堵之前赶到了医院，然后等了一会儿媳妇儿吃完饭就接上回家了，然后二十分钟路程开了四十多分钟才到家...

\#3

趁着这周双十一开始，电子配件价格还行，赶紧把配件都买回来准备搞个新电脑咯。

现在有钱了，基本啥都往最好的一档选，除了显卡官方店实在买不到 4090/D，只选了一个 4080S。

下单后算了一下总价，差不多 29656；这台机器感觉可以战未来十年！

中间还有个插曲，twitter 上遇到一个似乎很懂硬件配置的人，我顺带写了一下我的配置单，他说 DDR5 现在是没法插满4条还开 xmp/expo 的

然后我身边的同事/同学（马总和O哥）居然都不知道这个事情...

于是我花了点钱买了上门装机服务，先咨询了一下对面师傅；他说建议别搞四条，他装了这么多机器就没能开 xmp/expo 的

于是我赶在最后一刻把内存方案从 Acer Predator 32G x 4 换成了 gskill 48g x 2，后者内存颗粒还只是海力士 m-die，不过这也是我能选到最好的 48g 单条了...

等下周电脑装完可以感受一下新设备带来的爽快感，😄

\#4

这周看了三部电影，其中《蓦然回首》是周三和电影俱乐部的同事一起去电影院看的，电影票说是直接走了俱乐部经费 😊

- 电影 **蓦然回首 ルックバック** 3/5 75分钟大陆版多出来的部分是动画staff的采访...整体故事还可以，但是同类型的看得太多了所以没有太直击灵魂的感触
- 电影 **铁血战士2 Predator 2** 3/5 中规中矩的爆米花，没有啥特别出彩的地方，甚至连 predator 的刻画都挺直的
- 电影 **异形大战铁血战士 AVP: Alien vs. Predator** 4/5 杂糅型类型片范本了吧，整体叙事和节奏都意外在线，看了一下导演居然是保罗安德森，那难怪了，连瞎搞设定都是一贯风格

## Work

\#1

**CppCon 2022 | Implementing Understandable World Class Hash Tables in C++ - Eduardo Madrid, Scott Bruce** https://www.youtube.com/watch?v=IMnbytvHCjM&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=55

- 作者二人用 SIMD 和一些 micro-optimization 来优化 robin-hood hash table 的故事，总结下来他们的优化点有
    - SIMD(SWAR) for batch comparison
    - 引入一个 PSL 来表明 home-index / actual-index 距离，并且操作都和这个 PSL 有关
- 对数据结构/算法优化有兴趣的可以看一下 inner working details，像我这种人估计就关心 benchmarks 以及这库是不是开放使用 🤣

**CppCon 2022 | Back to Basics: Object-Oriented Programming in C++ - Amir Kirsh** https://www.youtube.com/watch?v=_go74QpFPAw&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=56

- OOP + 一些常用 design patterns 讲解

**CppNow 2024 | Fast Conversion From Cpp Floating Point Numbers - Cassio Neri** https://www.youtube.com/watch?v=w0WrRdW7eqg&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=12

- 这个 talk 干货不少啊，先介绍了 ieee 754 的模型，这个阐释确实容易懂很多，让我一下理解了之前公式 mantissa 部分诡异的首位进位
- 然后介绍了 floating → str 这么多年的算法演进，从一开始的 dragon 到现在用的最多的 dragonbox 和 ryu
- 最后是作者自己实现的算法版本（作者用的某个南美土著语言中的龙来命名，字打不来…）；不过目前没有公开实现

**CppNow 2024 | C++26 Preview - Jeffrey Garland** https://www.youtube.com/watch?v=CwYILWyTRMQ&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=22

- 26 比较期待的特性大概是 contracts / compile-time reflection / execution model
- 其他的都是小修小补

**C++ Weekly - Ep 452 - The Confusing Way Moves Can Be Broken in C++** https://www.youtube.com/watch?v=ZuTJAP4oMwg

- 这一期非常生动形象的阐释了要么 rule of 0，要么 rule of 5
- 定义了析构函数之后如果没有定义移动构造，那么编译器会 supress 移动构造，并且用拷贝构造来代替可能的移动场景；这个行为类似定义了拷贝构造但是没有定义移动构造
- 还挺坑的

**C++ Weekly - Ep 443 - Stupid constexpr Tricks** https://www.youtube.com/watch?v=HNn-PmrL5X8

- 一个老的 convention：const integral 用 literal/constant 初始化之后是类似 constexpr 这种 compile-time constant
- If a lambda is qualified to be constexpr, it will be constexpr, since C++ 17

**C++ Weekly - Ep 441 - What is Multiple Dispatch (and how does it apply to C++?)** https://www.youtube.com/watch?v=l2VemFmfkG4

- 本质就是讲了一下 variant 的 visitor 吧…

**Binary Banshees and Digital Demons** https://thephd.dev/binary-banshees-digital-demons-abi-c-c++-help-me-god-please

- C++ ABI 吐槽， TL;DR 行文真的超长
- 开头先说 ABI break 是没法避免的事情，而且 C 的 ABI break 更加容易出现，然后细数了为了不出现 ABI break 委员会做的一些傻事情；std::regex 被反复拿出来鞭尸，MS和MSVC也被反复拿来吐槽…

**std::optional and non-POD C++ types** https://felipe.rs/2021/09/19/std-optional-and-non-pod-types-in-cpp/

- 这篇的内容要谨慎看待，作者遇到的问题其实就是当 RVO 不能运作时返回 std::optional<T> 会多一次 T 的移动构造。然而这个我并不觉得是 std::optional 的问题
- 另外作者比较在意 inline 导致的 binary bloat，这个具体有多少 bloat 其实也是要因人因项目而异的
- 作者的 solution 就是有些场合用 output parameter…

**C++ Return: std::any, std::optional, or std::variant?** https://www.cppstories.com/2021/sphero-cpp-return/

- 作者想搞一个 Result/<T> 来表示要么有返回类型，要么失败了结果为空，然后基于题目说到的三个组件来构建
- 一开始想用 any 是因为可以避免标注模板类型，后来发现这个限制去不掉，那就索性回到 optional 和 variant 好了，最后选择是基于 optional
- 讲道理我也没看出除了改了一点点接口语义之外和 optional 哪里有区别；不过实话说 error reporting 的话用 optional 并不好，因为丢失了错误信息
- 既然不愿意用 exception 的话（作者解释了原因）那就等 expected 呗，或者 boost 有类似的

**When Is An Antipattern Not An Antipattern?** https://m-peko.github.io/craft-cpp/posts/when-is-an-antipattern-not-an-antipattern/

- 提了两个”曾经“需要 `return std::move(obj)` 来处理的场景
    - 一个是参数是 `T&&`，然后返回 `T`，老编译器不太能处理这种场景，所以需要手动 move 否则就变成 copy 了
    - 另外一个是 Derived 继承自 Base，但是没有新增成员，返回类型是 Base，返回 Derived；因为没有新增成员，所以没有 sliced；而且因为类型不一致，所以不满足 guaranteed copy elision
- 上面这两个问题，gcc-11 和对应的 clang 版本都已经 fix 了

\#2

开始拿 https://luncliff.github.io/coroutine/articles/awaitable-event/#implementation 练手

不仅可以回忆一下手写 epoll 的使用流程，还能加深对 coroutine 的理解，感觉一举两得呢 😂

---

好了这周就这样，下周见
