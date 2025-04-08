---
title: 一周杂记 in Week 1 Apr 2025
categories: CODE-LIFE
date: 2025-04-07 00:47:15
tags: [杂记]
---
本周（03/31 ~ 04/6）是四月份第一周，气温又明显回升，开始有夏天的感觉了。

## Life

\#1

这周不仅把 Hades 的全成就解锁了，还在我“锲而不舍”的研究一下，终于也打过了一次 32-heat，Skelly 的三座雕像都齐了。这回算是名正言顺的全成就了。

![](img/hades-1.jpg)
![](img/hades-2.jpg)
![](img/hades-3.jpg)

\#2

周五第一天假期带着媳妇儿做了 routine 产检，中间等生化血结果的时候开车去了附近西溪西边的一个古建筑群：洪园 转了一下。

感觉这个经典不错，建筑很有味道，而且人也不是很多。

往里走有片湖的地方微风吹过还非常舒服。

感觉下次可以试一下付费区 🤡

\#3

周六很有兴致并且很期待的开车去了媳妇儿提议的 江南驿 吃中饭。

但是实话说感觉是一般的本帮菜，而且份量有点偏大。

然后媳妇儿也觉得一般，所以以后估计不会再来了 🤷‍♂️

周日中午吃完饭之后小憩片刻开车去了浙江省博物馆的之江馆区。

然而整体感觉也挺一般的，逛下来只有浙江史前文明部分有点意思；博物馆这种地方，感觉北京对其它地方真的是降维打击，尤其是国博。

而且回来路上紫之隧道还堵了7-8公里 🫠

\#4

周六吃完饭回来小区陪媳妇儿闲逛居然时隔近一个月再次在草丛里看到乐乐。

这娃还记得我，跟我打招呼，但是估计还记得上次被我抓住去打疫苗了，所以这次比较警惕，保持了一定距离 🤡

不过看起来感觉可能最近有点瘦了？媳妇儿说是天热了换毛了所以看起来瘦了 🤔

\#5

本周观影

- **狮子王 The Lion King** (2019) 3/5 年纪大了再回过头来看这个剧本就觉得好平庸，而且这CG是很逼真，问题就是太逼真了导致情绪的表情都很奇怪
- **盟军敢死队 The Ministry of Ungentlemanly Warfare** 3/5 非常不盖里奇的主旋律版无耻混蛋。但是亨利是真他妈帅啊

## Work

\#1

**CppCon 2022 | Binary Object Serialization with Data Structure Traversal & Reconstruction in Cpp - Chris Ryan** https://www.youtube.com/watch?v=rt-c7igYkFw&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=102

- 介绍的这套方案感觉有点 old-fashioned…

**CppNow 2024 | The Most Important API Design Guideline - No, It's Not That One - Jody Hagins**

https://www.youtube.com/watch?v=xzIeQWLDSu4&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=48

- 这一期几乎是闲聊为主，演讲者确实有点牛逼，还拿了高德纳老爷子的支票
- 核心就是 API 的设计上 testability 最重要，然后发散展开各种相关的事件
- 最后讲了一下他认为的 OOP，其实就是 Alan Kay 说的 message passing

**CppNow 2024 | Security in C++ - Hardening Techniques From the Trenches - Louis Dionne** https://www.youtube.com/watch?v=t7EJTO0-reg&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=49

- 过了一遍各种 classic memory safety issues
- UB is notable for memory safety issues and especially for UB in standard library.
- library hardening 是希望直接随产品一起发布的，目前提供了 none/fast/extensive/debug 四种级别，并且这个是 translation unit 粒度的，这个对于某些 performance critical 的代码可以直接 TU 上关闭，而且不会导致任何 ABI issue
- 提了一嘴 contract proposal
- Temporal memory allocation，概括一下就是系统提供每个类型的单独分配器，避免 aliasing 和 memory reuse；不能解决 memory safe related bugs，但是可以增加 exploit 的难度；MacOS 的 darwin kernel 已经做了这部分支持

**C++ Weekly - Ep 473 - continue and break Statements** https://www.youtube.com/watch?v=CiB1ex0hi3w

- 核心 continue/break 出现的场合很多时候考虑一下是不是可以用 stl algorithms 那套替代，可读性更好

**C++ Weekly - Ep 370 - Do Constructors Exist?** https://www.youtube.com/watch?v=afDB4kpYnzY

- 核心点就是：单参构造下，构造函数更像是 conversion operator
- 感觉其实没啥内容。。。

**C++ Weekly - Ep 369 - llvm-mos: Bringing C++23 To Your Favorite 80's Computers** https://www.youtube.com/watch?v=R30EQGjxoAc

- 算是一个玩具性番外，这套 port 可以在 commodore64 上跑有限的 C++23 代码
- 不过以为只有64KB存储所以标准容器字符串这些 port 都是没有的，但是 constexpr 这类 core features 还是保留了

**C++ Weekly - Ep 368 - The Power of template-template Parameters: A Basic Guide** https://www.youtube.com/watch?v=s6Cub7EFLXo

- template template parameter 的基本用法

**C++ Weekly - Ep 367 - Forgotten C++: std::valarray** https://www.youtube.com/watch?v=hxcrOwfPhkE

- valarray 的主要作用就是数值计算的时候可以直接把 valarray 当作整个矩阵对象处理吧，而且编译器会尽量 vectorization
- 缺点就是其他场合意义不大而且容易用错

**C++23: Consteval if to make compile time programming easier** https://www.sandordargo.com/blog/2022/06/01/cpp23-if-consteval

- C++23 引入的 consteval if 能让整个 branch block 变成 compile-time evaluated
- 顺带知道了 constexpr 函数里就算用 std::is_constant_evaluated 也没法再对应的 branch 调用 consteval，估计是语义上的不一致，这个被算作 defect 了
- 不过有意思的是，查了 cppreference 发现 std::is_constant_evaluated 的实现理应通过 consteval if 来完成，但是后者进入标准更晚，所以之前都是编译器开洞

**C++20’s parenthesized aggregate initialization has some downsides** https://quuxplusone.github.io/blog/2022/06/03/aggregate-parens-init-considered-kinda-bad/

- 作者反对 C++ 20 的 parenthesized aggregate initialization 这个特性（已经正式 standardized了），理由是会把之前 `{}/()` 的混乱进一步加深
- 简单讲一下就是，C++ 20 允许用 `()` 初始化 aggregates

    ```cpp
    struct Coord { int x, y; }; // aggregate
    Coord c(5, 10); // ok since C++ 20 but failed before
    ```

- 另外我看了这个 post 才意识过来，多参构造可以通过 `{}` 来构造隐式转换，即

    ```cpp
    struct BadGrid {
        /*explicit*/ BadGrid(int width, int height) {}
    };

    BadGrid g1 = {10, 20};
    ```

    不过至于多参要不要加 explicit 看人，我倾向于不加，除非是用 passkey idiom 的时候（这个时候应该能反应过来为什么 passkey idiom 要特意加 explicit 了）


**Potential issue with C++20's initialization change** https://gist.github.com/s9w/ad9b1dd1ea6fb17e956559c8b352e246

- 吐槽的上面那篇其实是一个东西 🤡

\#2

这周发现升级 CMake v4 之后，doctest 没法直接构建了。因为 doctest 的 cmake_minimum_version 版本低于 3.5。

对于适用 cpm.cmake 这种源码管理方式，可以直接指定 CMAKE_POLICY_VERSION_MINIMUM 为 3.5 或者更高来 workaround，比如 https://github.com/kingsamchen/esl/commit/c67a14c7facc356376e4c6b4d8301b9337254bd7

但是如果用的 vcpkg 或者其他包管理，就只能靠包管理去fix了。

doctest 不升级 cmake_minimum_version 的理由是担心破坏 downstream users...

等了几天发现 vcpkg team 自己提了一个 PR 指定了 policy version，这样就不用等着三方库作者一个一个修了

\#3

继续抽了点时间看了一下 http-router 的代码。

这回轮到多个 route path 的处理了，希望最近有完整时间可以研究一下这块。

---

好了这周就这样，下周见
