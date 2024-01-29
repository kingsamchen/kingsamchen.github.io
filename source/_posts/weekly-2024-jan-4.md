---
title: 一周杂记 in Week 4 Jan 2024
categories: CODE-LIFE
date: 2024-01-29 22:55:15
tags: [杂记]
---
本周（01/22 ~ 01/28）是一月份的第四周，下周就将进入2月份，并且离农历新年只剩下了两周。

## Life

\#1

这周在周二和 Keting 在公司对面吃了一顿黄牛火锅，味道还不错，但是感觉离三分银边上那家贵州黄牛肉还是差点（这家店我甚至觉得比余阿福还好吃）

席间聊了一些公司的八卦，包括大倒我们组各种蛋疼的苦水 Orz

周四晚上兰哥过了高级并且拿到了副高，所以请我们吃了一顿三分银，三分银的味道确实是不错

\#2

好像每次吃大餐隔天就会出大新闻。

周四吃完三分银到了周五上班的时候听到同事说 US 那边 layoff 了两个人，并且看到了 calendar 里有一个11点开始的 hz team 全员会。

仔细打听了一下发现是干掉了两个没几年的小虾米...

老张在全员会上还一个劲的咬死说那两人被 layoff 是 perf 的问题，我们项目还是真有保证的...

俩字，呵呵 🙂

\#3

这周看了两部电影：

- **绿色边境 Zielona granica** 3.5/5 仅从电影角度成片质量还是相当好的，叙述利落不拖沓情绪调动到位算是上乘的 propaganda。但是从电影作为内容媒介的角度我只能给予非常不看好且中立偏负面的评价。难民问题是一个非常复杂的政治及社会议题完全不是导演想表达的只要人人都献出一点爱就能解决的；这是极其幼稚的想法。上层精英人士有阶层保护罩，又有什么资格替受难民潮冲击第一前线的中下层民众做决定。随意救助难民要比随意喂养流浪猫狗却不做绝育更加恶劣
- **茶啊二中 4/5** 久违的家庭影院。套路老但是架不住青春回忆啊，笑点也足够。现在国内动画制作水平确实进步很大

## Work

\#1

本周学习进度

**Building Microservices | 16. The Evolutionary Architect（全书完）**

- 作为全书最后一章这章内容基本是比较务虚且杂
- 讲了何为软件架构以及如何定义业务边界且如何演进
- 然后就是团队搭建和团队成长以及技术债处理，不过这部分都是蜻蜓点水

**深度学习入门 | 1. Python 入门**

- python 环境搭建啥的，比较简单
- 至少要装一下 numpy 和 matplotlib

**价值投资实战手册 | 1. 正确面对股价波动**

- 两天看了二十来页，全书几百页就三章…
- 开头这部分讲了一些基本的观点逻辑：
  - 投资是为了在未来更有能力消费而放弃今天的消费
  - 做大概率成功的股模式投资并且把投资贯穿一生，不要放弃投资
  - 比起长短期债券，黄金，储蓄，股票的长期综合收益率是最高的，并且过去几十上百年在多个国家的金融市场都得到了验证；中位数上的上市公司的利润增长一定是大于等于整体国家的GDP增长率
  - 股票**稳定增值**的核心逻辑来源于企业自身增值和融资增值，短期市场情绪波动是很难被预测且利用的

**CppCon 2021 | Evolving an Actor Library Based on Lessons Learned from Large-Scale Deployments - Benjamin Hindman**

- 一个基于 actor 同时暴露出 future/promise 语义的框架: libprocess
- 使用 actor model 还得需要考虑调度，例如 continuation 会在哪个上下文被执行
- 感觉最后还是得看一下标准库的 executor + coroutine 的方案

**CppCon 2021 | Discovering a User-Facing Concept - Christopher Di Bella**

- 通过一个具体的例子深入阐述应该如何使用 concepts 并且给出了一些 guidelines（基本都在最后的 summary 里），例如
- concepts 是用来描述算法的要求，不是某些类型的属性，所以设计 concepts 前要对算法有深刻理解
- 同样的，你的 concepts 优化版本不一定比 naive 实现快，如果 concept 化的目标是为了性能，那么一定要 benchmark
- 暴露给 end-users 的 concepts 一定要是必须的且广泛合理，否则就把这部分 concepts 约束做成实现细节

**CppCon 2021 | Just Enough Assembly for Compiler Explorer - Anders Schau Knatten**

- 一些(反)汇编基础能让你看懂 compiler explorer 上基础的C代码
- 为啥说是C呢，因为C++的特性和标准库的东西随便一生成就是另外一种样子…不过这个也算一个 good start

**The quantum state of a TCP port https://blog.cloudflare.com/the-quantum-state-of-a-tcp-port/**

- 干货超级多，核心内容是讲 tcp 的 local port reuse
- 首先可以用 `sysctl -w net.ipv4.ip_local_port_range='60000 60000'` 来强制系统可以使用的 port range
- client-side 在 connect 前手动使用 bind 会导致内核同时 reserve 这个 port，阻碍了 local port reuse，解决方案是配合 `IP_BIND_ADDRESS_NO_PORT` 这个 option
- `SO_REUSEADDR` 在一些情况下可以强制内核先考虑 local port reuse
- 整体具体的 reuse 上下文非常复杂，遇到问题分析不出来的时候建议直接看原文

**Designing Distributed Systems -- A Conversation with Ken Arnold, Part III** https://github.com/kingsamchen/footnotes-of-linux-multithreaded-server-side-programming/blob/master/designing_distributed_systems_a_conversion_with_ken_arnold.md

- design for failure and be failure tolerant
- local/remote transparency so far is impossible; and there is partial failure and distributed transaction or retry with idempotency may fix them
- finally, state is a hell in distributed system

**浅析如何设计一个亿级网关** https://mp.weixin.qq.com/s/n9CmpkiGThQbXW6KcGcQVA

- 蜻蜓点水讲的很浅；内容上看作者自己也没多少深入研究的功力…

**Consul Service Discovery 实践** https://lidawn.github.io/2019/12/12/consul-service-discovery/

- 比较水，其实就是简单过了一下 consul 的基础功能

\#2

因为这周有个 MR 太大了感觉不太好合进去所以需要拆分，然后就遇到一个问题：需要 git stash unstaged 的变更

问了一下 gpt，立马得到了答案

```shell
git stash save --keep-index
```

\#3

稍微研究了一下 include-what-you-use 的 setup，确实有点麻烦，不过在 Ubuntu 下还相对便捷一些

这玩意儿缺点是 false positive 有点多...

另外这周发现了 esl 在 gcc-11 编译后 tests 会 fail，挂在某个 is_nothrow_move_constructible 判定上，回头需要仔细研究一下

另，这周没看 absl/status 代码，我深刻反省一下 🤪

---

好了这周就这样，下周见
