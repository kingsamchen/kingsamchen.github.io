---
title: 一周杂记 in Week 5 Aug 2023
categories: CODE-LIFE
date: 2023-09-04 23:15:17
tags: [杂记]
---
本周（08/28 ~ 09/03）是炎热的8月份的最后一周，也是9月份的开端。

这周的事儿比较多，为了减少文本我省略着说。

## Life

\#1

周一大A开盘前印花税下调的新闻闹得沸沸扬扬，大家都好奇这次能持续多久，最后结果是：1分钟...

虽然后续有几天显著上涨，但是整体看只能说，肾上腺素的作用持续不了多久。不过也趁着这波，清了手里一些鸡肋的基金，同时部分割肉了芯片这种拉大跨但是暂时上涨的。

不过有点蛋疼的是认房不认贷的政策对债市有一定冲击，进而影响了我自己手里的理财收益，所以整个8月份的收益非常难看，基本在 -7000 这个水平。

因为大环境非常不稳定，所以未来形式还是只能用扑朔迷离来形容。

\#2

周三 Kidd 请了大伙一顿三分银，味道不错。

不过因为周三去吃了所以就没去参加公司的电影俱乐部了

\#3

周日和两个发小/同学约了滨江天街的奥本海默。

这里要吐槽一句滨江天街影院的观影体验是真不咋地...选这里主要是因为这是我们三个人中间地点...

本周观影：

- 禁闭岛 5/5 一开始以为是悬疑推理，最后发现是惊悚。大受震撼
- 奥本海默 5/5 比之前敦刻尔克更合我胃口，双视角回忆插叙剪辑后劲很大。一个有七情六欲有私心的“普通人”被推上了普罗米修斯式的位置，但他可能从未这么设想过。甚至这个人曾经因为一些小小的不满可以立刻给自己mentor的苹果注射氰化物，第二天又立刻后悔了。Oppenheimer也好，Strauss也罢，他们都是人，逃脱不开矛盾一体。
- 开始看猎魔人S3 下周看完后在评论

## Work

\#1

本周学习

**CppCon 2021 | Using C Libraries in your Modern C++ Embedded Project - Michael Caisse**

- 有一部分涉及了 C++ 部分的逻辑怎么暴露给 C 程序；剩下一部分涉及嵌入式的一些设计
- 不过不是很懂这个领域

**CppCon 2021 | Lightning Talk: Is My Cat Turing-Complete? - Chloé Lourseyre**

- 一眼丁真，鉴定为炫猫

**CppCon 2021 | Lightning Talk: Tackling Technical Debt: Hello Junior Developers! - Tulio Paschoalin Leao**

- Legacy code 项目如何让新人（junior devs）上手开始开发

**凤凰架构 | 1. 服务架构演进史**

- 历史概论部分，写的还不错，不是很枯燥

**凤凰架构 | 2. 访问远程服务**

- 去看电影路上来回看完的；讲了 RPC 的发展史和 REST

**DDIA | 9. Consistency and Consensus**

- linearizability && casaulity ordering；终于有个能让我看明白 linearizability 的书了
- 分布式事务和简述主要一致性协议

**Building Microservices | 1. What Are Microservices?**

- 介绍序章，微服务优缺点

可以看出来我把微服务/架构作为新一阶段的阅读目标

\#2

这周踩了一个 `std::reference_wrapper` 的坑

```c++
int main() {
    std::string str;
    std::reference_wrapper<std::string> ref(str);
    std::string s2{"test"};
    ref = s2;
    fmt::print(str);
}
```

上面 L88 实际的行为是：从 `s2` 创建一个临时的 reference wrapper 然后覆盖 `ref`，而不是一开始以为的修改 `ref` 底层的 `str`

正确的做法是用 `ref.get()`

\#3

用了周日晚上半个小时时间给 himsw 加上了 enable/disable breakoff time 的选择功能。

后面再加上一个配置的支持就差不多完结了

---

好了本周就这样，下周见
