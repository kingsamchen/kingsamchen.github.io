---
title: 一周杂记 in Week 5 Nov 2023
categories: CODE-LIFE
date: 2023-12-04 20:27:52
tags: [杂记]
---
本周（11/27 ~ 12/03）是十一月最后一周

## Life

\#1

本周观影

- 美剧 Loki S02 完结 5/5 最后一集质量收尾的恰到好处，直接把前面略微平庸的几集给挽回了
- 石门 4/5 很像纪实类的电影，冲击上和之前看的喜丧差不多。另外这剧因为得奖在豆瓣上已经被和谐了...

\#2

周四顶着8℃的气温穿着短袖踢了半场球（一个小时），然后觉得还是太冷又套了一件公司发的卫衣踢完了剩下的半小时。

然后周五就开始咳嗽了...

周六上午约了科目二的培训，要早起，那会儿已经觉得身体有点不太舒服...可能周六下午睡了好几个小时回了一波血所以没有太在意

但是周日上午7:10又早起练车，练车过程中已经感觉有点不对劲了。11点结束在小区里感觉全身有点发抖，量了体温发现烧到了 39.3...

和媳妇儿说了这个事吃了点泰诺和头孢睡了几个小时后提问降到了38，但是过了一会儿又烧起来了...

因为媳妇儿在值班，所以让媳妇儿寄了新冠和甲流的试剂，以及神药玛巴络莎韦片还有布洛芬。

测完试剂后确认是甲流阳性，还好搞来了神药，就着布洛芬吃完后又睡了几个小时。快十点醒来的时候发现体温又降到了38，但是症状明显有了好转

周一请了天病假，而且体温也慢慢降到了37.3，算是进入正常范围了

说起来去年貌似也是这个时候得的新冠...

\#3

本周练完后面就只剩下最后一次练车了，教练说等约了考试再安排练车时间

希望12月份能顺利拿到驾照 🙊

## Work

\#1

本周学习

**CppCon 2021 | Branchless Programming in C++ - Fedor Pikus**

- 现代计算机架构中分支预测错误带来的性能损耗在高性能计算和性能调优中必须要给予重视
- branchless 意味着 more work，但是流水线内的这部分 extra work 经常能带来更高的的性能，因为相比下 branch misprediction overhead 更大
- always measure

**凤凰架构 | 13. 持久化存储**

- K8S 的存储

**凤凰架构 | 14. 资源与调度**

- K8S POD 调度

**Perfect locality and three epic systemtap scripts https://blog.cloudflare.com/perfect-locality-and-three-epic-systemtap-scripts**

- 介绍了 `SO_REUSEPORT` 可以增加 accept queue 的能力，并且作者几人试图通过保证 accept/connection 始终在同一个 CPU 甚至保证同一个 connection packet 的rx/tx都在同一个CPU来改善 locatility 进而提升性能
- 但是比较好笑没有 measurable performance gain

\#2

还在写 asio coroutine-based chat server，而且遇到了一个小坑。

研究了一段后大概猜到了原因，不过后面还需要再确认一下。

因为这周身体状态不佳，没写太多代码

---

好了这周就这样，下周见
