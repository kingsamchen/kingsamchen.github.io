---
title: Monthly Read Posts in Feb 2018
categories: PROGRAMMING
date: 2019-03-02 15:46:47
tags: [http, http2, c++, goroutine, database, code review, unicode]
---
## Network

[Designing Headers for HTTP Compression](https://www.mnot.net/blog/2018/11/27/header_compression)

看起来是 HTTP/2 的 header compression 使用建议

---

[How to Think About HTTP Status Codes](https://www.mnot.net/blog/2017/05/11/status_codes)

HTTP status codes 使用建议。

适合结合 Rest API design 相关文章一起食用。


## Programming Languages

[论golang Timer Reset方法使用的正确姿势](https://tonybai.com/2016/12/21/how-to-use-timer-reset-in-golang-correctly/)

golang 这破 timer 坑真多

---

[Guaranteed Copy Elision](https://jonasdevlieghere.com/guaranteed-copy-elision/)

C++ 17 Guaranteed copy elision explained.
<!-- more -->

---

[Goroutine Leaks - The Forgotten Sender](https://www.ardanlabs.com/blog/2018/11/goroutine-leaks-the-forgotten-sender.html)

两个常见的 goroutine 泄露的例子。

A kind advice: any time you start a Goroutine you must ask yourself:
- When will it terminate?
- What could prevent it from terminating?


## Database

[MYSQL的SQL性能优化总结](https://mp.weixin.qq.com/s/PbtikNJlGmsR2iLSIA4aMw)

可以稍微看看，个人对互联网产品的 db 使用也出在学习中。

---

[Database 101](https://thomaslarock.com/2018/07/databases-101/)

Fundamental ideas of relational dbms and non-relational dbms.

ACID V.S BASE

Choose from CAP

## Data Structures

[I've been writing ring buffers wrong all these years](https://www.snellman.net/blog/archive/2016-12-13-ring-buffers/)

第三种方案实在是太 tricky 了，依赖 unsigned integer overflow to wrap around，而且 capacity 限制也必须是2次幂。

通常情况下第二种方案可能会更好，性能这种东西没有确切的 benchmark 太虚了。

## Misc

[What is an Incident Postmortem?](https://www.pagerduty.com/resources/learn/post-mortem-incident-report/)

An effective postmortem should include:
- a high-level summary of what happened
- a root cause analysis
- steps taken to diagnose, assess and resolve
- a timeline of significant activities
- learning and next steps

虽然这篇 post 有打广告的嫌疑，但是内容还是足够可以的。另外要推荐 Google 出的 SRE 那本书，其中有几个章节主讲 incident management 和 writing postmortem。

---

[How To Speed Up The Code Review](https://sergeyzhuk.me/2018/12/29/code_review/)

核心观点: _the larget the request is, the less valuable is its review_.

这里的 _**large**_ 尤指心智负担。

所以做法是，不要混合不同 category 的提交到一个 pull request，因为不同 category 的提交需要的专注度和目的不一样。

不过按照个人经验要做到这个目的并不太轻松，在公司内部团队推广估计尤其困难。

---

[其实你并不懂 Unicode](https://zhuanlan.zhihu.com/p/53714077)

不错的扫盲文

想当年做 Win32 GDI 时被 glyph 和 grapheme 搞得要死要死。