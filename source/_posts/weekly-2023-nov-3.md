---
title: 一周杂记 in Week 3 Nov 2023
categories: CODE-LIFE
date: 2023-11-20 22:48:26
tags: [杂记]
---
本周（11/13 ~ 11/19）是11月的第三周

## Life

\#1

上周末练了两天车感觉非常疲惫，所以这周六随便找了个理由不练车了。

提前计划了一下和媳妇儿中午吃了**温州小吃**之后（毕竟家乡味道），跑到离家不远的杭州**大运河紫檀博物馆**参观了一下。

博物馆在一个十字路口角上，整体有5层，但是第五层不对外开放；预约后直接进入就行，有向导带你一层一层地解说。

博物馆整体还可以，尤其近距离观察了感觉很金贵地紫檀木，不过缺点是藏馆有点小，一会儿就逛完了，而且其实我对紫檀木制作加工啥的比较感兴趣，而这个场馆里并没有。

除了博物馆之后走个几分钟就到了小河直街地公园，这次算是故地重游吧。

因为周六天气很好，公园人不少，还遇到有一对新人在公园里拍婚纱照...

不过小河直街人太多了，随便逛了一会儿媳妇儿就觉得还是回家吧...

\#2

周日继续练车，而且是 7:00 -- 11:00 档。

周六晚上没睡好，第二天在路上开的时候都有点心慌...

\#3

本周观影：

- **大陆酒店 _The Continental_** 完结 3.5/5 虽然人物上弱了点但是整体还是超出预期，动作部分依然强项，吉布森大爷有点抢风头。另外男主真看不出是91年的…
- **泳不放弃 _Nyad_** 4/5 导演是凭借 **_Free Solo_** 拿了奥斯卡的金国威和他媳妇儿，Nyad 展示了论如何正确的打鸡血

## Work

\#1

本周学习进度

**CppCon 2021 | Back to Basics: Compiling and Linking - Ben Saks**

- C++ 编译链接模型基础，顺带还包含了 template code 的正确使用姿势，以及特化和显式实例化

**CppCon 2021 | C++ Standard Parallelism - Bryce Adelstein Lelbach**

- 第一部分简述了 pre-11, 17 和 20 的 parallelism 现状，并且引出了 S&R model
- 到这里我大概明白了为什么 NV 这帮搞 high-performance computing 的不愿意 asio 的 executor 进标准而要重新搞一个了…网络编程的异步/并行和HPC中面临的情况确实相去甚远…
- 第三部分介绍了 std::mdspan（会进入 C++ 23，并且在 scientific computing 中有大作用）

**Building Microservices | 9. Testing**

- 微服务架构下尽可能提升测试的 feedback speed，尽量减少 test 拖慢CI的情况
- 尽可能避免跨团队的 e2e tests；非跨团队的也尽量控制一下数量，可以考虑采用 stubs/mocks (Consumer Driven Contracts Tests) 来替代

**HBase原理与实践 | 4. HBase 客户端**

- HBase client 会缓存 Region server 信息；scan 操作很复杂，并且由众多参数可以 fine tune
- client 自身也有众多参数可以配置，某些参数会显著影响请求延迟和吞吐
- scan 支持多种 filter，不过很多都其实挺坑的看起来，比如 PrefixFilter 和 PageFilter，不如直接使用 scan 对象对应的参数配置
- 大数据量走 生成 HFile → Bulk load 效率更高对产线影响更小；以及一些生产中可能会遇到的问题

**凤凰架构 | 12. 容器间网络**

- 先介绍了 Linux 的虚拟网络/路由相关功能，例如 netfilter 钩子（iptables 的基础），以及 tun/tap 和 veth 两套虚拟网络方案，还有超级复杂的 linux virtual bridge，这部分完全没看懂，毕竟没有网工经验
- 然后介绍了 docker 的网络方案，以及跨主机通信的 CNM；和 K8S 选择的 CNI
- 随着 docker swarm 被 k8s 击败，CNM 已经寄了，现在考虑的是使用 CNI 的哪款容器插件更符合业务

\#2

开始写 coro-based chat server，并且相比 coro-based echo server，这次是更加严肃的当作工程的场景。

因为 chat server 各个 session 之间不是独立的，涉及到的管理也更加复杂

---

好了这周就这样，下周见
