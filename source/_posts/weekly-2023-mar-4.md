---
title: 一周杂记 in Week 4 Mar 2023
categories: CODE-LIFE
date: 2023-03-27 22:36:20
tags: [杂记]
---
本周（03/20 ~ 03/26）是三月份的第四周，所以下周还有一周才能到四月份。

## Life

\#1

因为上周末回了趟老家参加发小的婚礼，所以周一休了一天假在家里躺躺。

但是因为我妈来杭州补牙齿，所以周一也在家里，就搞得休假不是很惬意，懂得都懂

\#2

这周又看了三部电影

- 秒速五厘米 7/10 周三电影俱乐部看的。年纪大了不是很能get到点
- Smokin Aces 7/10 Cast 很强大，但是剧本节奏一般...
- 不止不休 6.5/10 周六晚上和同事一起看的，主题还行但是戏剧张力一般

## Work

\#1

抽了点时间改进了一下 esl 的 scope guard，主要是允许 `ON_SCOPE_SUCCESS` 调用的 task 能抛出异常。

不过这意味着整条链路的 exception spec 都要调整，导致搞了一堆的 tidy issues...有些实在不能解决的直接被我 `// NOLINT` 了

PR 地址：https://github.com/kingsamchen/esl/pull/6/files

\#2

之前在公司项目里接触了 short-uuid 之后想着抽个时间给之前的 uuidxx 也做个支持，于是首先做的是把 uuidxx 的 CMake 工程文件，clang-format 和 clang-tidy 都休整了一下。

这个算是个大工程吧。因为 uuidxx 自身用了很多 low level memory operations，所以默认的 tidy issue 有点严格一直在 complain...

费了老大劲才把这个做完。

PR 地址就不贴了，纯粹苦力活

\#3

上面把工程修缮好就该写实现了，于是我对着别的语言的实现研究了一下 short-uuid 的算法，发现这个标准算法真坑爹...

总结一下就是：

1. 首先生成一个 uuid，可以是任何版本，通常来说是 v4 即随机数版本
2. 因为 uuid 的 raw data 是 128-bit（也就是俩 `int64_t`，所以 short-uuid 要做的就是对着 128-bit data 做 base-57 encoding。

这个 base-57 是作者自己拍脑袋想的，其实就是 base64 字母表里去掉 URL 不友好的 `/` 和 `=`，然后再去掉 `1` `l` `I` `O` `0` 这几个容易误判的字符，剩下的字母表大小就是57.

坑爹地方来了：不同于 base64 的 64刚好是2的整次幂，57可不是，所以没法简单地通过位移运算来编码，相应的，得将 uuid raw data 作为一个 128-bit 大整数，不断对 57 取模....

所以就需要大整数运算的支持。

原作者因为用的 python 天生支持大整数啊...Golang 的版本用的自带的 math/bigint

虽然 C++ 也可以用 3rd 大整数或者干脆用编译器扩展的 128-bit 运算扩展，但是我总觉得这个对于一个定位小巧的 lib 来说有点离谱。

如果是一个大项目的基础库还是可以搞搞的，单独的库就算了吧。

\#4

本周学习进度如下

- 《重新理解创业》 看了
    - 3. 重新理解品牌
    - 4. 重新理解流量

- 《Apache Kafka 实战》看完了
    - Chapter 6
      这一部分都是一些实现细节，可以看看增加点见识

- CppCon 2020
    - Heterogeneous Programming in C++ with SYCL 2020 - Michael Wong & Gordon Brown
      干货不多..

- Posts
    - incast [https://www.pdl.cmu.edu/Incast/](https://www.pdl.cmu.edu/Incast/)
    - TCP incast: What is it? How can it affect Erlang applications? [https://github.com/kingsamchen/footnotes-of-linux-multithreaded-server-side-programming/blob/master/tcp_incast_what_is_it.md](https://github.com/kingsamchen/footnotes-of-linux-multithreaded-server-side-programming/blob/master/tcp_incast_what_is_it.md)

---

好了这周就这样，下周见
