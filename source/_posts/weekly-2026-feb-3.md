---
title: 一周杂记 in Week 3 Feb 2026
categories: CODE-LIFE
date: 2026-02-23 23:37:54
tags: [杂记]
---
本周（02/16 ~ 02/22）是2月份第三周，2/16 第一天就是除夕，春节假期正式开始。

## Life

\#1

最后一个工作日岳父的值班安排出来了，初四才开始值班，所以除夕和前几天可以来杭州。

一开始和媳妇儿打算在银泰订一家台州海鲜作为年夜饭两家人一起吃，并且除夕前一天媳妇儿和岳父母都去试吃了觉得菜品不错，相当满意。

但是打算预定的时候才知道包厢早就没了，美团上的信息是 out of date 的他们一直没更新 🤣

虽然大堂还可以坐，但是考虑到年夜饭大堂人太多，对秋宝不好，所以还是打算在自家烧好了。

所以除夕的年夜饭就是两家人在我家吃的自家烧饭。

不过比起来确实便宜很多，盒马放开了买最后也就花了一千多出头，比起酒店合菜少了 2/3。

\#2

今年我爸和我姐都是除夕下午到的杭州，一个高铁一个飞机，所以我当了一下午司机。

这里必须要吐槽一下萧山机场出口附近的道路规划，高德在这里连续犯错，我们重试了三次才开上外部的道路，还是靠媳妇儿的分析。。。

而且因为我爸妈我姐都来了，我晚上睡觉会打呼噜，所以三个房间不够用，我连续三天只能躺在秋宝的爬爬垫上睡觉...睡起来是真的挺累的

\#3

老婆几天前就想去迪士尼玩，做了一点点攻略。

然后初一上午就把我叫醒了说我们买票去迪士尼吧。

然后就8点半多买了三张带8项速通的票：我，我媳妇儿，我姐。

高速上开了两个多小时，终于到了迪士尼；你还别说，自驾去迪士尼是真的挺方便的，除了老婆一开始想 11:30 再进停车场可以省一点停车费，结果我控时间费劲巴拉的也只晚了17分钟，11:24就到了停车场。。。

又挨打又吃屎。。。

不过迪士尼整体是真的好玩，玩了一天开心；而且外面迪士尼小镇的饭店性价比还可以

吃完饭在去停车场路上看了一会儿烟花，就开高速回去了。

不过我要吐槽一下夜间高速，太难了，老有抽象的人打远光

\#4

因为今年家里还是要初四做东，所以给我爸妈我姐买了初三下午的高铁票，顺带开车送他们去东站。

我妈初六下午祠堂酒席吃完之后再回来

期间刚好岳母在，所以岳母临时接替三天带娃

这两三天也是继续当司机 🫡

## Work

\#1

**Asynchronous Programming with C++ | 7. The Async Function**

- 这一章介绍 `std::async()` 和配套的各种 policies
- 实话说我觉得务实一点的资料就应该直接说：严肃生产代码不要用 `std::async()`，这玩意儿就是个半成品

**CppCon 2023 | Great C++ is_trivial: trivial type traits - Jason Turner** https://www.youtube.com/watch?v=bpF1LKQBgBQ&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=24

- 前半部分涉及 RVO/NRVO 的部分可以作为一个查漏补缺，但是核心是引出后面“trivial types 有更多的优化机会”
- 感觉这个 talk 还是有点意思

**C++ Weekly - Ep 520 - Yes, UB Really is THAT BAD** https://www.youtube.com/watch?v=atEP9wbuaL0

- UB 搭配优化选项生成会导致生成“奇怪”的最终代码这个都已经算是尝试了
- 不过比较有意思的是2016年的 gcc-6 优化模式下会导致很多当时大家习以为常觉得合理的UB代码优化飞，当时造成了不小的风波，包括Qt，KDE 甚至 Chromium 都中枪

**C++ Weekly - Ep 519 - initializer_list vs Initializer List** https://www.youtube.com/watch?v=8OlG6ya3kIY

- auto f = foo{abc}; 中的 `{}` 是 initialization list，with space；而 std::initializer_list 是标准库设施
- 我觉得很多时候是口头表达的问题
- 另外 talk 中

    ```cpp
    struct foo {
      int x, y, z;
    };

    foo f1({1, 2, 3});
    foo f2{1, 2, 3};
    ```

    `f1` 也能编译通过的原因评论区有人说了，这里用 cppinsight 也可以看出来 https://cppinsights.io/s/fe082453

    ```cpp
    struct foo
    {
      int x;
      int y;
      int z;
      // inline constexpr foo(foo &&) noexcept = default;
    };

    int main()
    {
      foo __temporary9_20 = {1, 2, 3};
      foo f1 = foo(__temporary9_20);
      /* __temporary9_20 // lifetime ends here */
      foo f2 = {1, 2, 3};
      return 0;
      /* f2 // lifetime ends here */
      /* f1 // lifetime ends here */
    }
    ```


**C++ Weekly - Ep 192 - Stop Using Double Underscores** https://www.youtube.com/watch?v=0vJXQ2VOzEs

- 标准说下面这些命名是 UB：
    - `__foo_bar` 开头两个 _
    - `_Foobar` 开头一个下划线，然后紧接着一个大写字母
    - `constexpr auto _pi = 42.`  全局作用域的下划线开头
- 所以还是用 trailing _ 好

**C++ Weekly - Ep 191 - Cevelop IDE With CMake Based Projects** https://www.youtube.com/watch?v=nnfdvE2qNeo

- 当考古好了
- Cevelop 是基于 eclipse cdt 之上加了一点点 template visualization 的功能
- 反正 github 上最后一次更新都是5年前了 https://github.com/Cevelop/cevelop
- 现在直接无脑 vscode 就行…

**C++ Weekly - Ep 190 - The Important Parts of C++17 in 10 Minutes** https://www.youtube.com/watch?v=QpFjOlzg1r4

- 覆盖的特性
    - Guaranteed copy elision
    - `constexpr` support in the standard library
    - `constexpr` lambdas
    - `std::string_view`
    - Class template argument deduction (CTAD)
    - Fold expressions
    - Structured bindings
    - `if` init expressions

\#2

搞完了 fawkes 的 graceful shutdown support 以及 cancellation 的相关使用。

这个 graceful 是跟具体业务场景的实现要求高度相关的，目前是提供了必要的 building blocks，并且开启 server serve_timeout 之后基本不会有 http session 能卡死 shutdown 流程的情况。

PR：https://github.com/kingsamchen/fawkes/pull/23

**插曲**

---

这个 PR 跑 CI 的时候 clang 相关的都挂了，搞得我一脸懵逼。

研究了一下发现是因为 ubuntu 22.04 LTS 从 apt 上游拉取 clang packages 但是上游给的 package metainfo 是有问题的...

搜了一下 issue 果然是最近刚被 llvm-team 搞出来的问题：[llvm/llvm-project#182462](https://github.com/llvm/llvm-project/issues/182462)

等他们 fix 太慢了于是我直接把 runner image 从 ubuntu 22.04 升级成了 ubuntu 24.04，顺带把 gcc 的 ci 版本也轮次改成 12 / 13/ 14

\#3

之前说的系列第一篇写完了：{% post_link general-purpose-cancellation-mechanism-stop-token std::stop_token: A General-Purpose Cancellation Mechanism %}

---

好了本周就这样，下周又要当牛马去搬砖了。
