---
title: 一周杂记 in Week 4 June 2025
categories: CODE-LIFE
date: 2025-06-30 22:04:49
tags: [杂记]
---
本周（06/23 ~ 06/29）是6月份的最后一周，下周二就是7月份了，要进入暑假了。

## Life

\#1

这周一一早就收到了公司的官方邮件，官宣老张正式下课，变成IC，官方说法留了一个辅助技术架构，背后含义懂得都懂。

然后周二和临时接管的VP，CDO还有我们杭州这里的新 manager 开了一次 all hands，正式进入我们组的 2.0 时代。

既然老张没了，那么后面势必要在保证业务继续运行的前提下，做好灾后重建，架构上该改的要改，该重构的要重构。

只是我们这堆了5年半的屎山，超过200w行的业务代码，要 remake 谈何容易...这周只能先开个头，做一下模块梳理，然后靠一线 manager 把情况和上面几个头同步一下，争取资源能让后续做灾后重建。

不过夸奖一下 mermaid 的 flowchart 做模块依赖展示还挺好用的，用类似 markdown 的方式 delcare 一下各模块的依赖关系，自动出图。

这要是人肉画图肯定是画不出来的。

\#2

周三 WFH 然后中午开车去老婆家，下午 13:30 约了老婆家附近街道卫生院给屎总打疫苗。

屎总整个流程还是比较乖的，就扎针那几下哭的比较凶，后面安抚好了之后就没事了。

不过要吐槽一下这个北干街道社区医院，特么也太破了，电梯没有就算了，母婴室也烂的出奇，还没有泡奶的恒温水提供。

地面停车场也是烂的要死，一开始在大门外还以为是什么老年人棋牌社

\#3

周末表哥（小姑儿子）婚礼，老婆和娃肯定不好回去，所以就只有我回去了。

周六下午的高铁回去，周六晚上订婚宴；周日中午吃完结婚宴，周日下午高铁回杭州。

挺折腾的，而且感觉我没回去也没多大事情啊 🤷‍♂️ 白折腾一趟。

另外吐槽一下高铁二等座，以后再也不抱着侥幸心理买二等座了

\#4

这周在家看了 阿盖尔，呃。。。

- **阿盖尔：神秘特工 Argylle** 3/5 你这cast配AI写的剧本???亨利这发型是真的过分了

## Work

\#1

**Asynchronous Programming with C++ | 9. Asynchronous Programming
Using Boost.Asio**

- 感觉是最好的 ASIO 入门教材？可能因为已经默认不是网络IO/多线程菜鸟了，所以 ASIO 新手需要的各种点都覆盖到了
- 唯一不足就是还是缺乏一些深入，不过 ASIO 真要深入估计一本书都不够

**Asynchronous Programming with C++ | 10. Coroutines with Boost.Cobalt**

- 介绍了 cobalt 的一些功能
- 不过感觉有一些 asio 也已经初步提供在 experimental 了？不得不说 golang 那套设计确实影响不少借鉴者

**CppCon 2022 | Taking Static Type-Safety to the Next Level - Physical Units for Matrices - Daniel Withopf** https://www.youtube.com/watch?v=aF3samjRzD4&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=28

- 为物理单位设计一个单独类型，通过类型系统来约束运算行为
- 他们自己的口号叫 if it compiles it works

**CppCon 2022 | WebAssembly: Taking Your C++ and Going Places - Nipun Jindal & Pranay Kumar**  https://www.youtube.com/watch?v=ZS6OUzDFrE0&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=35

- emscripten + C++ 实现 WASM

**CppCon 2022 | Case For a Standardized Package Description Format for External C++ Libraries - Luis Caro Campos** https://www.youtube.com/watch?v=wJYxehofwwc&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=42

- 演讲者是 conan 的官方开发人员，这个 talk 的想法/愿景是搞一个标准的 package description format，对应的其实就是那个叫 CPS 的提案，这样各家 build system 和 package manager 都可以收敛，朝一个方向发展
- 主要还是目前现有的方案差别太大了

**C++ Weekly - Ep 327 - C++23's Multidimensional Subscript Operator Support** https://www.youtube.com/watch?v=g4aNGgLzVqw

- C++ 23 放松了 `[]` 内 `,` 的语义，于是可以引入 multidimensional subscrip operator 了

**C++ Weekly - Ep 326 - C++23's Deducing `this`** https://www.youtube.com/watch?v=5EGw4_NKZlY

- 复习 C++ 23 的 deducing this

**C++ Weekly - Ep 325 - Why vector of bool is Weird** https://www.youtube.com/watch?v=OP9IDIeicZE

- 复习一下 `vector<bool>` 这个坑，连 v.front() 返回的都不能是 refernce
- for-range loop 里要注意引用类型

**C++ Weekly - Ep 324 - C++20's Feature Test Macros** https://www.youtube.com/watch?v=4Bf8TmbibXw

- 从 20 开始终于提供 feature test macro 了，并且这个 macro 还支持 pre 20 的 features，就有点意思…

**C++ Weekly - Ep 323 - C++23's auto{} and auto()** https://www.youtube.com/watch?v=5zVQ50LEnuQ

- C++ 23 开始可以用 `auto{x}` 来创建一个 x 的 拷贝了
- talk 里面展示了一个 `std::erase()` 不正确的使用情况，拷贝值可以专门解决这种情况

**「全球C++及系统软件技术大会」Boolan首席咨询师吴咏炜：C++性能调优纵横谈（上）** https://www.bilibili.com/video/BV1Gr4y1b7vU/?vd_source=b1612988b780cebad3c7035af0de51bd

- 跑 benchmark 的时候一定要注意目标代码没有被编译器做了什么奇怪的优化，不然没意义
- linux 下标准库的时间戳性能还是很高的，精度也够；Windows下其实也类似，但是 Windows 整体的 resolution 要低于 Linux

**「全球C++及系统软件技术大会」Boolan首席咨询师吴咏炜：C++性能调优纵横谈（下）** https://www.bilibili.com/video/BV1WA4y1f7M6/?vd_source=b1612988b780cebad3c7035af0de51bd

- 下部的优化点主要集中在同步这块，还有浮点数运算，以及一些编译器自己能做的优化

**Copy-on-write with Deducing this** https://brevzin.github.io/c++/2022/09/23/copy-on-write/

- 作者也是 deducing this 的 proposal 之一，这篇文章核心是利用

    ```cpp
    struct X {
        int i;
        constexpr operator int() const { return i; }
        constexpr auto plus(this int x, int y) -> int { return x + y; }
    };

    static_assert(X{1}.plus(2) == 3);
    ```

    这个 trick 来简化 copy-on-write 的实现

- 不过文章也提到了， cow 需要尽可能优化内存分配开销，所以有时候代码写的简单性能可能会有问题

\#2

这周花了很多时间在研究学习 ASIO 官方的 cpp11 目录下的 examples

感觉下周差不多能看完

---

好了这周就这样，下周见
