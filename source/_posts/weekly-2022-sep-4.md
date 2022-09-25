---
title: 一周杂记 in Week 4 Sep 2022
categories: CODE-LIFE
date: 2022-09-25 23:10:44
tags: [杂记]
---
本周是九月第四周，下周周末就是十一长假了。

## Life

\#1

这周最大的事情是媳妇儿在职研究生开始走毕业答辩的各种流程，周一帮她在公司打印了十来份的资料。

最后打印出来的文档叠起来感觉都有半本小书那么厚了。

并且流程中最蛋疼的是她的女导师一个登陆系统确认送审的操作拖了一周都没给她弄。考虑到下周三就截止了所以她周日直接问她导师能不能告诉她身份证（后六位是学校系统的登陆密码）她来完成这部分流程。

还好她导师这方面还算爽快，最后媳妇儿用了十五分钟就把卡了一周的流程走完了...

因为这事老婆说她这几天基本每晚都没睡好...以及以后读博士的话再也不选女导师了。

\#2

这周五组里搞了一个“小团建”，在黄龙某个看起来比较高端的饭店吃了一顿。

说是小团建是因为用的是欢迎新人的经费，而且因为周五有些同事不方便参与，所以不是组里所有同事都参加。

\#3

这周终于下单了乐歌的升降台，周四一到就把工位做了一个改装，正是体验了一把站立办公。

不过实话说站立办公确实太考验双腿了，尤其需要一双比较舒适并且最好有软垫的鞋子 🤪

\#4

这周没看电影和美剧...这部分跳过。

## Work

\#1

因为下周就要 internal beta 了，所以这周我们的产品是连着 client 正是走到了 QA 测试。

恰好这周又是我 oncall，所以基本是一边看 kibana 上是不是有 E2E Tests 挂了以及遇到 clang-tidy issue 还要抽时间修；同时一边给 QA 看问题。

希望下周 internal beta 开始不要出现什么大问题...毕竟马上就开始十一了不是...

\#2

本周学习的进度如下：

- 开始了新的极客时间课程 _趣谈网络协议_，本周刷了5节课，感觉都是一些 overview/introduction 的东西...
- 看完了 _A Note on Distributed Computing_ 这篇 paper
  这篇的主题是 local computing 和 distributed computing 有着本质的区别，无法找到一个模型可以将二者结合。
  其实这篇稍微过一下就行了，讲的也有点啰嗦。我觉得最核心的还是直到 distributed computing 本质上具有 partial failure 这个最鲜明特征就行，其他啥 memory access pattern, latency 啊相比下都是次要的
- 看完了 _Database Internals | Chapter 6 B-Tree Variants_
  没啥好说的，除了 conclusion 部分比较有印象之外中间的内容基本是看了就忘
- Talk 方面看了 _CppCon 2020 | 40 Years Of Evolution from Functions to Coroutines - Rainer Grimm_
  实话说有点失望，因为冲着 coroutine 来的，结果没多少内容。而且这个 video 还没有AI字幕，演讲者口音又有点重
- _大规模分布式存储系统 | Chapter 5 分布式键值系统_
  看了一半了；不过这章也比较少

---

本周就是这样，下周见
