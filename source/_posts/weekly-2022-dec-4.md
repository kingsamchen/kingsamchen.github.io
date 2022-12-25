---
title: 一周杂记 in Week 4 Dec 2022
categories: CODE-LIFE
date: 2022-12-25 23:59:34
tags: [杂记]
---

本周是12月第四周，也是倒数第二周。

## Life

\#1

上周周末得了新冠，到周一的时候已经是第三天，但是依然脑子不太舒服，所以又请了一天假。

然后就见到周围同事一个接一个倒下烧的死去活来。

周二开始恢复上班的时候组里加起来好像也就6个人，而且我自身其实也是属于没有完全恢复好的。

所以这一周都是一边恢复状态一边继续休息，整周都可以说是没有啥 productivity。

虽然周三开始没啥明显症状了的，但是会明显感到容易疲劳，工作一会儿就想睡觉...

以我个人经验来看，差不多要整一周时间才能恢复到之前的状态。

另外老婆也差不多从新观众康复了，而且感觉她恢复的比我还快一点，虽然我们俩都没打疫苗。

\#2

以目前的新闻看，不确定是不是中国的疫苗效力问题，Omicron 在中国引起的反应比预期大得多。

这值得后续持续关注

\#3

本周看了两部电影：

- Carnage (2011) 8/10
  cast 很强大，完全是演员自己的表演撑起来的一部电影
- Moon Fall 5.5/10
  就算是科幻电影，也不能这么乱来啊；不过特效确实可以

## Work

\#1

这周把 redis 的 cuckoo filter 的源码看完了。

不过没有和上次 bloom filter 一样自己动手写了一个，主要是这源码看下来感觉这玩意儿不适用。

虽然支持 deletion，但是限制依然一堆，比如不能 delete 一个不存在元素，否则薄记会不对等等..

另外这源码看下来感觉这写操作开销比 bloom filter 也大太多了吧。

对于这玩意儿在工程中的实际效果，我是有点怀疑的。

\#2

本周学习进度

看了俩 CppCon talk

- CppCon 2020 | Taskflow: A Parallel and Heterogeneous Task Programming System Using Modern C++ - Tsung-Wei Huang
  这个主要安利的 TaskFlow 这个 framework。
  其实我之前有点不太理解为什么 LWG 要搞 send/receiver 模型，不直接用 ASIO 就好了，看了这个 talk 我才发现并发/异步编程很多时候不仅仅是网络编程那么简单。
- CppCon 2020 | The Many Shades of reference_wrapper - Zhihao Yuan
  讲的 reference_wrapper。我也是看了 talk 才知道默认 reference_wrapper 赋值是 reference rebind...
  另外作者是华人，也活跃在 Twitter 上

看完了 Software Engineering at Google | 18. Build Systems and Build Philosophy。这章内容还是比较和我胃口的

另外因为大规模分布式存储系统那本书之前看完了，所以这周又翻出 Database Internals | 7. Log-Structured Storage 继续开始看了十来页。

极客时间那个课程就不说了，感觉还是鸡肋。

\#3

另外这周稍微看了一下 Golang net/smtp 的源码。

主要是想改进一下我们 codebase 里那个 smtp client 所以想参考一下。

---

好了这周就这样，下周见。
