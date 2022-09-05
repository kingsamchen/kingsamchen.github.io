---
title: 一周杂记 in Week 1 Sep 2022
categories: CODE-LIFE
date: 2022-09-05 22:44:57
tags: [杂记]
---
## Life

\#1

上周的体检报告这周出来了，找媳妇儿帮我看了一下报告明细，媳妇儿和我说讲，虽然血脂比起往年有好转但是还是很高危，尿酸依然很高，并且今年的心电图上看，我的心脏有心肌缺血的征兆...

这话给我直接看傻了。

所以最近开始从严的饮食调整，每天晚上不吃饭，支持纤维多热量少的水果、水煮蛋和牛奶；中饭也控制摄入，避免以前十分饱的状况。

另外一个就是提早入睡时间，调整生物钟。当然有氧运动还是要继续保持，甚至加大频率。

预期三个月后再去老婆医院做一次简单的生化和心电图。

\#2

给次次卧（老婆书房）准备的床和床垫本周都到了。

这个床和床垫是老婆自己挑的，主要是因为主卧对着前面的工地，早上施工很早，非常影响睡眠，所以老婆买了小床把书房也改成起居室。

IKEA 的东西好处是可以自己拼，所以周日和老婆一起拼了一个多小时...虽然只有0.9M宽的床，但是拼起来可以点都不省事...

好在床整体还算比较舒服。

现在屋子里有三张床了：主卧1.8m的大床，次卧1.5m的中床和次次卧0.9m的小床。

\#3

本周抽了时间自己看了 everything, everywhere all at once

我觉得这个翻译成《妈的疯狂宇宙》更好。

看到最后发现是家庭教育片....

\#4

周四的时候同事说同层楼有同事体检诊断出了肺结核（早期）。

一开始的时候将信将疑，后来大群发了才坐实。

不过问了老婆说不用太过担心，所以就没放在心上了。

\#5

周五晚上我的微信账号又双叒叕被封禁了24小时，而且连带一个群也直接给干没了...

能说啥呢？

## Work

\#1

这周给 esl 加上了一个 byteswap 的小 util

起因是之前写 topt 实现时候需要一个字节序转换的，想着在 C++ 23 摊开前还是有必要先自己用用。

一开始是打算直接用 kbase 的 endian_util，后来一想觉得之前实现得不太对，因为要站在 endian 的角度去做的话，还要考虑当前架构师 little-endian 还是 big-endian；这样一来就大大的搞复杂了。

所以最后索性也不做 endian 了，就简单地做一个 byteswap 地封装就行了。

\#2

本周知识学习进度

- **_CppCon 2020 | Empirically Measuring, & Reducing, C++’s Accidental Complexity - Herb Sutter_**
    我觉得 Herb Sutter 的这个想法很好，但是我目前对这个提案不是很感冒。虽然确实能简化参数传递的复杂度，但是隐藏了类型总感觉不够 consistent
- **大规模分布式存储系统 | Chapter 4 分布式文件系统** 看完
    对 GFS, TFS 和 Haystack 的架构有了一个整体的感知。讲道理，我觉得这书对于东西的 introduction 做的比 database internals 要好多了
- 两篇文章 [Accelerated Connection Retry for HTTP and Firefox](https://bitsup.blogspot.com/2010/12/accelerated-connection-retry-for-http.html) 和 [从一次经历谈 TIME_WAIT 的那些事](https://coolshell.cn/articles/22263.html)
- 还有一篇古早的论文 [accept()able Strategies for Improving Web Server Performance](https://github.com/kingsamchen/footnotes-of-linux-multithreaded-server-side-programming/blob/master/acceptable_strategies_for_improving_web_server_performance.md)
    所谓的 accept strategies 实际上就是 listen fd 有活动事件的时候是一次通知只调用一次 `accept()` 收割还是调用多次甚至一直到返回 `EAGAIN`
    不过论文还是以定性程度为主，即：不同的 accept limit 对性能有影响，且几乎所有时候 K > 1 的 accept limit 带来的是改进。不过具体 K 应该用多少最好，论文没有给出

---

本周就是这样，下周见
