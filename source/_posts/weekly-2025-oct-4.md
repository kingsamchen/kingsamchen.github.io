---
title: 一周杂记 in Week 4 Oct 2025
categories: CODE-LIFE
date: 2025-10-27 23:25:35
tags: [杂记]
---
本周（10/20 ~ 10/26）是十月第四周，这周杭州气温全面下降，给人一种一秒入冬的体验。

## Life

\#1

这周是秋宝在拱墅这边家里的完整的一周，全家人都在陪着秋宝磨合。

整体下来还行，我妈做事上虽然有些糙，反应也不够快，搞了好几次大问题把老婆都给气得不行，但是多次练习后进步明显，感觉整体上已经大差不差了

下周老婆恢复上班前的最后一周休假，所以差不多要把整个流程和做法都给固定下来了。

PS：秋宝自身适应的很好，能吃能睡，长势喜人，越来越可爱。就是好几天把大家折腾的不轻。

\#2

这个月又是一个Q的最终月，在我一番大力出奇迹下让老崔定了周五团建，而且还给 XDM 争取回来半天的休假。

吃饭啥的就不说了，那个东富酒家说是潮汕菜，但是做法简直一点都不潮汕。很多海鲜的烧法简直离谱

吃完之后大家去了附近的公园草地上打德扑，虽然老崔让 g1 g2 悄悄回去加班 🤷‍♂️

差不多四点多我就和suyang一起回家了，到家后扛不住直接眯了一会儿歇息，还是有点累的。

\#3

这周公司观影俱乐部活动，看的一战再战。

不过因为城西银泰的传奇奢华关门了（影院和银泰闹掰了），所以选了农贸市场的汇合影城，简直...算了，反正不是自己掏钱

- **一战再战 3/5** 预期太高了结果直接没接住。你要搞政治黑色幽默吧就好好搞，这表现手法跟老奶奶的假牙一样生硬且老套，西恩潘还是太老了年轻个十几二十岁演这个角色才没违和感，全剧上下只有德托罗的sensi是超出预期的

\#4

周日上午5KM跑步机+BC，2个小时不到刷了接近1K大卡热量

吃完中饭洗完澡就和媳妇儿开车去了乔司的 IKEA 给次卧买新床取代现在 90cm 宽的小床

花了三个小时终于敲定所有，还在IKEA吃了个晚饭。

不过去IKEA路上导航给我选了一条高速的路，开了3分钟高速花了7块钱高速费...

## Work

\#1

**CppNow 2025 | Undefined Behavior From the Compiler’s Perspective - Shachar Shemesh** https://www.youtube.com/watch?v=HHgyH3WNTok&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=22

- 简单介绍了一下 UB 然后展示了一些 real-world UB cases
- 结论就是有条件最好打开 ubsan

**C++ Weekly - Ep 246 - (+[](){})() What Does It Mean?** https://www.youtube.com/watch?v=fYd84StM5OI

- 核心 trick 是 `+` 可以把一个 captureless lambda 转换为对应的函数指针，所以后面那部分就是一个空 lambda…
- 还好这个 trick 我很早就知道了所以这一眼就看出来是一个 no-op

**C++ Weekly - Ep 247 - -O3, -Os, -Og, -Oz, Oh My!** https://www.youtube.com/watch?v=THE14sSDT6A

- Ofast 在 O3基础上开了 ffmast-math 这些可能会违反标准的优化
- Os 是面向减少体积优化
- Og 用了 O1 同时减少一些阻碍 debugging 会促进 inline 的优化选项
- Oz 是 clang 独有的，可以看作是 Os plus

**C++ Weekly - Ep 248 - Understand the C++17 PMR Standard Allocators and Track All the Things** https://www.youtube.com/watch?v=Zt0q3OEeuB0

- PMR allocators 的比较详细的介绍
- 哪天需要用到时候回来再看

**C++ Weekly - Ep 501 - Does C++26 Solve the constexpr Problem?** https://www.youtube.com/watch?v=x3Z-k34u3Q8

- C++ 26 会引入 std::define_static_string 和 std::define_static_array 来编译期做string/array的拼接等处理 proposal：https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2025/p3491r1.html
- 新方法可以直接取代 https://kingsamchen.github.io/2025/07/21/weekly-2025-july-3/ 中 C++ Weekly - Ep 313 的这个 trick

**C++ Weekly - Ep 502 - Simple Reflection For C++20** https://www.youtube.com/watch?v=voljWhjl0bA

- 利用的 trick 是 C++ 20 开始 structural binding 可以直接 bind aggregate types 的 members，搭配 if constexpr 人肉列出 member 数量就行
- 至于这个是数量怎么搞，这个 simple 实现的做法是需要人肉给每个 struct 定义一个 compile-time names array，size == num of members，同时还可以提供名字

**Improving my C++ time queue** https://schneide.blog/2022/11/10/improving-my-c-time-queue/

- 文章里着重要处理的是 timed out tasks 执行过程中会进行二次 insert 的问题，这就回到了经典的 insert while iterating 的情况
- 作者一开始的方案用了两个结构，一个临时的基于 vector 的 deferred queue，和一个基于 list 的 queue，每次 iterate 期间插入都会先进入 deferred queue，最后再统一转移到 queue
- 第二个方案直接换了个思路，timed out tasks 不就地处理，直接把这一批返回去给上层处理，这样就避开了
- 第二个方案其实是目前很多实现采用的方案

\#2

这周花了不少时间在 fawkes 上。

首先终于把 middlewares 相关的代码给 finalize 了，见 PR https://github.com/kingsamchen/fawkes/pull/4

处理这个 PR 的时候“意外”发现很早之前做的 clang-format-diff 的 pre-commit hook 居然漏掉了 `hpp` 文件，因为当时只 grep 了 `.cpp` 和 `.h`；还好搭建的 Github CI 检查出了这个问，所以这个 PR 里顺带把这个给 fix 了。

---

另外调整了一下 clang-format 的规则文件，最大的变化是 Continuous Indent 不再 double，看下来对于业务代码来说，整体观感确实会好一些。

以及把 fawkes 添加 route 中 user handler 和 middlewares 掉了个顺序，这样 lambda 放最后格式化看起来更“优雅”一些；另外就是考虑到 C++ 的习惯上，lambda 参数通常来说就是最后一个。

这部分见 PR https://github.com/kingsamchen/fawkes/pull/5

---

最后就是修了一下 windows/msvc 构建的一些小问题：

1. test_utils 是一个 `OBJECT` library，但是只有头文件，所以 MSVC 链接时候不知道这个 target 应该对应哪个语言。不过好奇这个居然只是 warning...
2. MSVC 对于形如
    ```cpp
    if constexpr (cond) {
        // do sth
        return;
    }

    // do another thing
    ```
   的代码会告警下面的 do another thing 是 unreachable code
   没办法，只能把下面部分用 else branch 包起来

这部分见 PR https://github.com/kingsamchen/fawkes/pull/6

\#3

fawkes 下一个要做的 task 目前决定是 https 的支持，并且大概有一个初步的想法（参考 boost/beast的 flex ssl sample）

不过在此之前，我打算休整一下 fawkes 的工程部份的一些老顽疾

一个比较直接的理由是考虑提早把 fawkes 给组里靠谱的同事先提前使用，好收集反馈以及可以接受贡献 XD

---

好了这周就这样，下周见
