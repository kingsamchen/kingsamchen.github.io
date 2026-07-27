---
title: 一周杂记 in Week 4 July 2026
categories: CODE-LIFE
date: 2026-07-27 22:12:24
tags: [杂记]
---
本周（7.20 ~ 7.26）是7月份第四周，这周在温州老家居家办公了4天。

## Life

\#1

这周在老家居家办公了四天，体验真不咋地。

老家书房和杭州的没法比，虽然空间比杭州大一倍，但是设备差太多，而且采光要么太强要么太暗；之前买的西昊的座椅在 herman miller 面前简直垃圾。。。除了办公桌可以升降之外简直没法打。

而且因为我是居家办公，所以下班的时候都六点多了，老家是村里的回迁房小区，外面这个点全都是各种烧烤摊小吃摊，而且天太热完全没法逛，空气也很差。

媳妇儿和娃还能早上和傍晚出去兜风，我想了想请假出去玩也不是很有意思，所以这段时间对我来说差不多就是坐牢。

虽然新房子没有美洲大蠊（大蟑螂），但是德国小蠊（小细长蟑螂）每次上厕所还能看到一两只，也是有点烦人。

所以买了车票，自己周五早上提前回来了。

周五起了个大早，6点50就挣扎着起床，吃个早饭，收拾一下，我妈就开车送我到车站。

7:50的高铁还能眯个四十分钟，然后8:30用手机开热点开始开会，开完也不困了，就只能继续干活了；期间点杯星巴克强行续命，没办法，周四晚睡得太晚，搞 ubi9 的 conan build 将近一点才睡着，拢共睡了不到6个小时。

10:40 到杭州东，和chao请假个1个小时，下站后直奔P2停车场。

因为之前把车停东站了，所以回去还是比较顺滑的，就是这个停车费一天封顶60，这几天花了312块。鲷哥说比起虹桥已经好很多了，还要什么自行车 🤣

\#2

虽然周五提前回来了，但是忘了提早在群里和教练还有 hogan 说，到了周六的时候教练把我们鸽了，说已经安排其他人的课了。

于是周六中午自己有氧+器械练了一把。

到了下午的时候，包子说周日上午又变成只有一个学员，所以可以上拳击课，但是 Hogan 已经和他姐约好去她姐家做客，所以这周还是得跳过。

于是我周日上午去上了一节体态 🤡

\#3

周日下午洗完澡出发去东站接媳妇儿和秋宝，结果快到东站的时候出了一个剐蹭的事故。

我在第二车道跟车，有个傻子为了抢最左左转道直接冲到我前面想加塞别人，我一个条件发射往右打了一把方向结果变道蹭到第三道直行的车。

我和对方示意不要堵在这里，开到前面路口商量，因为我一个体面人，知道这个肯定是我全责。

停车后我和对方检查了一下，拍完照之后互加微信，商定好都先接人再报警，完了之后一起来处理。

对面年纪和我相仿，开的车恰巧也一样，沟通起来也比较方便。

于是把媳妇儿和娃送回家之后我联系了对方，一起出发到事故附近的交通岗亭让交警出事故认定，来走保险。

后续就很顺利了，还是有素质的人处理事情效率快很多，总共加起来不到半小时吧，还没路上开车时间多。

到家后联系保险，发了一些照片，剩下的就保险去处理了。

\#4

周五择日不如撞日，和 iker 约了当晚 21:30 的 obsession，开车去影院花了20分钟，开场前十五分钟到了影院。

而且这周终于开始看 The Daredevil Born Again S2 了

- **痴迷 Obsession** 3.5/5 没预想的好 惊悚主要来自于精神病似的疯批行为而非基本的精巧 主题呈现还行
- **Daredevil: Born Again Season 2** 先看着先，暂无评分

## Work

\#1

**CppCon 2023 | Taro: Task Graph-Based Asynchronous Programming Using C++ Coroutine – Dian-Lun Lin** https://www.youtube.com/watch?v=UCejPLSCaoI&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=46

- 传统的 task flow framework，例如 taskflow，在某个 task 里如果出现子操作的同步阻塞则会直接卡住执行当前 task 的 executor：

    ```jsx
    auto C = taskflow.emplace([] {
        launch_gpu_kernel();

        wait_for_gpu();  // blocks this Taskflow worker

        consume_result();
    });
    ```

- Taro 的做法是，每个 task 都可以是一个 coroutine，这样当 `wait_for_gpu()` 操作需要等待时，就直接 suspend 自己，让 executor 切换去执行其他任务
- 实际上 taskflow 也可以通过把阻塞操作给隔离出来单独做成 task 的方式来解决，但是这样会让 task 变得更加细碎，难以理解

**CppCon 2023 | A Long Journey of Changing std::sort Implementation at Scale - Danila Kutenin** https://www.youtube.com/watch?v=cMRyQkrjEeI&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=45

- google 利用基于 bits trick 的 block-quicksort 和 tuckey’s ninther 看看就行，但是关于使用 sort 的一些常见错误倒是非常非常值得思考，例如浮点数排序比较的坑，comparator 必须满足 strict weak ordering .etc
- 几个错误例子真的非常值得研究思考，例如 web_rtc 里面那个

**CppCon 2023 | Lifetime Safety in C++: Past, Present and Future - Gabor Horvath** https://www.youtube.com/watch?v=PTdy65m_gRE&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=51

- 先讲一些常见的 memory issues，然后开始讲这几年各家编译器/标准在 getting safer and safer 这件事上的努力
- 编译器的各种针对 lifetime 的告警，以及 annotations 辅助分析
- 整体听起来比较有 promising，感觉是比 circle 那个照抄 rust 的方案看着靠谱

**CppCon 2023 | The Au C++ Units Library: Handling Physical Units Safely, Quickly, & Broadly - Chip Hogg** https://www.youtube.com/watch?v=o0ck5eqpOLc&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=50

- Aurora 家是做无人驾驶的，au 是他们推出的单位库，号称应该是目前综合最好
- https://github.com/aurora-opensource/au

**CppCon 2023 | Lightning Talk: Higher-Order Template Metaprogramming with C++23 - Ed Catmur**  https://www.youtube.com/watch?v=KENynEQoqCo&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=53

- concept 简化了不少类型约束表达，如果一次 concept 不够，可以套一个 lambda 接着放 concept 🤔

**C++ Weekly - Ep 542 - C++26's inplace_vector** https://www.youtube.com/watch?v=dD6PhTJLz08

- inplace_vecotor 本质就是 fixed capacity vector on stack
- boost 里可以提前尝鲜使用

**C++ Weekly - Ep 114 - cpp_starter_project GUI Additions** https://www.youtube.com/watch?v=mMvTXcxkPcA

- 过了一下这个基础入手项目里配的几个比较容易入手的 GUI frameworks

**C++ Weekly - Ep 113 - Will It C++? Atari Touch Me From 1978** https://www.youtube.com/watch?v=SbS_RP5fGSE

- 省流：llvm 对 PIC16 支持不好，没有真的跑起来

**C++ Weekly - Ep 112 - GCC's Leaky Abstractions** https://www.youtube.com/watch?v=S9_mYmvO4Ow

- gcc 的编错误提示直接把一些生成的隐藏变量给暴露出来了
- 而且老版本的 gcc 的 lambda value captures 外部还能直接赋值。。。。

**C++ Weekly - Ep 111 - kcov** https://www.youtube.com/watch?v=NRCnS5alouI

- commandline code coverage tool https://github.com/SimonKagstrom/kcov

**C++ Weekly - Ep 110 - gdbgui** https://www.youtube.com/watch?v=em842geJhfk

- 介绍的 gdbgui 这个辅助

**C++ Weekly - Ep 109 - When noexcept Really Matters** https://www.youtube.com/watch?v=AG_63_edgUg

- 省流：`vector<T>` 扩容时只有当 `T::T(T&&) noexcept` 时才会用 std::move，否则会用 copy

\#2

这周优化了一下 scheduler 的 sanitizer flow，`preset=ci` 不再绑定 sanitizer，而是引入 `preset=ci-san`，原因是老 jenkins 保持原来的快速构建和测试验证，单独再引入一个跑 sanitizer 的 jenkins，每天半夜跑一次，看看有没有 memory issue 和 leak。

\#3

这周自己起了一个 ubi-9 的 docker container，镜像用的还是公司提供的，主要是为了本地验证一下现有的 conan packages 的构建。

跑下来除了一个公司内部的 sdk 因为用了 `uint32_t` 没有包 `<cstdint>` 而是依赖间接包含导致在 gcc-15 上挂了，需要单独出一个 patch，其他的都比较丝滑。

不过 build team 的哥们直到周五下班都没给我弄好 build job...

下周就得正式开始 alma9 vagrant 的迁移了，先把 codebase 的构建搞通了才能搞部署的 ubi-9 镜像。

---

这周就这样，下周见
