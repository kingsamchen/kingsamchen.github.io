---
title: 一周杂记 in Week 1 Nov 2026
categories: CODE-LIFE
date: 2026-11-10 00:31:16
tags: [杂记]
---
本周（11.2 ~ 11.8）是11月的第一周，这周开始天气明显长时间处于低温状态。

## Life

\#1

这周连续遇到蛋疼的坑爹事件。

首先是乐刻双十一年卡续费充错了门店，我也没想到默认会选离你最近的门店而且距离算的是直线距离

结果默认给我选成了实际上离我快3km的店，我人都傻了。

找店长走流程走了好几天才给我退费，然后又找店长买了美团的优惠卡，整体也还算可以吧，1649

另外一个坑爹事件是我买了一箱咖啡胶囊，100 + 18*2 = 136粒胶囊，但是送达之后都第二天了我都没看到这货在哪。

问了菜鸟物流，还要我亲自给他发单号，折腾了半天才告诉我可能送错送到楼下去了

还好我下楼看了一下确实就在楼下那层楼门口，检查了一下东西也没被冒拆开

\#2

因为拳击课就剩下最后一节了，所以我这次干脆一次性续了24节课。

按照一周一节的频率可以上到明年5月份了

而且考虑到这课减去平台抽成，碰到优惠期教练其实到手不多，我和教练商量完，私下单独按照每节课补一定钱给他

而且因为有双十一优惠，折扣算下来其实就比之前一节多了不到20，但是教练自己能多赚不少，整体算双赢

\#3

这周周六的拳击课练的强度太大了，到了晚上皮质醇都降不下来，非常兴奋，一直失眠到凌晨四点 😅

还好周日还有一天休息调整，就是这 HRV 直接猛掉到只有 48，着实有点离谱。

\#4

这周秋宝感觉又学会了不少短语，每天模仿我们说话的样子特别逗。

现在都好奇再过一段时间等她开始能说长句子的时候，每天会是什么状况，🤭

## Work

\#1

**CppCon 2023 | Object Introspection: A C++ Memory Profiler - Jonathan Haslam & Aditya Sarwade** https://www.youtube.com/watch?v=6IlTs8YRne0&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=76

- 核心思路是利用 DWARF 提供的调试信息来做类似反射那样获取指定类型 layout 信息，然后再递归计算获取

**CppCon 2023 | Can C++ Data-oriented-design be Improved? - Ollivier Roberge** https://www.youtube.com/watch?v=s3LvIWhBOtQ&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=77

- 有点虚的一个 talk，演讲者估计还没毕业而且是第一次上这种场合
- 核心是延申 data oriented design 出来一个 transformation oriented design，不过给的是一个 typelist + compile time dispatch 的东西，不是很理解
- 核心代码就在 46:43 那一页，没多少

**CppCon 2023 | Lightning Talk: Help! My Expression Template Type Names Are Too Long! - Braden Ganetsky** https://www.youtube.com/watch?v=YKWBNzjmBvg&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=81

- 有点意思，模板套模板名字太长怎么办，给了一个 msvc 的trick，用一个 non-capture lambda 里的局部 struct 继承
- 不过这个 trick 只能在 msvc 上用；另外名字缩短了编译时间也上去了 😅

**CppCon 2023 | Designing Fast and Efficient List-like Data Structures - Yannic Bonenberger** https://www.youtube.com/watch?v=stfEry0zz5E&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=79

- 本质上是受到 std::deque chunked memory 启发，把 memory pages 链了起来，但是核心也不是为了解决中间插入，而是为 task queue 这种做针对优化

**CppCon 2023 | Embracing CTAD - Nina Ranns** https://www.youtube.com/watch?v=pcroTezEbX8&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=81

**Boost Libraries | TCP/IP Networking with Boost.Asio** https://www.youtube.com/watch?v=tyDWXT8-Ykc&list=PLJDO7P5jAoXznYam4ucdMMcKJky8UHbhC&index=6&t=2300s

- 展示传统的 asio asynchronous with callback 如何设计并实现一个 nntp server/client
- 还是值得一看

\#2

这周给 fawkes 做了如下的更新：

https://github.com/kingsamchen/fawkes/pull/50

- Set connection-close once stop is requested
- Fix cancellation_signal potential use-after-free
- Rejects malformed requests
- Lower log level for timeout connections to reduce noise

以及 增加了更多的 end-to-end tests

https://github.com/kingsamchen/fawkes/pull/51

因为把很多 end-to-end tests 转成了 Python 实现，感觉可操作性和效率都有大幅提升，对 AI 来说也更加契合了。

---

好了这周就这样，下周见
