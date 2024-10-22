---
title: 一周杂记 in Week 3 Oct 2024
categories: CODE-LIFE
date: 2024-10-21 23:24:24
tags: [杂记]
---
本周（10/14 ~ 10/20）是十月份的第三周。

## Life

\#1

周三的时候“忽悠”媳妇儿一起来公司参加观影会，顺带蹭一顿公司晚饭福利。

不过媳妇儿对电影不是很感冒，而且看到一半感觉会议室太闷了，就出去透气了。

回家路上的时候还吐槽说本来想在茶水间休息区透气，但是那边吃饭的人比较多而且稍远点的剩菜剩饭垃圾桶离得有点进，她闻了有点想吐。。。

周五傍晚提早半个小时下班，和 chao 还有 iker 一起去德信影院杭州之翼商场看 都灵之马，就是那个之前我停车把车蹭了的商场 🤣

为了不浪费时间，在公司和chao一起点的跑马场的三明治，有点小贵，但是感觉确实品质不错。

来回影院都是坐的 iker 家的 minicooper，他媳妇儿也一起。

\#2

本周观影

- **美国小说 American Fiction** 4/5 极其欢乐，和同事在公司观影俱乐部上一直笑个不停。男主一家是出身良好的老黑，自身对社会关于黑人群体的stereotype极其反感，但是白人群体反而很喜欢popularize这种观念以掩藏自身内心的虚伪。期待什么时候来一部嘲讽 woke 的电影
- **都灵之马 A torinói ló** 3/5 直接给我看沉默了，看完第一个小时后面基本都让我猜到了...意象我都懂，但是尼玛155分钟还是太长了，125分钟的话能好很多，导致我看完之后差点要变成尼采了，反而是怀孕的媳妇儿要来照顾我了…

\#3

周六开车先和媳妇儿一起去了她医院让她查个房，结果一开始预期要一个半小时的查房半个小时就完成了

然后就直接开车去她家了，休息了一段时间之后吃了晚饭，然后和丈母娘还有她朋友一起去媳妇儿家附近的杭州大剧院听丈母娘很喜欢的一个叫 罗小罗 的歌手的小型演唱会。

说是小型是因为现场估计就来了200多人，不过目测大多数人都是50往上的大爷大妈。

歌手的声音还是挺强的，就是自己的原创歌曲就两首，而且原创和翻唱的歌基本都是一个风格的慢歌，挺多了就容易困，事实上我也中间睡着了大概二十几分钟 😂

散场后丈母娘和她朋友不知道哪里搭的关系，能到后场和歌手合影还不公开的一起合唱了一首歌，虽然我和媳妇儿全程在外面等 😂

## Work

\#1

**CppCon 2022 | Rules for Radical Cpp Engineers - Improve Your C++ Code, Team, & Organization - David Sankel** https://www.youtube.com/watch?v=ady2mUIQpt4&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=49

- engineer soft-skill 方面的东西
- tech-team management 是挺难的

**CppCon 2022 | Smarter Cpp Atomic Smart Pointers - Efficient Concurrent Memory Management - Daniel Anderson**

- atomic shared_ptr 几乎没有 scalability
- hazard pointer 和 RCU 在性能上非常有优势，但是并不太容易使用
- 作者研究团队再 shared_ptr 之上藉由 hazard pointer 和 RCU 的手法搞了一套 lockess 实现策略性能不仅使用简单，性能还接近裸 hazard pointer 和 RCU
- 这个 talk bar 挺高的，有兴趣的可以直接读他们的论文和参考实现

**CppNow 2024 | C++ Type Erasure Demystified - Fedor G Pikus** https://www.youtube.com/watch?v=p-qaf6OS_f4&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=13

- 干货很足的一个 talk
- 先介绍了 type erasure 的 common definition，其中我觉得最重要的是 non-intrusive 和 external polymorphism，至少这是我 prefer type erasure 的原因之一
- 然后提到了三种实现 type erasure 的常用技法：
    1. 传统的基于继承/虚函数搭配模板；好处是通用，缺点是动态内存分配有影响，不过可以通过 local buffer 优化
    2. 通过静态函数包装函数指针；优点是性能好，缺点是每个操作都需要一对静态函数+函数指针，并且如果函数是 stateful，那么基本也需要引入 local buffer 来解决存放状态
    3. 手工vptr table；这是对方法2的优化，用了各种 tricks
- 最后给了一个优化版的 function-te
- 然后最后 Q&A 环节好像大家焦点都在 benchmark 是否考虑了 branch predication / cache hit 这些…

**CppNow 2024 | The Importance of the C++ Build System Target Model - Bill Hoffman** https://www.youtube.com/watch?v=bQQMCdIsjgw&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=20

- 核心内容是有一个 common package specification 的提案，任意一个 build system 只要按照这个规范来接受/输出 package 的话大家之间可以互通
- 其他的内容都是围绕着目前各家构建系统，这里以 cmake 为例，各自存在一些不同并且不好兼容的地方

**C++ Weekly - Ep 450 - C++ is a Functional Programming Language** https://www.youtube.com/watch?v=jf_OxE3j4AQ

- 就简单提了一下 functional object 其实…
- 说是会做成一个 series，看看后续

**How to Define Comparison Operators by Default in C++** https://www.fluentcpp.com/2021/08/23/how-to-define-comparison-operators-by-default-in-c/

- 如果 `MyType` 的 ordering 定义为每个成员的 ordering，那么
    - Before C++ 20，利用 `std::tuple<>` 来高效实现

        ```cpp
        bool operator<(MyType const& lhs, MyType const& rhs)
        {
            return std::tie(lhs.member1, lhs.member2, lhs.member3, lhs.member4, lhs.member5)
                 < std::tie(rhs.member1, rhs.member2, rhs.member3, rhs.member4, rhs.member5);
        }
        ```

    - C++ 20 开始，直接通过 `<=> = default`

        ```cpp
        friend bool operator<=>(MyType const& lhs, MyType const& rhs) = default;
        ```


**= delete; // not just for special member functions** https://vorbrodt.blog/2021/09/16/delete-not-just-for-special-member-functions/

- 核心想法是既然 `=delete` 可以运用到普通函数，那么就把一些不希望匹配上的函数重载用 `=delete` 屏蔽掉
- 其实就是穷人版的 SFINAE-out，但是讲道理我更偏好使用 SFINAE 来直接让某些函数不参与 overload resolution

**C++ coroutines: The lifetime of objects involved in the coroutine function** https://devblogs.microsoft.com/oldnewthing/20210412-00/?p=105078

- 对比简单实现的 promise 和优化实现（inlined, refcounted）版本的在生命周期管理上的区别

**C++ coroutines: Tradeoffs of making the promise be the shared state** https://devblogs.microsoft.com/oldnewthing/20210413-00/?p=105093

- 支持多次 co_await 的 promise 会无畏地拉长自己的生命周期，占用太多资源；所以和 simple promise 是需要分场景来使用的
- 这篇为后面的 simplify simple promise 起个头

**C++ coroutines: Making it impossible to co_await a task twice** https://devblogs.microsoft.com/oldnewthing/20210414-00/?p=105095

- 强制只能通过 `co_await std::move(task)` 来处理 promise

**C++ coroutines: Getting rid of our mutex** https://devblogs.microsoft.com/oldnewthing/20210415-00/?p=105109

- 通过 atomic<coro_handle> 替换，利用 nullptr, self coroutine handle value 来表示 not-complete/complete yet no-registered waiter 的情况

\#2

这周突然想抽点时间给 esl 加上 Github CI pipeline，原因最近有几个 esl 的改动要做一下，而之前每次有改动都要自己手动在 Linux 和 Windows 两个平台跑一边相关流程，其实也挺烦的，而且还容易有遗漏

所以想着如果能加上 CI pipeline，那么这部分回归就可以自动化了，能省不少时间。

于是选了 fmtlib 的 workflow 作为参考，加上自己微调，把初步的 pipeline 做好了，见 https://github.com/kingsamchen/esl/pull/19

目前触发条件限定为创建 PR，因为非 master 分支的推送我觉得很多时候只是暂存一下，没必要过 pipeline；而我现在习惯就算是自己的提交也走 PR，这样一些信息都可以留档

目前支持的操作有

- Lint，会检查 cpplint, clang-format 和 clang-tidy
- Build and test on Linux，ubuntu 22.04 为基础环境，gcc-11 ~ gcc-13, clang-17 ~ clang-19 各自三个版本
- Build and test on Windows，vs2022 和 vs2019，并且只考虑 X64 架构

目前看基本的需求都覆盖到了，特别是有 cmake presets 的支持后工程相关的流程就可以弄得非常简单

---

好了这周就这样，下周见
