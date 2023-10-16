---
title: 一周杂记 in Week 2 Oct 2023
categories: CODE-LIFE
date: 2023-10-16 21:49:41
tags: [杂记]
---
本周（10/09 ~ 10/15）是十月第二周

## Life

\#1

节后第一周，非常不想上班...

为啥我还不躺平啊....

\#2

本周观影：

Talk to Me 3.5/5 A24 出品的一个惊悚片，我感觉还可以


\#3

周五的时候媳妇儿说投的 SCI 论文这么久了没反应，我还是写封邮件催一下吧，😔

结果到了晚上快9点的时候，媳妇儿很紧张的喊我的名字，然后说论文被接受了。

我愣了一下，然后大叫一声，把她都吓到了。嘿嘿

虽然这篇只有2分作用，但是用来申请 ZJU 的 PhD 应该差不多了，毕竟目标导师就是媳妇儿硕士的导师，还有点 connections。

剩下的就是赶紧把雅思6分搞定了。

爽~~~

\#4

因为周五媳妇儿论文中了，所以周六打算去吃大渔。结果没想到连上了七天班后的杭州大悦城那么多人，大渔门前一堆等位的...

无奈换了一家之前考虑的新疆菜，但是吃下来感觉味道一般...还不如周日中午的 KFC

然后周日下午老妈也来了杭州复查牙齿，估计也要呆个几天吧。不过刚好那几天媳妇儿在西溪值班，刚好错开 😅

周日上午在健身房跑了7KM，练了4x12 27KG 的 shoulder press；晚上又和媳妇儿打了半小时多的乒乓球，感觉一天热量拉满。

## Work

\#1

**[Benchmarking Apache Kafka, Apache Pulsar, and RabbitMQ: Which is the Fastest?](https://www.confluent.io/blog/kafka-fastest-messaging-system)**

- Kafka VS Pulsar VS RMQ 性能和吞吐 benchmark
- 稍微看个结论就差不多了

**[Boost System Error Design Doc](https://www.boost.org/doc/libs/1_81_0/libs/system/doc/html/system.html)**

- 虽然是 Boost 的文档，但是对标准库的 error_code 使用有非常不错的指导意义
- 而且把前因后果都讲到了；后面有时间我写一篇综述的文章总结一下

**CppCon 2021 | Building a Lock-free Multi-producer, Multi-consumer Queue for Tcmalloc**

- 这个 talk 很逗，作者费尽千辛万苦实现了一个 lockfree MPMC queue 想加速 tcmalloc transfercache 部分的 mutex 使用，结果花了一整年踩了N多坑，最后在 google web search 上验证发现还慢了几个百分点…

**凤凰架构 | 5. 架构安全性**

- authentication & authorization 都覆盖到了，而且还提供了一个强度不低的用户在客户端侧创建密码到服务端保存以及后续登陆验证的 case
- 加密和安全传输部分也不错

**Building Microservices | 5. Implementing Microservice Communication**

- 覆盖了 request/response 和 event 两种通信模型，并且强烈推荐使用 explicit schema 作为 payload
- 带了一下 api gateway 和 mesh

\#2

有个负责迁移我们项目开发/运行环境从 almalinux 到 ubuntu 22.04 的同事说在 ubuntu 上生成好多个 executable 的时候会提示 shared-lib A 中找不到 shared-lib B 中的一些符号。

他研究了很久但是一直都没有解决，于是找到我和另外一个同事（suyang）

把问题简化一下大概是这样：

1. shared-lib A 和 shared-lib B 逻辑上互相依赖（别问为什么用后端服务用 shared-lib，也别问为啥会循环依赖...）
2. shared-lib B 构建时显式依赖了 shared-lib A，即 B 的 Makefile 中直接 -lsharedlib_a；但是 shared-lib A 没有显式依赖 shared-lib B
3. executable E 构建中显式依赖了 shared-lib A 和 shared-lib B
4. almalinux-8 上使用 gcc-10 和搭配的 binutils 一直都是可以正常构建的

因为是链接失败，猜测是 linker 的问题。但是查看了一下两个系统用的 linker 都是 ld.bfd，只不过 Ubuntu 的版本要高一些

因为 ubuntu 22.04 用的 gcc-11 支持 `-fuse-ld`，所以生成 executable 的时候试了一下 `-fuse-ld=gold` 成功生成...所以100%就是连接器的问题了

比较蛋疼是老板不同意换 linker，那只能换个方法。

试了一下 `--unresolved-symbols=ignore-in-shared-libs` 能用，但是这玩意儿后劲（副作用）太大，和同事讨论了一下感觉不太适合...

启用我的满级 Google 大法，感觉问题出在 ubuntu 上 ld.bfd 会默认开启 `--as-needed` 把 elf 中的一些 `NEEDED` entries trim 掉导致的。

但是很不幸，我给 makefile 里加了 `-Wl,--no-as-needed` 并没有效果...

折腾了一天多之后本来打算放弃了，后来一不小心发现 Stackoverflow 上有个和我类似的问题：同样循环依赖，同样 ubuntu，同样 unresolved references，而且更神奇的是解决方案就是加 `-Wl,--no-as-needed`...

只不过人家是加载对应的 `-l` 之前。

于是我改了一下顺序把 option 放到了所有 `-l` 之前，重新 make 了一把，居然就成功了...

同事后来恭维了我几句，那会儿可把我高兴坏了 🤪

这里容我装个逼：这问题留给 US 那群高P处理，估计拖到年底 deadline 他们都搞不定，绝对中途就换了一个又屎又烂的方案。

\#3

这周看了 absl/base/internal/errno_saver 源码，这部分很简单，直接没几分钟就看完了。。。

于是想了想，顺带把 abseil base/attributes 源码分析 和 abseil strings/strip 源码分析更新到了 blog。

想想后面还欠了不少文章 😂

---

好了这周就这样，下周见
