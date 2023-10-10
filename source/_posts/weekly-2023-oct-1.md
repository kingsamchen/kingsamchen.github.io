---
title: 一周杂记 in Week 1 Oct 2023
categories: CODE-LIFE
date: 2023-10-10 00:39:14
tags: [杂记]
---
本周是（10/01 ~ 10/08）是十一假期周。

因为这周事情比较多，所以很多事情会简略略过

## Life

\#1

10.1 参加了温总（我表哥的小学/初中/高中同学兼发小，以前一起玩的时候认识的）的婚礼，带媳妇儿蹭了一顿酒席。

这差不多也是第一次塞份子钱红包 233

折腾了十来年，从小年轻熬成了中年大叔，终于成家了，可喜可贺

\#2

10.1 下午回来后稍作休息就和媳妇儿以及丈母娘自驾跑到几十公里开外的啥168黄金海岸线转了一下，顺带“参观”了一个叫渔岙村的小渔村。

丈母娘还给科普了一下著名温商王均瑶的事迹

\#3

因为媳妇儿10.4开始值班，所以10.3下午就回了杭州。

10.4 前同事X方同学来杭州参加他同学的婚礼，下午来家里参观了一下和他去了影院二刷了奥本海默

小伙子现在混得不错，可喜可贺

10.5 大表哥表嫂刚好在杭州看亚运会，傍晚来家里坐了一会儿，闲聊了一个多小时。

本来以为要一起吃饭的，所以那会儿晚饭就没在家准备，结果才发现他们下午吃的晚，那个点压根不饿...

\#4

10/11俩月媳妇儿要轮岗西溪这边一个士官学校的校医，本来是没啥压力的活，但是好巧不巧学校住宿环境恶劣，大蟑螂出没...

这事儿搞得鸡飞狗跳，蛋疼的一笔，而且看起来余波难平

\#5

本周观影

- 陪同X方同学二刷了奥本海默，继续给影片贡献票房
- 怒火攻心 Crank 3/5 有点 cult，整体一般吧
- 天使之心 Angle Heart 3/5 宗教私货太多，气氛营造/渲染不错，年轻的米基大叔是真的俊啊
- 周日晚上约了媳妇儿一起看了 好像也没有那么热血 4/5 比预期好，完成度和流畅度都很高。穿插的笑点不错，也没有过分煽情。而且媳妇儿观影评价也不错

## Work

\#1

本周学习

**CppCon 2021 | Back to Basics: Lambdas - Nicolai Josuttis**

- lambda 基础；确实有点基础了…

**CppCon 2021 | Lightning Talk: Reading Configuration Values When You Should Not Fail - Matthias Bilger**

- 作者自己实现了一个简易的单个 config value 具有多重值约束的方案
- 个人感觉作为配置系统不是很合适，因为太 verbose 而且用了太多 template trick 缺乏一些动态灵活性；不过可以作为其他方案的基础方案，比如某个配置系统的约束机制设计

**CppCon 2021 | Modern CMake Modules - Bret Brown**

- 以如何写一个 cmake module 开头介绍，并且讲了一些 cmake 在 bloomberg 的使用

**CppCon 2021 | Lightning Talk: Simplify Vulkan APIs with Code Generation - Victor Bogado da Silva Lins**

- slides 的代码片段都没有显示出来，有点尴尬…

**CppCon 2021 | Lightning Talk: Quickly Estimating Powers-Of-Two in Your Head - Andreas Weis**

- 2^x 到 10^y 的一个简单估算，而且虽然有一定误差，但是能保证量级基本一致

**DDIA | 12. The Future of Data Systems**

- （未来）另一种数据系统的设计
- 还讲了不少 tech ethics 的内容
- DDIA 终于看完了…虽然目测没吸收多少

**[浅谈分布式服务协调技术 Zookeeper](http://www.linkedkeeper.com/detail/blog.action?bid=1014)**

- 说是浅谈，其实就是综合多篇文章来源的 overview...

\#2

实验了 vagrant ubuntu vm，比自己预想的要更容易使用；并且网络设置更省事

但是不能直接在 vm 里重启，并且 virtualbox 的 vm 启动后一段时间 sshd 才启动，boot/reboot 略慢

后面看可以稳定的拿 vagrant 作为自己开发环境的 vm 了

\#3

翻了一下 abseil base/internal 的 endian 的源码部分。

这部分设计很简单，就俩部分：

- 基于 __bswap 这种 compiler built-in intrisinc 的 byteswap 基础操作函数
- 基于 外部 configure 确定的适配当前 endianess 的，且基于 byteswap 实现的 host_to_network/network_to_host wrapper

---

好了这周就这样，下周见
