---
title: 一周杂记 in Week 3 Feb 2023
categories: CODE-LIFE
date: 2023-02-20 22:00:25
tags: [杂记]
---
本周是二月份平平无奇的第三周。

## Life

\#1

裁员的余波未去，hz 组里的同事本周感觉都不是很有积极性，包括我。甚至都想直接躺平了。

倒是US那边因为很多人怕会有身份问题，好些个打了鸡血一样堆MR...

\#2

本周有空看了两个电影。

- Gray Man 6.5/10
  纯动作，剧本还算流畅，cast 也还不错，但是这剧本整体上感觉就是 AI 写的，什么时候有爆点，什么时候该转向...

- 大明劫 8/10
  和媳妇儿一起看的家庭影院，为了看这电影还开了一个月的B站大会员...
  不过电影整体质量还可，虽然质感上差一点，但是没有特别明显的短板。而且剧本比较和我胃口。有意思的是我在豆瓣写的短评：“远看是疫病，近看是经济，拆开来再看是政治。你最好讲的真的是大明”。过了几分钟之后发现标记的广播被删了....

## Work

\#1

本周学习进度有点超出预期。

_Database Internals_ 看完了 _12. Anti-Entropy and Dissemination_ 并且 _13. Distributed Transactions_ 也看了十几页，下周估计整本书就能看完了。到时候有时间的话我再来吐槽这本书的问题。

《Apache Kafka 实战》看完了 《4. Producer 开发》，并且搭配作者的课程《Kafka 技术实战》也看了几个 course。

Post 上看完了 _@Design of a Modern Cache_。这篇总结一下就是如何给简单的 LRU 带上 frequency 的考虑。

CppCon 2020 看了三个 Talk:
- _The Future of C++ Parallel and Concurrency Safety Guidelines - Michael Wong & Ilya Burylov_
  这个 talk 主要讲 styleguide 和一点点的 guidelines，所以没有我一开始想的那么有干货
- _Closing the Gap between Rust and C++ Using Principles of Static Analysis - Sunny Chatterjee_
  MSVC team 的介绍 static analysis 的一些 rules。其实现在 c++ 各种静态分析已经比以前强大了不知道多少倍了，但是实践中有多少人真的会开会去约束自己就是另外一回事了。
- _Test Driven C++ - Phil Nash_ Catch 测试框架的作者介绍 TDD
  不过我个人不是非常感冒 TDD，主要是觉得要遵循的这个 TDD cycle 太低效了并且很容易转移设计的注意力。

\#2

公司去年10月份开始强制提交的代码需要上 gpg 签名，但是工作中时常遇到跑完 clang-lint 之后直接提示签名失败。

这周被这个问题连续搞得很烦，所以研究了一下，发现根本原因是接受用户输入的是另外一个程序 pinetry，这玩意儿有默认的超时时间，而且不是很长...

解决方案：

```shell
cat<<EOT > $HOME/.gnupg/gpg-agent.conf
pinentry-program /usr/bin/pinentry-curses
pinentry-timeout 86400

EOT
```

\#3

这周开始写 try-esld 这个小 demo。

目的是自己想一下如果当初没有 libpsl 的话要怎么利用 public suffix list 来实现 effective tld / sld 的查询。

中间遇到一些犹豫不决的“想法/问题”，所以估计还要写几天。

---

好了这周就这样，下周见
