---
title: 一周杂记 in Week 1 Aug 2025
categories: CODE-LIFE
date: 2025-08-11 00:05:23
tags: [杂记]
---
本周（08/04 ~ 08/10）是8月份的第一周，灾后重建 quality workstream 已经略有眉头的不错进展

## Life

\#1

这周体检结果很速度的出来了，整体结果不如去年同期，不仅血脂没有好转，心电图还变糟糕了 囧rz

现在是真的有点怕怕，所以和老婆讨论了一下之后重新定了方向和方针，具体包括：

- 早睡，以后要控制11:30 -- 12:00之内上床，不仅要充分保证睡眠，还要提早入睡。因为很怀疑是之前睡觉过完导致血脂不好控制
- 中饭之前应该没啥问题，晚饭要保持多样化，不能隔三岔五KFC原味鸡或者一直吃鸡蛋了。而且晚饭还要多来一些水果
- 早上目前保证有酸奶和牛奶
- 每天多喝水，尽量避免和饮料；咖啡也减少一般的摄入，运动饮料也仅在非常有必要时候

这周至少睡眠上感觉有很大提升，早起都比较成功，并且睡眠时间都有了保证。

另外有氧跑步要注意控制心率了，最高峰心率要压低在165BPM。

于是周四晚上压心率跑了一次7km，最高心率连续两分钟超过165就立马减速停下来；整体感觉不错，最终平均心率压在 154，整体配速只有略微下降一些，而且跑完之后感觉一点气都不喘。觉得应该是一个不错的开始。

周五上了一次BP，和 Guts 还有 Hogan 吃了一家餐厅，但是这家感觉一般。

周日上了一次BC，心率和热量消耗爆表了，一节课烧了600多大卡的热量，确实有点牛逼

\#2

周三晚上 Roro 请了灵隐的米其林餐厅，桂语山房。

这个餐厅的味道确实不错，毕竟米其林一星，但是比较蛋疼的事这个餐厅在灵隐里，下班过去的时候和总理打了一辆车，进入灵隐之后在路上就堵上了，堵了大半个小时才到饭店 🤷‍♂️

不过还好吃完之后蹭了 Roro 的车回到公司，骑上电驴直接回家了。

以后要是有时间可以带媳妇儿来这里吃。🤔

\#3

这周末继续去媳妇儿家带娃，一周没见感觉娃又有点点变大了，而且变得更清秀可爱了，而且这周感觉特别想说话，一直在咿咿呀呀而且很想别人和她一起陪伴

周日晚上和媳妇儿一起看了浪浪山小妖怪，老婆觉得一般，但是我觉得还行

- **浪浪山小妖怪 4/5** 故事还行，小人物视角的主题注定会有不少加分。不过这个传统画风实在不太讨喜

## Work

\#1

**CppCon 2022 | tipi.build A New C++ Package Manager - Damien Buhl** https://www.youtube.com/watch?v=cxNDmugjlFk&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=93

- 他们家的 build 有两个卖点
    1. 非显式声明依赖，构建的时候自动识别，这部分是包管理提供的能力
    2. global build cache
- 这家公司最近被收购了，现在似乎转型做专门的 build cache 的业务

**CppCon 2022 | Lightning Talk: C++ Debug Performance is Improving: What Now? - Vittorio Romeo** https://www.youtube.com/watch?v=CfbuJAAwA8Y&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=151

- 这个 talk 有点意思，之前看过类似的，本质是说 zero-cost abstraction 大量依赖编译器优化，导致 debug build 几乎性能非常糟糕
- 一个经典例子就是 `std::byte` ；甚至 debug build 下 `std::move()` 都不会被 inline
- Clang/GCC 这两家开始直接在编译器前端做着一些优化
- 查了一下原来就是之前已经提过的这篇 https://vittorioromeo.info/index/blog/debug_performance_cpp.html

**CppCon 2022 | Lightning Talk: Now in Our Lifetimes Cpp - Staffan Tjernstrom** https://www.youtube.com/watch?v=pLVg3c6bljE&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=147

- C++ 目前为了避免UB的一些 lifetime 相关的设施，例如 start_lifetime 还有 launder

**C++ Weekly - Ep 492 - initializer_list Constructors** https://www.youtube.com/watch?v=E5aEt4917mM

- 这个有点炒冷饭了，讨论过很多次的 initializer_list 影响构造函数语义的问题

**C++ Weekly - Ep 491 - Extract, Merge, Insert: C++17 Node Handles** https://www.youtube.com/watch?v=VzDMgStcCGs

- 介绍了一下 extract / merge 这几个 node-based operations

**C++ Weekly - Ep 490 - std::ignore vs _ vs [[maybe_unused]]** https://www.youtube.com/watch?v=iUcS0LCj2Ko

- `std::ignore` 和 `[[maybe_unused]]` 都有缺陷
- 最优解是 C++ 26 引入的 `_` 的新语义，不过慢慢等吧

**C++ Weekly - Ep 306 - What Are Local Functions, and Do They Exist in C++?** https://www.youtube.com/watch?v=-EDx6fC6mkQ

- 省流：直接用 lambda

**C++ Weekly - Ep 305 - Stop Using `using namespace`** https://www.youtube.com/watch?v=MZqjl9HEPZ8

- 给了一个非常 compelling 的例子为什么不要这么做
- 因为会影响 name resolution 而且不会有任何告警什么的

**Prefer core-language features over library facilities** https://quuxplusone.github.io/blog/2022/10/16/prefer-core-over-library/

- 这篇有点意思，是说有些 core lang features 和 library facilities 有功能重叠，作者推荐优先考虑 core lang features；虽然有些选择比较有争议
- 另外作者着重说了一个反例：CTAD。他个人非常执著地认为CTAD是一个很糟糕的 lang feature

**Why is there a make_unique? Why not just overload the unique_ptr constructor?** https://devblogs.microsoft.com/oldnewthing/20221019-00/?p=107300

- 原因是会引起各种语义上的混乱
- std::unique_ptr<T> 本身存在了构造 unique_ptr 的语义，如果要额外加上构造 T 的语义就会混乱不堪。

\#2

boost.unordered_flat_map 的性能比 absl/flat_hash_map 要好不少，尤其在 heavy lookup 场景下

https://www.boost.org/doc/libs/latest/libs/unordered/doc/html/unordered/benchmarks.html#benchmarks_boostconcurrent_flatnode_map

标准库目前只有 flat_map 的 proposal，不知道后续会不会把 unordered_flat_map 也加进去

\#3

这周终于把 fawkes 的 router 部分都写完了，目前可以做到

```cpp
DEFINE_uint32(port, 9876, "Port number to listen");

int main() {
    try {
        boost::asio::io_context io_ctx{1};
        fawkes::server svc(io_ctx);
        svc.do_get("/ping", [](const fawkes::request&, fawkes::response& resp) {
            resp.body = "Pong!";
        });
        svc.listen_and_serve("0.0.0.0", static_cast<std::uint16_t>(FLAGS_port));
        io_ctx.run();
    } catch (const std::exception& ex) {
        spdlog::error("Unexpected error: {}", ex.what());
    }

    return 0;
}
```

request/response 比较原始，这俩的封装也会是下一步的核心

\#4

抽了个空给 anvil 修了两个 issue

https://github.com/kingsamchen/anvil/pull/26

---

好了这周就这样，下周见
