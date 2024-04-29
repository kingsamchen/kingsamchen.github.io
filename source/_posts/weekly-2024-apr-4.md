---
title: 一周杂记 in Week 4 Apr 2024
categories: CODE-LIFE
date: 2024-04-29 15:10:15
tags: [杂记]
---
本周（04/22 ~ 04/28）是四月份第四周，也是最后一周。下周三就是五一。

## Life

\#1

这周花了不少时间练车，尤其接送媳妇儿每次都要富阳往返，加起来差不多刚好100公里；而单程50公里又刚好包含了普通的城市道路，隧道，快速路，甚至富阳大学城附近的烂路，所以算是非常不错的练习道路了。

这一周加起来跑了大概有三次富阳往返，感觉熟练度蹭蹭上涨

另外周六看电影去西湖文化广场也是开车去的，除了不知道在哪里需要掉头之外，感觉城市道路已经可以比较熟练的行驶了

我觉得再练一个月应该就可以进入熟手驾驶之列了 :P

\#2

周五组里团建，吃饭的地方选在了城西银泰，直接给我激动的；毕竟离我家也太近了，直接走这就到了。

吃饭选的很久以前，感觉味道不错，就是有点小贵，性价比一般

吃完之后和同事慢慢走到了亚运公园，不过现在改名叫拱墅区运动公园了

围观组里其他人打了一两个小时德扑之后就下班回家休息了。

这团建附赠了半天的假期也还是爽的 🤣

\#3

这周观影看得比较多

- **幕府将军 Shogun** 完结 2/5 吐了，和权利游戏一样终极烂尾糊弄人...白瞎了真田广之...男主从头到尾都是一副“我好惊讶啊”的表情…
- **破墓 파묘** 3/5 在公司电影俱乐部一起看的，算上了一层 debuff。前半段还是有点惊悚的，但是后半段的故事...而且这两段感觉没有衔接起来
- **年少日记** 4.5/5 情感渲染恰到好处但是后劲很大；各演员的表现都很棒，郑中基把那种有一些天赋的底层小人物靠自我奋斗发达之后变成 the victim of own success 演绎的太到位了；快结尾突然切出来男主是弟弟的视角一瞬间有点愣神
- **杀手47** 3/5 故事性比后来那版好，但是总感觉这版本的 47 过于人性了…

## Work

\#1

**C++ Templates 2nd | 1. Function Templates**

- 第一章比较简单，聚焦在函数模板上
- 函数模板重载这块可能要注意一下，并且书里也给出了建议：确保只有一个重载能匹配所有调用（避免ambiguity）以及重载尽量只改动模板参数相关的行为并且一眼就可以看出来

**CppCon 2021 | A Crash Course in Calendars, Dates, Time, and Time Zones - Marc Gregoire**

- 总时长只有30分钟却要覆盖 chrono 离的几个大块，确实有点 crash course；不过整体值得看的，尤其还提到了一些坑，比如 predefined duration 都是 integral type 作为 rep；以及 C++ 20 的 date 中的 + 1year 是要考虑闰年的
- 另外我才注意到原来几个 clock 的 duration 定义都是 impl-defined的，cpp reference 上难怪查不到，估计是不同平台的 Rep 不好确定吧，但是 time_point 又是需要通过关联的 clock 获取 Rep

**CppCon 2021 | The Factory Pattern - Mike Shah**

- 比较适合初学者，主体都是 interface based 传统那套

**CppCon 2021 | Design Idioms from an Alternate Universe - Ivan Čukić**

- 讲 FP in C++ 的，我觉得还是去看作者写的那本书好了…

**CppCon 2021 | Sums, Products, Exponents, Monoids, Functors, Oh My! - Steve Downey**

- 比上一个讲 FP 的还干，讲了一堆的理论知识…

**What C++ Fold Expressions Can Bring to Your Code** https://www.fluentcpp.com/2021/03/19/what-c-fold-expressions-can-bring-to-your-code/

- 依然还是 fold expression，不过这次讨论得稍微深入了一点
- 甚至提到了那个经典的 struct overloaded

**Using C++17 to Create Composable, Recursive Data Types** https://tech.davidgorski.ca/using-c17-to-create-composable-recursive-data-types/

- 本质是拿 `std::varaint` 做嵌套类型

    ```cpp
    // Forward Declaration of Tree Node
    struct Node;

    // Simple
    using Int = int;
    using String = std::string;
    using Null = std::monostate;

    // Composite
    using Sequence = std::vector<Node>;
    using Data = std::variant<Int, String, Null, Sequence>;

    struct Node {
        std::string m_name;
        Data m_data;
        explicit Node(std::string&& name, Data&& data)
            : m_name(std::move(name)), m_data(std::move(data)) {}
    };
    ```


**Reflecting Over Members of an Aggregate** https://bitwizeshift.github.io/posts/2021/03/21/reflecting-over-members-of-an-aggregate/

- 方案仅针对 aggregates 有用，但是思路很巧妙：利用 aggregate initialization 配合神级 template 技巧拿到成员个数，然后再结合 structural binding 把每个成员的访问拿到
- 不过现在就推荐直接用 boost.pfr 了，文章里面也提到了

\#2

这周把之前写的 esl 里的 unique_handle “迁移到” 公司项目 codebase 里时发现改用 GTest 之后有个 test case 直接编译失败

```cpp
auto fd = wrap_unique_fd(::open(...));
ASSERT_TRUE(fd.get() != -1);
```

提示 operator!= ambiguity

这里的 `fd` 其实是 `std::unique_ptr<unique_fd_deleter::pointer, unique_fd_deleter>`，所以 `get()` 返回的其实是 deleter 定义的 pointer 对象，本质上是我自己实现的 `handle_ptr`

思考了一下感觉是因为我的 `handle_ptr` 即提供了 operator T 到 raw handle value 的转换，又有 raw handle value 到 handle_ptr 的 implicit ctor，所以这里 ambiguity 了

因为已经有了 wrap 系列函数，所以隐式构造存在性不大，加之比起隐式转换到 raw handle value 更容易出现错误，所以我去掉了单参数这里的隐式构造

确实最后也解决了问题，esl 自己的 PR 见：https://github.com/kingsamchen/esl/pull/15

\#3

之前说好的 absl/status 的源码实在是没时间看，下周五一小长假，希望有点时间可以看看吧...Orz

---

好了这周就这样，下周（五一假期）见
