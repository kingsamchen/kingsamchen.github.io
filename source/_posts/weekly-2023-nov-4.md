---
title: 一周杂记 in Week 4 Nov 2023
categories: CODE-LIFE
date: 2023-11-27 22:52:48
tags: [杂记]
---
本周（11/20 ~ 11/26）是十一月第四周

## Life

\#1

本周观影

- 东方的承诺 Eastern Promises 3/5 先不说核心的三个俄国人是由一个美国人一个法国人一个德国人假装的，整体氛围上怎么感觉 Nicoli 和 Kirill 这俩这么gay来gay去的.... 最后莫名其妙就结束了也给人莫名其妙的感觉…
- 蓝色巨人 Blue Giant 3.5/5 整体还可以，说几个缺点：1. 采访的半回忆手法没有任何实质上的作用 2. 虽然是燃片但是作为电影不应频繁使用燃系音乐，容易导致观影疲劳
- 红猪 3/5 确实年纪大了，现在看这种电影丝毫没有啥感觉了

\#2

周四晚和媳妇儿一起去电影院看红猪，开映前在银泰吃了顿外婆家。

几乎是这辈子吃过最难吃的外婆家...预制菜就算了，预制菜的口感也太明显了...除了酒酿丸子勉强可以接受外其他的完全都毫无预期可言

然后更蛋疼的是红猪确实太一般了，而且导致媳妇儿看了半小时就受不了了...

散场后黑脸了一晚上...

\#3

周五上午一大早跑去浙江眼科医院（凤起路院区）想换副眼镜。

虽然此次验光配镜速度很快，但是主治医生给我的度数做了一点调整：近视度数增加了二十来度，并且把散光减少了二十来度；糟糕的是这个变动没有完全检查是否符合大多数场景

这就导致配镜结束后我戴上眼镜才发现散光度数不够，某些中远距离的原本能看清晰的黑色内容都开始有毛边了....

我那个吐血啊...镜片花了1200多总不能让我重新来吧...

最后只能算吸取一个教训，向下变更最好多重场景反复确认再提交

周五因为这事儿生气了很久，Orz

\#4

本周依然练了两天车，路上开越来越有感觉了，各种道的左右转的判断也明显比之前好，不过对头顶上的道路标识还是需要一定时间学习适应

科二依然磕磕绊绊，还做不到一把完美...感觉确定点位时机和操作结束还是不默契；不过特定角度已经可以慢慢盲打方向盘了...

后面好像就剩下两节课了 😭

## Work

\#1

本周学习

**CppCon 2021 | Our Adventures With REST API in C++ : Making it Easy - Damien Buhl**

- 介绍的这家公司根据自身需求实现的 rest server & http client 的东西，细节可以看 https://github.com/tipi-build/openapipp
- 整体上比较直接但是也没有太 eureka 的点；里面用到了他们自己实现的（考虑到 WASM 执行环境）的 xxhr (http client) 和 pre (反射库）；不过也可以看一下说不定对你有特别收获

**CppCon 2021 | Conquering C++20 Ranges - Tristan Brindle**

- 通过三个例子来逐步引入 ranges/views 的实现，感觉是 cppcon 2020 和 cppcon 2021 迄今看到的最适合我的讲 ranges 的 talk 了

**CppCon 2021 | Using Coroutines to Implement C++ Exceptions for Freestanding Environments - Eyal Zedaka**

- 实现一个基于 coroutine 的异常机制：使用 co_yield 抛出异常；使用 co_return 返回正常值
- 如何实现 memory allocation elision?
    - 简化函数，立即 resume (suspend_never → suspend_always）让函数可以被 inline 进而触发编译器优化
    - use co_return replace co_yield
    - 大招：引入一个 exit_condition，coroutine handle 的操作会设置这个变量，好处是优化变得容易得多可以更容易生成高质量代码，同时可以保留 co_yield 作为 error report 语义，专门针对 error report 做文章，例如最后彩蛋环节的通过 source_location 给错误加 callstack

**Building Microservices | 10. From Monitoring to Observability**

- 核心就是微服务的 observability 相关，如何正确使用 log aggregation, distributed tracing；定义 SLA/SLO .etc 还提到了 monitoring & alert
- 基本是常规的微服务的内容

**HBase原理与实践 | 5. RegionServer 的核心模块**

- 核心就是 HLog, Memstore 和 HFile 三个模块的一些 layout 细节展开
- 随便看看就行…

****Understanding Linux IOWait**** https://www.percona.com/blog/understanding-linux-iowait/

- cpu-intensive 的进程一起运行时会干扰 iowait 对 iobound 程序的统计，导致系统的 iowait 看起来并没有这么高。因为 iowait 是 cpu 空闲时正在等待 disk i/o 的统计
- vmstat 的 column `b` 代表当前等待 disk io 的进程数
- 另外还可以使用 iotop 或者 iftop 来观察，后者用于 network IO

\#2

给 anvil 加了一个 `anvil touch` 命令，自动将 `project.toml` 文件复制到当前目录

以前都没发现没有这个命令太麻烦...

PR 见 https://github.com/kingsamchen/anvil/pull/7

\#3

跟着前面 Tristan Brindle 的 talk 自己写了一遍了 ranges 的 sample code

https://github.com/kingsamchen/Eureka/pull/17

\#4

这周给公司的一个 dlp task 实现策略逻辑时因为不想到处类型擦除用了 `std::varaint` 来存储不同 enum 类型对应的 operator type

同时有一个 `OperatorUnknown` 来对应 enum unknown

用 overloaded trick 实现类型派发时用了如下代码

```c++

using foo_type = std::variant<foo_unknown, foo_a, foo_b, ...>;
using bar_type = std::variant<bar_unknown, bar_a, bar_b, ...>;

foo_type f;
bar_type b;
// ...
std::visit(overloaded{
    [](foo_unknown&&, auto&&) {
        // invalid case
        return nullptr;
    },
    [](auto&&, bar_unknown&&) {
        // invalid case
        return nullptr;
    },
    [](auto&& ff, auto&& bb) {
        return std::unique_ptr<some_base>(ff, bb);
    }
}, f, b);

```

这段代码无法被编译，看了半天都没找到问题在哪，但是把 overloaded 换成 if constexpr 的版本就可以正常编译。

后来突然灵光一闪，是不是匹配上缺了

```c++
[](foo_unknown&&, bar_unknown&&) {
    return nullptr;
}
```

导致的。

添加后果然就编译通过了...

不过我也没翻文档到底这个算不算 expected behavior

---

好了这周就这样，下周见
