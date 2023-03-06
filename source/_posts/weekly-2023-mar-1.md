---
title: 一周杂记 in Week 1 Mar 2023
categories: CODE-LIFE
date: 2023-03-06 00:34:22
tags: [杂记]
---
终于进入了三月份。

## Life

\#1

之前右脚关节附近位置有明显痛感但是又说不上是哪里痛，老婆说弄不好是关节炎，让我这周先好好休息，把跑步停了，免得一直好不了。

谨遵医嘱后这周工作日在没有跑一天，到了周五 WFH 的时候感觉确实右脚好像已经不痛了。

于是周日恢复了跑步了，和老婆一起在健身房练了40多分钟。其中跑步机接近30分钟跑走了4.5KM，因为还处于恢复状态，加上中间胃部总感觉有点不舒服所以速度一直没敢太快，10KM/h 的配速也就坚持了十几分钟就减速了。

有氧练完之后又做了两组共30个引体向上，感觉同样的配重已经明显比之前更能扛了。

不得不说运动出汗是真的舒服。

\#2

本周看了三部电影。

_The Unbearable Weight of Massive Talent_ 7/10 中规中矩，偶有惊喜。难得时隔这么久还能在大荧幕上看到凯奇大叔还算可以的电影。

《正义回廊》 8.5/10 题材比较和我胃口，叙事和节奏稳如狗，导演还加了不少私货。看完之后我才知道原来和之前的《踏雪寻梅》是同一个导演

_Nope_ 7/10 这部电影给我的观感其实不如凯奇大叔的这部，导演对隐喻太执着了，导致故事不好看，老婆怒打二星...

## Work

\#1

之前帮同事查了一次 libmagic 的问题，后来同事给了我另一个 PoC 我发现 libmagic 居然还有其他可能因为 fork/exit 不恰当导致我们服务 hang 住的情况...

不过因为解决起来比较麻烦，短时间内不容易有产出而且不是一个很紧急的问题，所以只能先放放了

\#2

最近和组里另外一个同事接了一个活，要接着苏州那边做不下去的某个服务做一个 SendGrid 的替代品出来。

因为这个服务大多都是网络IO而且业务逻辑也没有太简单，所以我一开始和 lead 讨论后觉得能用 Golang 是最好的，stage 1 可以直接用苏州那边的一部分功能先堆上去，后面再慢慢还。

一开始我们 manager 不同意使用 Golang，后来终于松口了，估计也是考虑到业务进度和形势环境。

不过我真好奇你一个 GOOG 出来的人咋这么讨厌 Golang 呢

\#3

本周学习进度如下

- CppCon 2020 | Building an Intuition for Composition - Sy Brand
  STL 中一些算法的组合（accumulate .etc）顺带给 ranges 打个广告；monad with expected/optional coroutine 甚至也是 monad 的一个延伸应用
- CppCon 2020 | Embedded: Customizing Dynamic Memory Management in C++ - Ben Saks
  嵌入式中通过自定义 operator new/delete 来定制动态内存分配来适配业务
  不过我没有相关经验，所以不好评价

_Database Internals_ 看完了 _14. Consensus_ 至此整本书都看完了。

对于这书的不详细吐槽我也直接在douban上标记了，直接跳过去看即可

_Apache Kafka 实战_ 看完了 _5. Consumer 开发_

_重新理解创业_ 看完了 _1. 重新理解战略_

\#4

这周终于有时间把 esld 的代码都写完了。

自己跑了几个 test cases 都能过了。

实现上各处还有不少可以优化的点，但是因为做了有点久，已经有点烂了，实现至少作为 PoC 级别的东西是可以了。

代码在：https://github.com/kingsamchen/Eureka/pull/3

---

这周就这样了，下周见
