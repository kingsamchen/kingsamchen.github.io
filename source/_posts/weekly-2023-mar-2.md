---
title: 一周杂记 in Week 2 Mar 2023
categories: CODE-LIFE
date: 2023-03-12 23:11:49
tags: [杂记]
---
时间来到三月份第二周。

## Life

\#1

周一下午整个 HZ server 大组由 manager 自费大家跑到三坝那块的亚运公园在草坪上露营野了一下午。

零食管够，游戏自便；小组里几个人又继续玩起了德扑。

不过我这次注资亏了￥100，被注资的同事一开始赚了500的筹码（10:1），在中间一把觉得自己牌不错很有希望获胜的加注中直接一把输了 1500 筹码 🤣

\#2

周四和同事踢了一场球，算是去年11月底新冠以来第一次踢球。

结果体能跟不上，最后十几分钟已经完全没力气了，只能在场上散步象征性防守一下...要不是这几次参加的人太少，我那会儿就直接申请下场休息了。

突然剧烈运动的肌肉酸痛一直持续到周日都还没完全消退...

\#3

这周看了两部电影

- _The Untouchable_ 8/10 这类片子的代表作了吧，为后来的同类型的电影奠定了很多基础。同时这篇的 cast 挺强大的
- “爱情神话” 8/10 老婆看了很不爽要打人，但是我觉得挺好的。上海本地人那种小资生活和人到中年的感情表达刻画的还是不错的

## Work

\#1

之前写 esld 的时候发现 `esl::strings::split` 用 `string_view` 做 delimiter 时会有编译错误。

于是这次专门抽了个时间研究了一下，发现是需要给几个 delimiter util class 加上 `string_view` 重载，但是因为要考虑到 `const char*` 参数下 `std::string` 和 `std::string_view` 都可能成为 candidate 会影响重载决议，所以还需要专门针对 `string_view` 做一点手脚，我选择的方案是用 SFINAE 来降低优先级，有点类似标准库中的相关处理。

PR 可见：https://github.com/kingsamchen/esl/pull/5

\#2

因为本周通过某篇 post 学习了一下 gperftools 的用法，但是文中给出的方案生成的 diagram 并没有火焰图直观，所以我想倒不如再继续研究如何最终生成火焰图。

回头有时间的话把写的 notes 直接贴到 blog 好了

\#3

本周学习进度

CppCon 2020 本周就看了一个 talk

- _C++ Standards Committee Fireside Chat hosted by Herb Sutter_
  非技术向，算是委员会部分人对于语言发展的未来讨论。

《重新理解创业》 看完了 “2. 重新理解竞争”

_DDIA_ 刚开始看，所以停留在 _1. Reliable Scalable and Maintainable Applications_ 中

《Apache Kafka 实战》 还在看 “6. Kafka 设计原理” 这一章内容还是挺多的。

看了两篇 weekly posts，不过这两篇其实放了好久了

- 使用 gperftools 分析程序性能
 就是前面讲的那片 post

- Cool stuff about GDB you didn't know
  这个本质上是一个 meeting c++ 的 talk

---

好了本周就是这样，下周见
