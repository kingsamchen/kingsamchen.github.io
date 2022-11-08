---
title: 一周杂记 in Week 1 Nov 2022
categories: CODE-LIFE
date: 2022-11-08 21:51:32
tags: [杂记]
---
本周是 11 月的第一周。

## Life

\#1

这周双十一开始，又恰逢老妈来杭州看牙齿，所以直接 JD 上下单买了 iPhone 13 128G 换掉了她用了几年的 iPhone 7。

至于为啥不买 14 主要是因为 14 没有现货提示需要一两周才能送货，而且中老年人用 13 还是 14 差别不大。

顺带体验了一下 iPhone 如今的数据转移，确实傻瓜好用，及其人性化，新手机差不多和 clone 一样。

\#2

除了 iPhone 13 之外，还下单买了 LG Ultrafine GB95R-B (27' 4K 144Hz) 和 Herman Miller Aeron。

（感觉这个月开支要爆炸）

显示器最先到货，但是配上之后发现几个问题：

1. 某些字体色彩比起 DELL P2415Q 感觉怪怪的，研究了半天发现色彩校正还要额外再买一个仪器...算了，又不是不能用
2. 27'4K 的 PPI 比起 P2415Q 确实还差一点，我 DPI scale 开到 200% 之后明显觉得字体不如 P2415Q
3. 因为台式机用的二手的 1050 渣显卡，所以高刷只能开到 120Hz；而且这高刷不打游戏的话基本感觉不出来，除非一直晃动窗口。但是谁吃饱了撑着每天晃窗口玩啊。打游戏的话只能开2K 120Hz，体验还行
4. 这显示器的底座太杀马特了，贼大无比，直接占据了桌子好大一块区域。用了一天受不了了下单去买了显示器支架。

![](img/20221105_073849879_iOS.jpg)

结果支架这又遇到了新的问题....🤦‍♂️

因为我是双4K，所以直接买了双臂的支架，但是组装的时候发现一个27寸一个24寸，不管怎么调整都没法把两个显示器调到符合我使用习惯的情况。

因为我有强迫症，要求 primary monitor 必须正对着我视角中心，并且侧显示器扭头查看的角度不能超过15°，否则时间一久就会很难受。

![](img/20221106_142924398_iOS.jpg)

折腾了个把小时之后我终于放弃了，换了一个思路：

- 空置一个支架臂，另外一个支架臂挂 primary monitor，也就是 27' 的 LG
- secondary monitor 的 DELL 直接用他自己的底座，这样的话两个显示器是隔离的，可以直接微调。

并且我一开始要解决的就是 LG 的底座过大的问题...

![](img/20221107_010755821_iOS.jpg)

这个事情教育我们，prefer redundancy over dependency，也即我一开始应该买两个单臂支架....

至于椅子嘛，下单的时候就提示需要十几天才能发货，所以详情等到到货了我在更新。

\#3

这周和老婆一起看了 Kevin Spacy 大叔的 The Usual Suspect

剧情整体还是非常扎实的，唯一我有点疑问的是最后 FBI 探员发现被骗了不能立马发布全境通告吗？

整体上我能给到 8.5/10

## Work

\#1

这周是产品上线前最后一周，恰巧遇到需要紧急实现某个功能的情况。

于是哆哆嗦嗦折腾了几天终于赶在 code freeze 前把功能跑通了...

不过看了一眼 client team 依然处在暴风严重...

\#2

这周学习进度如下

- 《网络编程实战》依旧在刷，按照这个进度下周估计就可以完结了
- _Software Engineering at Google | 14. Larger Testing_ 继续看了几页，但是不多
- 刷了 _CppCon 2020 | Making Iterators, Views and Containers Easier to Write with Boost.STLInterfaces - Zach Laine_ 这个 talk
  核心就是标题里的 STLInterfaces 这个库，如果工作中经常需要定制 iterator 的可以看一下
- 看了两篇分别讲 [bloom filter](https://fylux.github.io/2017/03/19/Bloom-Filter/) 和 [cuckoo filter](https://blog.fastforwardlabs.com/2016/11/23/probabilistic-data-structure-showdown-cuckoo-filters-vs.-bloom-filters.html) 的文章。

后面会专门仔细找相关的开源代码和论文 deep dive 一下这两个数据结构，也会自己上手实现一次。

\#3

之前给 smtp-client 提的 issue 一直挂着也没作者回复。

索性也就不等了，把 PR 给写好了 https://github.com/somnisoft/smtp-client/pull/16

至于作者爱合不合吧。

不过写 PR 过程中瞄到这库这样两段代码：

- https://github.com/somnisoft/smtp-client/blob/master/src/smtp.c#L3298
- https://github.com/somnisoft/smtp-client/blob/master/src/smtp.c#L3371

合着每次加 recipient 和 header 都会重新分配内存，真牛逼呀。

毕竟稍微有点追求的人都不敢这么写代码 😅

---

好了本周就是这样，下周见
