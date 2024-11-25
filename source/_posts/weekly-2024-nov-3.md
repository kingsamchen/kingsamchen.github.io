---
title: 一周杂记 in Week 3 Nov 2024
categories: CODE-LIFE
date: 2024-11-24 23:51:19
tags: [杂记]
---
本周 (11/18 ~ 11/24) 是11月的第三周，天气开始转凉了。

## Life

\#1

周二上午去媳妇儿医院复查了一下血常规指标，以及查了一下自己血型，说是担心可能和媳妇儿血型冲突，胎儿有溶血风险。

反正我不是很懂，媳妇儿要求的就照做就是。

检查完之后和媳妇儿吃了一顿金华砂锅的大筒骨，贼过瘾，消费还能抵扣停车。

医院回来之后顺带去家对面的麦道夫洗了一下车，好几个月没洗了车有点脏，而且到冬天了胎压都降到240kp了，刚好顺带补充一下。

这里吐槽一下极氪官方商店，我下单车载充气泵都一周了还没给我发货 🤬

\#2

周五带大蛋小蛋出院了，小蛋伤口和肠胃都好了，大蛋伤口好了，但还是软便，不过医生说吃了这么几天的益生菌都还这样可能真的是因为吃太多了消化导致的，有点无解

但是只要自身还健康的话可以不用太担心，暂时先每日观察好了。

另外因为没找到领养就回他们“老家”住了，周围好心人会定时过来投喂；而且因为就在我家小区边上，所以我也会每1-2天去看他们一下。

另外下周周末就要带去打猫三联第一针了 😊

\#3

周六下午开车带同事去西溪博纳看了哈利波特与死亡圣器上，晚上去接了媳妇儿查房回家，去媳妇儿医院路上堵车快给我堵的心态爆炸；下周得安排一下 角斗士2 和 死亡圣器下

本周新增观影：

- **角斗士 Gladiator** 5/5 老雷以前拍史诗片真的是有buff加成，director cut 接近三个小时的电影叙事节奏把握的特别好，克劳和凤凰叔的表演无可挑剔，整体质感二十年后看一点都不掉价。
- **铁血战士 The Predator** 2/5 甚至比 2010 那版还要挫...完全失去风格的一部。女主要是换成 Elodie Yung 我还能给个三星

## Work

\#1

**C++ Templates 2nd | 21. Templates and Inheritance**

- EBO 优化以及被 unique_ptr 拿来做 deleter 优化的 compressed pair idiom
- CRTP 以及如何和 Barton-Neckman Trick 结合来实现一些“奇怪的”facade
- Mixins 的手法可以学习一下；以及最后 named template argument 的实现也太 tricky 了

**CppCon 2022 | Optimizing A String Class for Computer Graphics in Cpp - Zander Majercik, Morgan McGuire** https://www.youtube.com/watch?v=fglXeSWGVDc&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=62

- 这篇比较短，讲的是 Nv 内部一个研究组搞了一个专门针对 Graphics/Game 场景的字符串，SIMDString，可以指定内部栈区大小，同时专门针对静态资源区字符串的优化；但是 talk 里没有讲 SIMD 使用，有点神奇
- 另外 benchmark 上，大字符串，~2MB 和标准库基本没有啥优势，32 bytes 大小对比标准库有优势，不过我猜是因为这个大小标准库字符串都脱离 SSO 了
- 还横向对比了 eastl/string 和 folly/string，结果上看，STL/string 还是非常能打得

**CppNow 2024 | Boost.Parser (Part 1 of 2) - A Parser Combinator Library for C++ - Zach Laine** https://www.youtube.com/watch?v=lnsAi_bWNpI&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=18

**CppNow 2024 | Boost.Parser (Part 2 of 2) - A Parser Combinator Library for C++ - Zach Laine** https://www.youtube.com/watch?v=00Xd7HpRtUM&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=19

- boost.parser 作者粗讲他的 library
- 巨长无比

**C++ Weekly - Ep 437 - Pointers To Overloaded Functions** https://www.youtube.com/watch?v=NMWv2vQQjXE

- 常规方案，cast 成具体的函数指针类型
- 比较优雅的方案，用 lambda 包一层；lambda 因为本质是个 function object，所以可以应对这种

**C++ Weekly - Ep 436 - Transforming Lambda Captures** https://www.youtube.com/watch?v=t6hFPKiOS-Q&t=10s

- C++ 20 开始 lambda capture 支持创建 packing 了，有点意思

    ```cpp
    template<typename... String>
    void foobar(const String...& str) {
    	auto l = [...strlike = std::string_view{str}] {
    	  // use with `strlike` pack
    	};
    }
    ```


**C++ Weekly - Ep 435 - Easy GPU Programming With AdaptiveCpp (68x Faster!)** https://www.youtube.com/watch?v=ImM7f5IQOaw

- AdaptiveCpp 的广告，说是能轻松无痛 offloading to gpu while increasing throughput

**C++ Weekly - Ep 434 - GCC's Amazing NEW (2024) -Wnrvo** https://www.youtube.com/watch?v=PTCFddZfnXc

- 目前只有 gcc trunk 可用处于实验中，不过有点意思，针对不能启用 NRVO 的地方会进行告警
- 要不搞成 tidy 这种可以针对某些上下文屏蔽/启用的吧，感觉适用范围会更广一些

**[MUC++] Ivica Bogosavljevic - The performance price of virtual functions in C++** https://www.youtube.com/watch?v=DYnyN5aQKyQ

- 单就 virtual call 而言，对于 small/short 函数的影响比较明显， ~ 18% slow down，但是对于比较复杂的大函数，overhead 几乎是 negligble
- 另外一个显著影响性能的点是 virtual call 使用场景对象几乎都是 dynamically allocated，访问会影响 data cache；案例是如果将非常多的 base pointer 存放在 vector 里，相同实际类型的放在一起和完全乱序最多能有 7x 的性能差距
- virtual call 会 prohibit inlining，更糟糕的是会阻碍使用 inline 之后的优化；这里我想了一下大多数使用 virtual call 的场景，目标函数基本都是黑箱了，所以编译器几乎没法看到被调用函数的完整上下文，所以这是比 inline 更严重影响优化的点
- 另外就是对 instruction cache 的影响，和前面 data cache 类似，如果 vector 包含了很多指向不同派生类的基类指针，有序和乱序甚至 round-robin 对 instruction cache 会有显著的影响

**Optimization without Inlining** https://artificial-mind.net/blog/2021/10/17/optimize-without-inline

- 函数 inline 很重要，但是就算函数不被 inline，编译器能看到完整实现更重要，因为这能让编译器有更多的上下文，可以更好的优化代码

**Converting binary floating-point numbers to integers** https://lemire.me/blog/2021/10/21/converting-binary-floating-point-numbers-to-integers/

- 直接用浮点指令和手写 ieee 754 处理 mantissa 哪个转换浮点到整型更快…
- Arm 上好像指令确实不如手写快，X86-64 指令更快

**Optimizations enabled by -ffast-math** https://kristerw.github.io/2021/10/19/fast-math/

- 开启 `-ffast-math`  能够打开一系列 floating point 优化开关，优化编译

**Easy Way To Make Your Interface Expressive** https://m-peko.github.io/craft-cpp/posts/easy-way-to-make-your-interface-expressive/

- 算是一个老问题了：多个 boolean 参数的时候怎么避免用错，但是方案我看着就像硬凹一个出来的，讲道理真不好用

\#2

这周终于把之前学习材料里的 coroutine-based awaitable eventfd 给写完了 https://github.com/kingsamchen/Eureka/pull/29

写的过程中复习了一下 epoll 和 eventfd，回头可以总结一下。

然后开始有点明白之前看 Raymond Chen 写的 coroutine 系列中为啥那些 sync primitives 要写的这么鬼畜了，因为这次写的简化版只能作为一个 demo/poc，在生产环境压根没法用。

比如很难处理 suspend/resume 状态切换的时候是有 race 的，并且做不到多个上下文并发同时 co_await 这个 awaitable event。

如果要考虑到 performance 就更难了。

所以还是建议直接用现成的成熟的 library，可以看看他们的源码权当学习和熟悉。

有鉴于此，打算后面不再看这部分学习材料了，因为 demo 和 industry-grade 实现差距太大了。

后面重心应该会切换到使用 asio/beast 写点东西，因为不排除未来组里的项目有机会可以切换成这个（美好的愿景？），有时间可能就直接看 asio 的实现了，毕竟他家的实现是真的稳。

---

好了这周就这样，下周见
