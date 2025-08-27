---
title: 一周杂记 in Week 3 Aug 2025
categories: CODE-LIFE
date: 2025-08-25 23:20:09
tags: [杂记]
---
本周（08/18 ~ 08/24）是8月份的第三周，这周依然是真的热啊

而且这周依然要忙成傻逼了

## Life

\#1

这周我妈来了杭州，除了把家里的窗帘和纱窗清洗了一遍之后周中还抽了两三天跑到萧山奥体那边的媳妇儿家看孙女。

中间和老婆考虑下周是不是先把娃接到拱墅这边来，给丈母娘放个假；不过后来一起考虑之后觉得娃还太小，长时间开车和突然换环境可能会有意料之外的风险，于是作罢。

周末两天还是和我妈一起在媳妇儿家带娃。

娃这周又长得飞快，不仅吃的更多了，而且隔三岔五的自己尝试翻身，不过暂时因为肌肉力量不足所以还没有成功。

每次失败这娃都会哇哇大叫，我媳妇儿说臭脾气和我一样 🤣

因为暂时不需要我妈亲自来带，加上外婆肠胃这几天又有不适，所以给我妈买了下周一回老家的车票

\#2

周二晚上请几个 xdm 吃人均300的湘菜

讲道理除了剁椒鱼头和一个泉水牛肉确实值人均300之外，其他菜我觉得和人均50的湘菜馆没太大区别。

主要还是因为之前吃了太多浙菜，有湖北和湖南的同事说想换换口味 🤔

\#3

这周太热了，周中选了一天高温居家，然后周三的时候和观影俱乐部的同事约好一起去电影院看《捕风捉影》

- **捕风捉影 4/5** 坏消息：超级缝合怪 好消息：缝得还不错 大概是成龙最近N年来最好的作品了。美中不足的是结尾强行show张子枫和跟踪队逻辑减分，虽然加了一个影子重伤的铺垫但是很显然对这些菜鸡还是战力碾压的，所以包围圈压根就不成立。另外此沙看起来有种金城武平替即视感…

## Work

\#1

这周太忙了，只有时间快速过一下 C++ Weekly，posts 和 cppcon 的一点都没过

**C++ Weekly - Ep 294 - Hello Commander X16** https://www.youtube.com/watch?v=JVoBZA2u2eM

- 介绍一个 commander x16 的模拟器项目

**C++ Weekly - Ep 295 - API Design: Principle of Least Surprise** https://www.youtube.com/watch?v=Qs4nje3KaFw

- 有人建议把 Jason 之前写的 xoroshiro 的 fork 函数改成构造函数的实现，但是这样会混淆拷贝构造，因此接口上这个不是一个好设计。

    用代码表示就是

    ```cpp
    // Copy constructor
    constexpr xoroshiro128(const xoroshiro128& other) = default;

    // The other semantic
    constexpr xoroshiro128(xoroshiro128& other) {
      // use other's state to create/initialize this.
    }
    ```


**C++ Weekly - Ep 296 - Constraining `auto` in C++20** https://www.youtube.com/watch?v=A8nNjpaiP5M

- 利用 concept 来描述/限制 auto 到底对应哪一类东西
- 语法上是  `[concept-trait] auto [var_name]`

**C++ Weekly - Ep 298 - Detecting ABI Changes With abidiff** https://www.youtube.com/watch?v=GkB3TgkAN0M

- 介绍 abidiff 这个工具，能 diff  两个 executables 的 ABI 情况
- 这个工具隶属于 libabigail，如果后续遇到需要 ABI 的场景可以看看这个 repo 有没有什么好玩意儿

**C++ Weekly - Ep 493 - C++ GUI Quick Start with FLTK** https://www.youtube.com/watch?v=hFreBEUmumw

- FLTK 是一个跨平台的非常简易（简陋）的 GUI 框架

\#2

这周没什么时间的一个很大原因就是在写 spdlog 的一个 wrapper

这个主要是给公司的 codebase 用的，因为公司的产线日志必须要做 classification 以避免 PII 数据被权限不足的工程师看到（比如中国工程师 🤷‍♂️）

之前用的 glog 很难做到新的要求，而且使用体验会很差，所以这次选择基于 spdlog 做一个 wrapper

一个类似的 demo PR 可以见：https://github.com/kingsamchen/Eureka/pull/47

\#3

在写 spdlog wrapper 的时候遇到一个问题

```cpp
struct foobar {
    std::string_view sv;

    explicit(false) foobar(std::string_view sv) : sv(sv) {}
};
```

但是下面代码会编译失败

```cpp
// Candidate constructor not viable: no known conversion from 'const char[4]' to 'std::string_view' (aka 'basic_string_view<char>') for 1st argument
// foobar f = "abc";
```

解决方案是用 ctor template 搭配SFINAE（C++20的话用concept）

```cpp
    template<typename String,
             std::enable_if_t<std::is_convertible_v<String, std::string_view>,
                              int> = 0>
    explicit(false) foobar(const String& str) : sv(str) {}
```

---

好了这周就这样，下周见
