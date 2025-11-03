---
title: 一周杂记 in Week 5 Oct 2025
categories: CODE-LIFE
date: 2025-11-03 20:58:36
tags: [杂记]
---
本周（10/27 ~ 11/02）是十月最后一周，气温已经比较稳定的进入秋冬季节。

## Life

\#1

周四凌晨不到四点的时候娃闹醒然后喂了一次奶，哄睡后因为我妈抱回床上时走路不稳差点把娃摔了导致媳妇儿非常生气地指责我妈然后把我也吵醒了。

情绪上头的时候是真的没法冷静下来思考的，闹了半天我也受不了了和两边都吵了起来。

吵了半个多小时最后大家又不欢而散继续回去睡觉，但是我被闹了一顿之后真的完全睡不着，隔了不到一个小时又起来吃了一片褪黑素，最后挣扎了不知道多久终于睡着。

然后8点半又爬起来赶去公司开会，一天都是昏沉的不行，中午吃完饭补觉了半小时稍微好了一些。

本来还在考虑事情还没做完周五要不要休假，结果这么一闹，还是休假补觉吧。。。

还好周五之后大家冷静下来之后又稍微缓和了一下。

到了周末重新坐下来复盘了一下，最终算是 move on 了。

\#2

上周末买的IKEA的床周四送到了，周五师傅也上门完成了安装。

然后花了点时间把旧床拆卸之后放到小区的大件垃圾回收点，这个次卧的床就算升级改造完成了。

别的不说新床确实舒服了不少，1m2的宽度至少睡觉体验时非常明显的提升，之前0.9m实在太小了。

而且新床垫选的弹簧，偏硬一些，对我这把年纪也有好处。

\#3

周六闲的蛋疼把 Windows 25H2 更新了之后又把 ASUS X870E 主板的 BIOS 刷到了最新版本。

夸一下 ASUS EZ flash，刷 BIOS 确实简单了好多，虽然距离上次刷过了快一年已经忘了具体步骤，但是随便找一下官方指南很快就上手了。

并且刷玩之后切换到 DOCP II 超频方案发现好像稳定了不少，于是搞了 MemTest 打算做一次完整测试。

比较傻逼的时候这个测试是晚上九点半开始做的，而96G内存一轮下来要一个多小时，总共要跑四轮。

第三轮跑完没有错误的时候我实在扛不住了，于是把电脑留着继续跑测试自己睡觉去了。

还好第二天起来发现 4 pass 都是成功的，所以这个超频方案就先这么用着吧；看看后面会不会出现睡眠恢复后掉盘的情况毕竟内存频率直接从 4800 升到了 6400，简直美滋滋。

\#4

周三电影俱乐部和 iker 一起看了 炸药屋

- **炸药屋 3/5** 这种叙事风格搭配这个结尾就算不叫烂尾实际上也差不多了，就算再不想“引导”观众什么至少放个终结者那种爆炸镜头进去也行啊


## Work

\#1

**CppCon 2023 | Robots Are After Your Job: Exploring Generative AI for C++ - Andrei Alexandrescu** https://www.youtube.com/watch?v=J48YTbdJNNc&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=8

- Andrei 示范如何借用/利用 GPT 来思考并探讨问题；Andrei 设计了一个fine-tune 的 lower_bound，自然文本环境下性能比标准库的略好；不过最有意思的是利用 GPT 的那些过程

**CppCon 2023 | Back to Basics: The Rule of Five in C++ - Andre Kostur** https://www.youtube.com/watch?v=juAZDfsaMvY&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=9

- 复习 rule of 5/0

**CppNow 2025 | Missing (and Future?) C++ Range Concepts - Jonathan Müller** https://www.youtube.com/watch?v=RemzByMHWjI&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=31

- 讲的是作者觉得目前 C++ ranges 在很多地方（讲道理这部分应用越来越奇怪了）都不够好用，缺了很多关键 pieces
- 这说的都是啥啊，完全get不到点….

**C++ Weekly - Ep 242 - Design By Introspection in C++20 (concepts + if constexpr)**  https://www.youtube.com/watch?v=sy32kAtsIKg

- 核心就是 if constexpr 中的条件可以用 concepts 的 requires clause

**C++ Weekly - Ep 243 - Computer Science: The Magic of Two's Complement Binary Math** https://www.youtube.com/watch?v=s89pOAC_X08

- 补码大讲堂

**C++ Weekly - Ep 244 - Channel News, Updates, Thank Yous, and Changes** https://www.youtube.com/watch?v=dGYOc41m81U

- 这一期没啥技术上的内容，主要是频道的更新通知和 Jason 自己写了书以及一些 repo 的分享

**C++ Weekly - Ep 245 - PMR: Mistakes Were Made (By Me)** https://www.youtube.com/watch?v=6BLlIj2QoT8

- 稍微解释了一下前面 pmr 视频的“错误”，有术语（allocator/resource）没有解释清楚；也有用了 push_back 多构造了一个临时字符串这种小问题
- 不过总结还是 pmr 这块的日常使用；目前确实没怎么看到 pmr 在严肃生产环境有人用

**What are C++26's Expansion Statements? (Ep 503)** https://www.youtube.com/watch?v=yaWiGLSDc64

- 俗称 template for，有了这个 feature 之后遍历 tuple 就变得非常简单

    ```cpp
    template <class Tuple, class F>
    constexpr void tuple_for_each(Tuple&& tup, F&& f) {
      // Heterogeneous compile-time loop: expands the body once per element.
      template for (auto&& elem : std::forward<Tuple>(tup)) {
        std::forward<F>(f)(elem);
      }
    }
    ```

- 而且目前看能用这个 template for 的类型还不少，structural binding，`{…}` 这种可枚举类型（包含不定参数）

**C++ constexpr parlor tricks: How can I obtain the length of a string at compile time?** https://devblogs.microsoft.com/oldnewthing/20221114-00/?p=107393

- 结论：使用 `std::char_traits::length()` 但是这个需要 C++ 17
- 评论中有人提到那还不如直接使用 constexpr std::string_view::size 🤔

**Enums In C++, Choice is Oft Beguiled** https://shafik.github.io/c++/2022/11/12/enums-in-cpp-choice-oft-beguilded-part-1.html

- scoped enum 默认下 underlying type 是 `int` ；如果不指定 underlying type 那么会选用能容纳最大值的类型
- enum 的 underlying type 如果固定了，那么 enum value 的范围就是 underlying type 的范围，所以即使某个 enum 的值不是定义中的几个 enumerator，也是合法的；这是为了适应大家惯用的 bit-flags 的用法

    ```cpp
    enum EBitFlags {
      Flag1 = 1 << 0,
      Flag2 = 1 << 1,
      Flag3 = 1 << 2,
      Flag4 = 1 << 3
    };

    void f() {
        EBitFlags ef = static_cast<EBitFlags>(Flag1 | Flag3); // ef will have a value of 5
                                                              // which is not of one of the enumerators
    }
    ```

- C++ 20 引入的 using enum 可以在处理 switch cases 的时候 less verbose

**Exploring Clangs Enum implementation and How we Catch Undefined Behavior** https://shafik.github.io/c++/2022/11/15/exploring-clang-enum-impl-and-ub-part-2.html

- UBSan 检测 enum 值越界用的是 Clang 内部的 `getValueRange(...)`
- 另外可以利用 constexpr 来做这个 ub 检测

    ```cpp
    enum E1 {e1=0};             // Range of values [0,1]

    void f() {
      constexpr E1 x = static_cast<E1>(2); // Ill-formed, invoking undefined behavior is not allowed in
                                           // a constant expression
    }
    ```

- 但是 clang（作者写于 2022/11月）在这块有bug，作者提交了一个 fix patch，根据 godbolt 上的结果来看这个问题已经被修复了
- 不过我发现 gcc-13 也有这个问题….

\#2

fawkes 花了一点时间对工程的设置做了一些润色，见 PR https://github.com/kingsamchen/fawkes/pull/7

其中调整 pch 这个很有意思，一下解决了之前其他模块 reuse fawkes lib pch 导致的依赖连接问题，说明一开始思路就有点问题

\#3

这周专门抽了个时间整理了一下 fmtlib 对自定义类型做格式化支持的方案

然后才知道对于支持 ostream 的类型，除了 `fmt::streamed()` 这种临时解法外，还有继承版的解法…

果然有时间还是要看文档和代码，深入了解自己常用的工具啊

相信的内容见 {% post_link fmt-custom-type-support fmt 格式化 custom types %}

\#4

这周本来打算先把 fawkes user handler 改造成协程，这个是必须的，不然 user handler 中还要想其他 trick

但是做着做着发现触发了 tidy 的 issue https://clang.llvm.org/extra/clang-tidy/checks/cppcoreguidelines/avoid-reference-coroutine-parameters.html 但是我觉得对 asio 的使用场景是安全的

思考一番之后决定先复习一下一下 C++ 协程基础，然后再专门针对 asio 的 awaitable 类型做展开，总结之后再来继续做这个。

后面估计会出一个简单的 post 当作自己的总结

---

好了这周就这样，下周见
