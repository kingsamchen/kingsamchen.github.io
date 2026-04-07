---
title: 一周杂记 in Week 1 Apr 2026
categories: CODE-LIFE
date: 2026-04-07 14:13:54
tags: [杂记]
---
本周（03/31 ~ 04/05）是四月份第一周，夹杂清明假期。

## Life

\#1

周四上午是最后打包时间，公司搬家，后续接近一周半都WFH

因为有几天开发机不能用了，所以把 vagrant vm 打成 vbox 弄到公司发的笔记本上用，折腾了老半天，感觉性能还是一般，尤其不知道是不是 crowstrike 搞的鬼，编译的性能极差。

所以活差不多干完就不弄了，黑工也算了，搞起来太难受了。

\#2

周六清明假期第一天早上开车送我妈去车站，她回老家扫墓

假期第二天周日傍晚岳母才来，期间是我和媳妇儿两个人带秋宝，讲道理是真的挺累的，尤其这个年纪的宝宝特别调皮，要一直盯着。

每次最幸福的时候就是秋宝睡着的时候 🤡

所以这次假期只有第三天丈母娘在的时候才有时间去健身房稍微练了一会儿

哦，一个比较好的消息是这周的 HRV 有不错的上升势头。

\#3

周三观影俱乐部，最后一次了。下周开始上班 iker 就已经不在公司了 🤣 所以这次观影人数稍微多了那么1-2个

- **共同的语言 Une langue universelle 4/5** 你还别说，这种相对松散，剧情上不那么刻意的电影还有点意思；尤其最后男主看望许久未见已经老年痴呆的母亲的时候，身份转换的切镜点睛之笔

## Work

\#1

**CppNow 2025 | Declarative Refactoring for the Masses in C++ - Andy Soffer** https://www.youtube.com/watch?v=bgDZ0L_W3sU&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=47

- 有点欢乐的 sharing session，这家公司主要的东西是可以在的代码里加一些 DR_X 的 annotation/statement 来做高层次的代码重构
- 并且他们用的是非常 c++ like 的 DSL

**Compiling at Compile Time with Daniel Nikpayuk - CppCast Ep405 - C++ Weekly Ep 525** https://www.youtube.com/watch?v=JJjBQ95e28s

- 被采访的哥们做了一个 CCTP 编译期的一个计算库，还讲了这哥们是怎么开始走上 cpp 开发者道路
- btw，这哥们是因纽特人

**C++ Weekly - Ep 167 - What Is Variable Shadowing?** https://www.youtube.com/watch?v=L0YG2x7E87w

- 开 `-Wshadow`

**C++ Weekly - Ep 166 - C++20's Uniform Container Erasure** https://www.youtube.com/watch?v=YGAX509TCQs

- 开始提供 `std::erase()`，降低使用 remove-erase idiom 的门槛
- 注意这个函数的入参是一个 container ref，因为用 iterator 是没法真的删除元素的，并且这个函数是做在每个容器的头文件里，提供了一个 non-member overload
- 并且如果容器自己有 erase，会调用容器的 erase

**C++ Weekly - Ep 165 - C++20's is_constant_evaluated()** https://www.youtube.com/watch?v=nkhhV5uSSLk

- 正确用法

    ```cpp
    constexpr T foo() {
      if (std::is_constant_evaluated()) {
        // picked when the foo() is invovked with constexpr result
      } else {
        // picked otherwise
      }
    }
    ```

- 另外，能用 `if consteval` 就不要用这个

**C++ Weekly - Ep 164 - Adding a Random Device To The C++ Box** https://www.youtube.com/watch?v=1WAn2v1Hr6A

- Jason 自己之前写的某个项目增加一个 random number 的 use case

\#2

假期因为开发机还无法就绪所也没必要抽空打黑工，笔记本跑 vagrant/virtualbox 实在性能太低了，徒增烦恼。

于是抽了空给 fawkes 一些类加上了 move-able semantics，以及一些类本身支持的加了 static_assert 的 nothrow move-able type assertions。

然后就顺手把项目的语言标准切到了 c++ 23，这一步没啥问题，但是把 clang-22 支持加上之后花了不少时间处理一些坑。

第一个是 clang-22 开始把 `__COUNTER__` 这种认为是 c20y 的 extensions，使用了就给 warning...于是用的 doctest 直接就[寄了](https://github.com/doctest/doctest/issues/1042)。

还好他们修的也快，直接把 vcpkg 的依赖升级到 2.5.0 就好了

第二个是 clang-tidy 升级之后又查出一些新的"issues"：

1. 现在会把 un-namespaced symbols 标记出来，推荐你塞进 anonymous namespace 做成 internal linkage。
  ok，这个还能理解，而且比较少，改一下就行
2. 增加了 `cppcoreguidelines-pro-bounds-avoid-unchecked-container-access` 这个 check
  然后就炸了一堆，因为但凡它推断不出来安全的 `[idx]` 的使用都会标记出来，让你换成 `.at()`，哪怕你结合代码上下文能确认这个访问是安全的
  讲道理在业务代码里用 `.at()` 我还能理解甚至我觉得也是个好实践，但是我这是基础库啊...
  最后直接把这个 check 给 supress 了

第三个相对更麻烦一些

`std::bind_front()` 的一处用法产生的产物在 clang-22 下会通不过 `boost.asio::co_spawn()` 的 completion_token 的 concept 检查

而且这个东西我本地一开始还没发复现，最后是给本地装了 libstdc++-dev-14 才可以复现，至于为啥我也不懂...Orz

尝试了半天发现绕不过去，没有 workaround，于是索性直接把这处改成了 lambda，问题解决...

不过讲道理 lambda 其实对于 move-init capture 更加清晰一些，但是我觉得这里用 `std::bind_front()` 代码看起来更优雅

整体 PR 见 https://github.com/kingsamchen/fawkes/pull/26/changes

有一说一，升级 toolchain 确实不是什么小活，尤其对 cpp 来说

---

好了这周就这样，下周见
