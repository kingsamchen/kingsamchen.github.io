---
title: 一周杂记 in Week 1 Jan 2024
categories: CODE-LIFE
date: 2024-01-08 19:23:55
tags: [杂记]
---
本周（01/01 ~ 01/07）是1月份的第一周，happy new year!

## Life

\#1

这周因为新年氛围导致不是太想上班，所以周五干脆休了一天假，顺带去媳妇儿医院洗个牙齿。

媳妇儿直接找的口腔科护士长给我的洗的牙齿，手法还是相当老练的；并且因为我本身牙结石不多，所以整个清洗流程好像就持续了十来分钟就完了。

漱口的时候还吐出来一小块黑色的牙结石 🙈

歇了一会儿止住血之后就和媳妇儿去吃了一顿金华砂锅的筒骨，这家的筒骨火锅我非常喜欢，大骨啃起来非常带感

\#2

23年最冷的时候都没有在家里自己弄火锅，反倒是这周突然想起来，觉得周末可以在家里弄火锅。

盒马买点食材，自己调个酱，性价比极高，并且也可以确保食材的新鲜程度。

反正盘子什么的直接丢到洗碗机就行，我最多洗一下火锅的锅

\#3

这周观影

- **果尔达 Golda** 4/5 Meir 的角色张力演的很好，剧里真看不出之前天空之眼的痕迹。政治真的是一门生意的艺术

- **保你平安**‎ 4/5 现实往往比喜剧更荒诞，大鹏这么多年确实进步飞速啊
    间隔许久，家庭影院终于重新开张；这影片媳妇儿观影体验也说还行（可能比起来没有 年会不能停 那么喜庆）


## Work

\#1

本周学习进度

**HBase原理与实践 | 9. 宕机恢复原理**

- 核心就是 master server 和 region-server node 挂了之后如何恢复
- 前者基于 ZK 做了主备，主挂了直接切换到备；并且因为 master server 只是做一些管理性的工作，所以挂的概率很低
- region-server 挂了需要把他负责的 region 分给其他的 region server，并且需要把 hlog 先 split 之后才能在新的 region 上重放，避免数据丢失；这部分就有很多讲究

**HBase原理与实践 | 10. 复制**

- region 在 region server 间的迁移对 replication 会有负面影响，可能会导致 master 和 replica 数据不一致；解决方案是（小米团队）提供的串行化复制方案：每个 region 维护一个 barrier list记录每次 region 迁移的到某个 RS 所处的时刻，保证按顺序来复制
- 另外一个是同步复制，用于在主从切换时对数据一致性有要求的场景：做法是写主节点时同步写备节点的 remote-wal 日志，都写入成功了才返回客户端。主备之间继续异步复制流，完成的异步复制后会清理掉 remote-wal 中对应 entry；如果发生主从切换，备节点利用 replay remote-wal 就能使得数据和主节点一致，之后再继续接受外部流量
- 实践中开启同步复制会有大概 13% 左右的性能下降

**HBase原理与实践 | 11. 备份与恢复**

- 介绍 hbase 的 snapshot 机制
- snapshot 之所以可以迅速完成是因为创建了一个 reference 到 hfile，因为 hfile 写入后就不会变更；后续的正常写入写新 hfile

**HBase原理与实践 | 12. HBase 运维**

- hbase 的metrics 介绍，以及 YCSB 压测工具的使用
- 如何对hbase实现各种隔离：请求队列的隔离；利用 region-server group 来隔离不同的业务 region；但是使用 RSGroup 到导致关闭全局负载均衡，后续负载均衡都需要手动触发
- HBase 各种运维相关的配置参数

**Building Microservices | 13. Scaling**

- 讲了服务的水平扩展，垂直扩展，还有最小粒度的 functional decomposition
- 大篇幅的 cache 的正确使用，以及把 cache 当作一种优化手段，仅在有必要的时候采用；牢记过早优化是万恶之源
- 最后提了一嘴 auto-scaling
- 另外项目开始的时候不要用太复杂的技术架构，因为很可能做出来的产品没人用；后面有用户有流量了再继续调整

**CppCon 2021 | GPU Accelerated Computing on Cross-Vendor Graphics Cards with Vulkan Kompute - Alejandro Saucedo**

- 主要介绍的 vulkan kompute 这个 framework
- 对这个领域不是很熟悉，纯当作英语听力练习了

**C++ concepts with classes https://www.sandordargo.com/blog/2021/02/24/cpp-concepts-with-classes**

- 在类模板中使用 concept 有两种做法
- **方案1**

    ```cpp
    template<typename T>
    concept number_type = std::integral<T> || std::floating_point<T>;

    template <Number T, Number U>
    class WrappedNumber {
    public:
      WrappedNumber(T num, U anotherNum) : m_num(num), m_anotherNum(anotherNum) {}
    private:
      T  m_num;
      U  m_anotherNum;
    };
    ```

    直观，易读易写

    但是复合表达式必须得单独抽象成一个新的 concept

- **方案2**

    ```cpp
    template <typename T, typename U>
    requires Number<T> && Number<U>
    class WrappedNumber {
    public:
      WrappedNumber(T num, U anotherNum) : m_num(num), m_anotherNum(anotherNum) {}
    private:
      T  m_num;
      U  m_anotherNum;
    };
    ```

    使用 requires clause

    并且 requires clause 支持复合语句，所以（不考虑可读性）可以

    ```cpp
    template <typename T>
    requires std::integral<T> || std::floating_point<T>
    class WrappedNumber {
      // ...
    };
    ```


个人还是建议用方案1，除非有特殊的理由不用

**Synchronized Output Streams with C++20 https://www.modernescpp.com/index.php/synchronized-outputstreams/**

- 介绍了 `std::osyncstream` 来使得一次 `std::cout` 的多个 << 调用可以原子
- osyncstream 内部维护了一个 buffer 所有写入都先暂存到这个buffer，最后隐式或显式的刷到 std::cout 上（最终刷的时候内部会做同步机制）
- 不过我觉得这玩意儿就是给 std::cout 这种老的流输出打补丁，不如等一下后面基于 libfmt 的 std::print/ln

**Don’t blindly prefer emplace_back to push_back https://quuxplusone.github.io/blog/2021/03/03/push-back-emplace-back/**

- 同等条件下优选 push_back，至少性能不会比 emplace_back 更差；并且因为前者是固定参数类型的重载匹配，编译器确定的信息更多；而后者则是不定参数模板加上参数的完美转发，要到最后生成一大堆代码之后才能确定匹配信息，导致前者更容易做到优化
- 文中给的 benchmark sample 里，push_back 能比 emplace_back 要 5x faster
- 仅当类型不支持拷贝、移动构造，或者移动构造成本很高，必须得通过直接构造的方式时再考虑 emplace_back

\#2

抽了个时间把搁置许久的 abseil 又拉了出来，过了一遍 raw logging 的源码。

这部分比起 glog 啥的都简单得多，毕竟使用场景固定且有限

\#3

这周买了本深度学习入门，看豆瓣的书评对这本的新手友好性赞不绝口。

感觉今年实在是有必要认真的抽时间学习一下深度学习了，未来的风口基于这个的概率比较高。

今年再赚一年舒服钱，年底就要认真思考一下以后的方向啥的了，毕竟我对我们组目前的产品已经没有任何预期，只要能让我带满四年拿完 RSU 就是胜利 😂

---

好了这周就这样，下周见
