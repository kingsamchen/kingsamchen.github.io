---
title: 一周杂记 in Week 1 Sep 2024
categories: CODE-LIFE
date: 2024-09-09 18:44:23
tags: [杂记]
---
本周（09/02 ~ 09/08）是9月份的第一周，本周杭州依然很热 😵

## Life

\#1

周六晚上和媳妇儿还有丈母娘一起去了浙江音乐厅听了世界名曲的古典乐演奏，不过虽然名义上是经典古典乐，但是有一大半是现代流行乐的古典乐演绎，估计也是考虑到受众的接受情况 🤣

另外这个音乐厅有个很不爽的地方，每排座位之间的空间太过狭小，坐定后双腿都没法放直，坐一会儿之后小腿就开始隐隐发酸，这个差评 😒

\#2

本周观影

- **边境杀手2：边境战士 Sicario: Day of the Soldado** 3.5/5 不如前作，这部变成比较传统的动作片了，很多剧情推进都变得有点勉强，收尾也真是别扭。我有点好奇谢丽丹一开始写的剧本真的是这样的吗？
- **邪恶不存在 悪は存在しない** 3/5 不是我的菜，甚至我觉得有过誉了，除开结尾这种争议的处理手段，从叙事到摄影都挺平的。最后女孩脱帽想救中弹的鹿那段应该是男主脑中的想象画面，实际上男主和社员一起看到的已经是倒在地上的女孩。

## Work

\#1

**CppCon 2022 | How Microsoft Uses C++ to Deliver Office - Huge Size, Small Components - Zachary Henkel** https://www.youtube.com/watch?v=0QtX-nMlz0Q&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=27

- 介绍了一下 office 从 2010 年以来做了一个 Liblets 计划的改进，比如拆分modules，引入C++（以前是 C）直接从 modern c++ 开始

**CppCon 2022 | -memory-safe C++ - Jim Radigan** https://www.youtube.com/watch?v=ml4t-6bg9-M&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=28

- 前部分讲 memory safey issue 的危害，后半部基本都是 Visual Studio 如何集成 address sanitizer；因为 VS/Windows 有特殊性，所以标准的 asan 没法直接拿过来用

**CppCon 2022 | Taking a Byte Out of C++ - Avoiding Punning by Starting Lifetimes - Robert Leahy** https://www.youtube.com/watch?v=pbkQG09grFw&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=30

- 核心就是在讲为什么需要 start_lifetime_as 和 launder 这类东西
- C++ 对象底层虽然是 bytes 但是语言层面有 object lifetime 这一概念，这和 C 不太一样；C++ 20 引入的 implicit object lifetime 给 C-level 的 aggregates trivial types 打了一个补丁，但是对于一些 lib 开发者来说他们还是需要额外的工具来做一些微操
- 这个 talk 感觉不是很平易近人，老哥语速的有点快，内容一阵一阵的就没停过…

**CppCon 2022 | Back to Basics: RAII in C++ - Andre Kostur** https://www.youtube.com/watch?v=Rfu06XAhx90&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=29

- 又一个 RAII 的 back to basics 新手教学视频

**CppCon 2022 | Lightning Talk: Best Practices Every C++ Programmer Needs to Follow - Oz Syed** https://www.youtube.com/watch?v=xhTINjoihrk&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=32

- 太水了，没啥必要看

**C++ Weekly - Ep 277 - Quick Perf Tip: Avoid Integer Conversions** https://www.youtube.com/watch?v=jKpIZ4UcaNw

- integer 的隐式转换，尤其涉及到 sign ↔ unsigned，在 critical path 中对性能是有影响的
- 文中的 lowerst 是作为 vector 下标，所以用 std::size_t 反而可以避免这种转换开销

**Creating other types of synchronization objects that can be used with co_await, part 9: The shared mutex (continued)** https://devblogs.microsoft.com/oldnewthing/20210319-00/?p=104979

- 系列第29篇
- 这篇解决之前 shared mutex 中 shared lock 可能会饿死 exclusive lock 的问题，做法是如果此时有 waiter on the mutex，那么 shared lock 也去排队
- 另外一个是最后一个 shared lock release 可能存在 race，结局方法是最后一个释放的时候转为 exclusive lock

**Creating other types of synchronization objects that can be used with co_await, part 10: Wait for an event to clear** https://devblogs.microsoft.com/oldnewthing/20210322-00/?p=104981

- 系列第30篇
- 引入一个支持 wait on reset 的 event

**Creating a task completion source for a C++ coroutine: Producing a result** https://devblogs.microsoft.com/oldnewthing/20210323-00/?p=104987

- 系列第31篇
- 开始着手实现能提供返回值的 awaitable type

**Creating a task completion source for a C++ coroutine: Producing a result with references** https://devblogs.microsoft.com/oldnewthing/20210324-00/?p=104995

- 系列第32篇
- 这篇主要研究怎么给返回类型加上引用支持，第一个做法是额外包一层 struct 然后塞进 Union，这样就可以避开 union 不能放 reference member
- 第二个是返回类型用 decltype(auto) 来做 forwarding

**Did you know about C++17 variadic using declaration?** https://github.com/tip-of-the-week/cpp/blob/master/tips/231.md

- 继承 variadic classes 之后可以用 variadic using 来做一些 trick；不过这个 post 里的 trick 玩的比较high
- 另外 visitor pattern 常用的那个 overloaded 其实也是用的这个 trick

**How To Use std::visit With Multiple Variants and Parameters** https://www.cppstories.com/2018/09/visit-variants/

- std::variant & std::visit 的 quick introduction

**The big STL Algorithms tutorial: merge and inplace_merge** https://www.sandordargo.com/blog/2021/06/23/stl-alogorithms-tutorial-part-21-merge-inplace_merge

- 其实就是归并排序的归并过程的 STL 实现
- inplace 那个版本想了一下还是有一些trick要注意的

\#2

这周 nlohmann-json serde 的进度推的还可以。

设计上 `options` 支持 filter 或者 action，一开始是采用 `std::optional` 包成员的方式来实现只有 filter/action 的情况，但是总觉得这个实现有点不优雅，比如设计语义上看不出 filter/action 是可选的但是这两个至少要有一个的情况。

后来突然想到可以用 class template partial specialization 来针对只有 filter 或者只有 action 做特化，这样就干净多了，而且 dummy-filter/action 也只需要一个 tag class 来触发偏特化即可。

另外在实现 `with()` 重载的时候，因为 `with(filter)` 和 `with(action)` 的参数都是模板参数，所以一开始 SFINAE 是做在额外模板参数里的，即

```cpp
template<typename Filter, typename = std::enable_if_t<is_filter_v<T>>>
auto with(Filter filter) {
    return options<Filter, dummy_action>(filter);
}

template<typename Action, typename = std::enable_if_t<is_action_v<T>>>
auto with(Action action) {
    return options<dummy_filter, Action>(action);
}
```

这会导致编译器认为 `with()` 被重复定义了，而不是一对重载，因为本质上这两个函数的签名完全一致，SFINAE 的额外参数来不及起作用。

想了一下改成

```cpp
template<typename Filter>
auto with(Filter filter, std::enable_if_t<detail::is_filter_v<Filter>, int*> = 0) {
    return options<Filter, dummy_action>(filter);
}
```

把 SFINAE 引入到函数参数部分之后会让这部分变成 dependent context，函数签名的确定要等到实例化之后，这样就不会被认为 redefine 了。

---

好了这周就这样，下周见
