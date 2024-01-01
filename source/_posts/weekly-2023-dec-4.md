---
title: 一周杂记 in Week 4 Dec 2023
categories: CODE-LIFE
date: 2024-01-01 20:41:54
tags: [杂记]
---
本周（12/25 ~ 12/31）是12月份的最后一周，也是本年度的最后一周。

## Life

\#1

因为上周顺利速通了科二三四的考试拿到了驾照，加上这周依然是 US 的 holiday season，以及周末就是元旦新年假日，所以这周整体比较放松，Task 的节奏也降低到了一个比较舒服的状态。

不过考虑到后面整个项目面临的一些“挑战”，1月和2月的日子都不会太好过。

可能未来两个月会遇到老张疯狂微操以及疯狂从我们这群外包中顶锅祭天 🤨

\#2

周日下午和媳妇儿收拾了一下就出发去奥体的媳妇儿家拉上岳母一起过元旦了。

晚上在媳妇儿家边上的一家餐厅吃了一顿海鲜餐，味道确实不错，并且走的套餐所以三人 198 价格也不是很贵。

吃完饭回家歇了一会儿然后就触发去家边上的小电影院看了过年压轴电影（年会不能停）。

影院确实挺小的，一个家庭场就5排座位而且全都坐满了...而且尴尬的是中间放映到一半画面黑屏了...

不过胜在座椅舒适度不错以及离家确实近，一百米都不到，走几步就能回家了

而且因为楼上楼下都开了地暖的缘故，就算自己家没开地暖也不冷，比在拱墅的房子里确实舒服多了 🤣

\#3

本周看了两个电影

- 俱乐部的 **dumb money** 3/5 cast 有点惊喜，但是整个故事略零碎
- **年会不能停** 4/5 挺欢乐超出预期，虽然过于鸡汤但是谁说打工人就不能美好一把呢

## Work

\#1

本周学习进度

**CppCon 2021 | Template Metaprogramming: Practical Application - Jody Hagins**

- 首先介绍了 void_t 的一些坑点(defects)，表明目前官方的实现导致 void_t 无法在任意场合替换 enable_if
- 然后是 clang 支持的 -ftime-trace 来跟踪编译时间，以及一些元编程中减少编译时间的 tricks
- 最后是秀了一把 sorting types
- 整体的干货还是不少的，而且演讲者长得有点像波波维奇…

**CppCon 2021 | Making Libraries Consumable for Non-C++ Developers - Aaron R Robinson**

- 尽量使用固定长度的数据类型，例如 std::uint32_t 这种
- 注意 ABI：调用约定，内存布局，异常实现方式 等，尤其不要跨边界抛异常
- 两边内存交互模式

**Building Microservices | 12. Resiliency**

- 这一章虽然主题讲的是弹性和健壮性，但是讲的比较杂，涉及覆盖 超时、限流熔断以及故障恢复等
- 甚至还讲了一点营造良好友善的事故复盘氛围

****Monitoring and Observability**** https://copyconstruct.medium.com/monitoring-and-observability-8417d1952e1c

- Observability 关注的东西：
    - Monitoring
    - Alerting/visualization
    - Distributed systems tracing infrastructure
    - Log aggregation/analytics
- Monitoring is for symptom based Alerting
- Context is key. A tool that worked swimmingly well at company X won’t necessarily work just as well at company Y

**Performance analysis of cloud applications https://blog.acolyer.org/2018/05/04/performance-analysis-of-cloud-applications/**

- 真实线上环境的流量情况非常复杂且难以预测，实验环境能够模拟部分情况但是无法完全替代；不过某些优化如果在实验环境能跑出明显效果，那么在产线上一般也会有良好的效果，只是会有一定的出入
- 然而最佳的实验环境依然是复杂但真实的生产环境
- 关于 trace 以及收集数据：

    1) 同一时间点，所有程序同时开启 tracing 几毫秒，然后同时关闭 tracing 一会儿，如此往复。

    2) 利用 syscall 会被系统记录的特点，做一点点的 abuse：syscall(getpid, mark_1) 参数会被 getpid 忽略但是依然会被系统记录；好处是可以跨进程跟踪完整调用链

\#2

这周感觉歇的有点厉害了，没有投入时间在额外的编码上，下周一定补上

另外打算明年开始正式入坑深度学习，感觉这是一个比较重要的坑，至少得了解一下

---

好了这周就这样，下周（明年）见
