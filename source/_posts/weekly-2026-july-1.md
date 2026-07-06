---
title: 一周杂记 in Week 1 July 2026
categories: CODE-LIFE
date: 2026-07-06 21:33:02
tags: [杂记]
---
本周（6.29 ~ 7.5）是七月份的第一周，Q3正式开启。

## Life

\#1

这周的天气实在不行，几乎每天都有一段时间在下雨。

哪怕中午艳阳高照，到了下午转眼就开始瓢泼大雨。

也不清楚是还没出梅雨呢还是因为台风要来了

\#2

喜提职业生涯第二次，ZM 生涯第一次 PIP。

不过自从我听到这次 618 大裁员换成每个部门 5% 的小裁员的时候我就猜到老崔估计要拉我清单了，结果还真让我猜对了。

老崔说什么都是要让我顶名额，但是最后被 chao 发起了三方交易，最后给我捞到了 scheduler。不过 PIP 流程还是得走，加上老崔这个不确定因素，所以三个月后再看吧

反正我已经看得比较开了，大钱能赚的都赚的差不多了，小钱嘛，就干一天算一天咯。

最搞笑的是老崔找我 1:1 说这事的时候，说我产出少，做事不主动，开口就是：去年做 account-id routing 的时候....

去年被我当众喷了之后这个小本本真是一直记到现在啊，而且去年底的时候要不是出了几次 outage 被打脸了，那会儿就已经给我 PIP 了

不过既然能正式转到 scheduler，那也是个好事，很多事情都可以光明正大而且是全职做了。

\#3

这周日因为 Hogan 有事，所以约了周六下午14点的拳击课。

结果到了健身房才发现操房已经被人占用做舞蹈的内训了。。。

包子也是一脸无语，最后只能延迟到下周。

然后我和 Hogan 就随便做了一下器械训练 🤡

## Work

\#1

**CppNow 2025 | Five Issues with std::expected and How to Fix Them - Vitaly Fanaskov** https://www.youtube.com/watch?v=eRi8q1FjEoY&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=37

- 作者认为的标准 std::expected 的 5 个问题
    1. 允许 Value 和 Error 使用相同类型（或可隐式转换的类型）；即 标准库的 `std::expected<T, E>` 并没有限制 `T` 和 `E` 不能为同一种类型。这导致需要引入像 `std::unexpected` 这样的额外包装抽象来做区分，整个设计显得不够对称。此外，这也导致底层实现及概念（Concepts）异常复杂。
    2. 还提供了 `.value()` 这种传统且危险的接口；作者建议的风格是通过包装，只允许 monadic ops 和一个 pattern match 接口来处理
    3. 缺乏定制点，`value()` 不满足会抛异常；直接用 `->` 会UB，无法让使用者做一些细微干预
    4. monadic 操作还是太过繁琐，所以他自己包了一层内部用了 bind_front/bind_back
    5. 缺少 Applicative Functor / apply 机制
- 仁者见仁智者见智吧。。。

**C++ Weekly - Ep 539 - Modernizing C++ with AI** https://www.youtube.com/watch?v=SJ9Jm-K9bYU

- 用 AI 迁移老的 C/C++ 代码时候可以采用 push variables down 的方式来减少负债
- push variables down 指的是在使用变量时才定义，主要面向的还是传统 C 的问题

**C++ Weekly - Ep 122 - `constexpr` With `optional` And `variant`** https://www.youtube.com/watch?v=2eCV_udkP_o

- 老版本的 gcc 会把 optional operator=(U) 也实现成 constexpr 但是这是一个bug
- 但是C++20开始，optional operator= 都是 constexpr 了
- std::variant 类似

**C++ Weekly - Ep 121 - Strict Aliasing In The Real world** https://www.youtube.com/watch?v=tODCIrUat78

- pointer aliasing 会让 simd 代码生成的时候考虑可能的重叠，导致性能更低
- 另外 std::uint8_t* 有特殊含义，编译器会默认这个类型的指针会和其他指针参数存在重叠；type alias 可以绕开，但是很容易引发 UB

**C++ Weekly - Ep 120 - Will It C++? The Tandy 1000 From 1984** https://www.youtube.com/watch?v=tV61e2-Xd4k

- 老硬件设备 16-bit cpu 跑 c++ 1x

**C++ Weekly - Ep 119 - Negative Cost Structs** https://www.youtube.com/watch?v=FwsO12x8nyM

- 函数传参 多个 uint8_t 不如传一个等价的 struct，编译器会做寄存器的使用优化
- int32_t 我猜也有类似的优化

\#2

因为这周已经划到 scheduler 了，所以可以光明正大给 scheduler 做东西了。

这周把 scheduelr 里 makefile 相关的旧东西都给删除了，并且把 gitlab pipeline jobs 都调整了一遍。

clang-tidy-diff job 一开始还踩到了经典的 docker cgroup cpu quota 和 nproc 这种宿主机对不上的问题，不过确认原因后立马就解决了。

这周在做的另外一个事情是，尝试给 scheduler-api-server 这个 httpd module 加上/替换 jemalloc。这是为了试图缓解/解决当前产线容器中这个服务内存目前没法及时回收导致 OOM。

因为集成 sanitizers 需要花更多的功夫，所以目前第一步是先集成 jemalloc，看看 jemalloc 的归还机制下，能不能有效缓解 POD RSS 一直高水位的问题。

下周就可以直接上 dev 甚至 go 去做验证了。而且不管是否有效，sanitizer 的集成都会开始

---

这周就这样，下周见
