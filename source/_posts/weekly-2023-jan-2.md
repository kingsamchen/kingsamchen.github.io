---
title: 一周杂记 in Week 2 Jan 2023
categories: CODE-LIFE
date: 2023-01-16 19:23:59
tags: [杂记]
---
本周是1月份的第二周，春节节前一周。

## Life

\#1

本周依然 work from home。

周中的时候和行政 manager one-one，基本就是谈年终的问题。

因为之前就知道了这次年终奖会有系数，并且个人对本来就没多少钱的年终奖不是很在意，所以对最终分给我的也没多少期望。所以最后 manager 和我讲的时候反而感觉还行。

中间还顺带问了 manager 本人对 23 年他负责的一直是公司主要盈利业务的看法。

首先很感谢 manager 能够如实以告他的真实想法。其次 oneone 结束之后周五压着窗口期最后一天美国经济数据也确定的点，直接把手头的 RSU 清了...

虽然统计上的亏损减计有点显眼，但是这个时候不卖估计今年会窥得更惨...

\#2

这周把美剧 Terminal List 看完了。

感觉还行，但是解决好兄弟 Ben 的反转真的是太生硬且毫无必要。

好在整体质量不错，尤其动作戏非常惊艳，我能给 8/10。

## Work

\#1

本周学习总结：

- 《Apache Kafka 实战》看完了 1.认识 Apache Kafka 和 2.Kafka 发展历史
  是的，我开始认真的学习 Kafka 了。
- 同时书的作者在极客时间上有个 Kafka 的课程《Kafka 技术实战》内容其实和这本书差不多，但是比较新，所以刚好可以用来对比
- Database Internals 看完了 _9.Failure Detection_
  这书的问题后面打算看完后专门吐槽一下；这一章真的是太“学术”了，ping/pong 和 heartbeat 都严格区分了...

---

把 CppCon 2020 的 _Template Metaprogramming: Type Traits (part 2 of 2)_ 看完了。

这个作者的两个 talk 确实非常适合新手入门

---

之前 weekly posts 的 _Futures for C++11 at Facebook_ 也花时间看完了。

内容不多，但是整体上属于循序渐进的介绍 folly future

\#2

花了点时间给 esl 加了两个 file-util 性质的函数：`read_file_to_string()` 和 `write_to_file()`

读文件其实并没有一开始想的那么 trivial：

1. 之前写 Lumper 的时候就发现某些特殊文件，比如 `/proc` 目录下的，常规方法获取到的 file size 都是 0...所以对这种文件只能硬读，读到 EOF 为止
2. read buffer 怎么设置才可以既不浪费，又可以少几次读操作，毕竟地下是真的 syscall
3. 因为想做一些 fine control，所以没有用 fstream 的东西而是直接 fall back to C file ops，结果发现不同函数的错误汇报是不一致的

总之回头要有时间打算写篇 post 讲一下

写文件就比较直接了，也没啥绕绕，就不展开了。

\#3

公司方面的任务好像没啥好讲的，除了知道了远古的 SMTP 协议的 RFC 支持一个叫 source routing 的东西。

比如 RCPT TO command 的 mailbox 部分可能会出现 `@foo.com,@bar.com:test@foobar.com`

不过因为 DNS MX 推开之后基本没人用这个了，所以只要不是 pre-historical 的东西都已经不支持了。

连 Postfix 这种二三十年前的也明确说自己不支持这个，routing part 会在 trivial-rewrite 的时候会被 strip 掉。

---

好了这周就这样，下周见
