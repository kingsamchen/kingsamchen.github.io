---
title: 一周杂记 in Week 3 June 2026
categories: CODE-LIFE
date: 2026-06-22 22:35:48
tags: [杂记]
---
本周（6.15 ~ 6.21）是6月份的第三周，这周雨水至少占了一半时间。

## Life

\#1

周一早上因为电瓶车电量不够所以开车去上班，在一个堵塞的桥上碰到一个强行变道的傻逼司机，最后我没让他车右后视镜和他的左后视镜蹭到了。

因为之前也出过类似的情况最后浪费了半天就让对面出保险修了400多的后视镜，所以这次我打算就算了，结果这个傻逼司机拿着老虎钳就下车冲到我车前在骂骂咧咧。

我当时内心直接升起了拿出实心甩棍下车干他的冲动，最后还是控制住了。

最傻逼的是我那行车录像去警察叔叔举报加塞未遂，对面隔了一个工作日后直接回复说没看到加塞...真的是妈的智障

\#2

这周五是端午节，所以周四是最后一个工作日，感觉一周只上四天班的状态是最好的 🤔

不过因为进入了梅雨季这周一大半时间都是在下雨，不下雨的时候也是闷热的出奇

周日去拳击课路上刚开始下毛毛雨倒还好，练到中途直接大雨，想了一下干脆结束再上个团课好了。

还好团课上到一半真的雨停了天晴了，而且一直到结束都是晴天。

周六晚上临时下单给秋宝买了一个学步车，那个说明书写的不太行，和IKEA的没法比，费劲巴拉才拼起来。

结果秋宝只有周六晚上刚拼好那会儿玩的比较久，周日加起来总共没玩多少分钟...

\#3

周二和同事还有 iker 约了嘉里中心的火遮眼，所以干脆也开车去上班好了。

嘉里中心停车费还是贵的，2个多小时花了45，都赶上50的票价了

- **火遮眼** 3.5/5 没预期的好，文戏有点拉跨就不说了，关键这动作戏有点为打而打了，你要救女儿直接上武器啊，没有热兵器冷兵器也行啊，前面都知道用锤子后面真就空手...作为一个撸了几年铁最近一年练拳的人表示，这么打非常非常非常累人的，效率也不高还容易力竭被反杀。隔壁伸冤人就正常多了，能用一颗子弹解决的坚决不多用拳头

## Work

\#1

**CppCon 2023 | Back to Basics: Functions in C++ - Mike Shah** https://www.youtube.com/watch?v=CpHX1Du5R0Q&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=44

- 面向新人的内容，有经验的话跳着看都行
- 不过实话说对新人确实有好

**CppNow 2025 | Declarative Style Evolved - Declarative Structure - Ben Deane** https://www.youtube.com/watch?v=DKLzboO2hwc&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=38

- declarative style 可以减少熵值，然后下面是作者在 intel 做一些嵌入式开发的经验
- 测试上尽量不要使用 mock，推崇 arrange-act-assert 的套路，而且多用 compile-time polymorphism + 构建系统的 wrapper 也是可以方便的用 declarative style 定义 target
- 后面不管是 logging 还是 concurrency 都用了 global api compile-time injection 的 trick，缺点是用不对容易导致 ODR，优点是 compile-time 并且 flexible
- 用 S&R 做 concurrency operations
- 最后还提了一下那个既能打印值，又能打印 type 的 `CXVALUE` 宏

\#2

公司的 cursor 订阅本周就结束了，后面用不了 cursor 了，只能在 vscode + codex 插件耍耍了

cursor 目前还能用的 500 requests 不限模型是真的太赛博菩萨了，估计以后也不会有了

\#3

因为下周 scheduler 就要用新的构建做产线的部署了，所以这周特意留意了一下 dev 环境的情况，还真注意到了一个 httpd worker process 退出的时候因为全局对象析构时机不确定导致全局对象A被析构之后全局对象B还会试图去访问它，导致 segfault

通过和AI的亲切交流之后可以利用 httpd 提供的一个机制，在 worker process 退出前主动停掉活跃的B对象，这样及时A已经被析构了，B也不会去访问它。

插曲：让 sch 的 oncall 去修这个问题他一开始直接用 cursor 写了一个非常离谱的但是确实也能用的实现，但是我实在看不下去和对面解释了一下，最后改出来的版本算是勉强正常了一些。

后续部署到 dev 之后确实再没有 coredump 了

后面就等下周正式环境部署了。

---

这周就这样，下周见
