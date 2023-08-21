---
title: 一周杂记 in Week 3 Aug 2023
categories: CODE-LIFE
date: 2023-08-21 20:08:36
tags: [杂记]
---
本周（08/14 ~ 08/20）是8月份的第三周，气温有变凉爽的趋势。

## Life

\#1

先说电影，本周就看了一部：**北欧人 The Northman** 2/5 哈姆雷特王子复仇记，但是一点都不好看

家庭影院本来和媳妇儿商定好看**禁闭岛**，但是因为媳妇儿英语学习计划遇到了一点延迟，所以取消了家庭影院。

另外我打算下周开始把美剧 _**The Witcher S03**_ 开始看起来，工作日每天看一集，一周多能看完 🙈

\#2

周六和老蔡一起跑去了一个数据库/云原生相关的 meetup，参加的有三家公司：ApeCloud, RisingWave 和 Sealos，都算是创业型公司。

因为我不是 db 研发从业人员，所以基本是过去听个热闹，顺带薅一点羊毛；比如光手提袋就拿了三个😅。我也没想到一家会给一个

其他参会人员感觉多半都是做 db 或者云开发的，估计夹杂了不少大佬。

并且原定 17:00 结束直接给延迟到了估计快 18:00；因为我们第三场听完之后已经 17:30，中途退场去吃饭了。

晚饭拉上了Ho教练和chao/他媳妇儿，坐了一把 Lexus SUV。

唯一差评的就是一开始选定的啥18道菜的店等位等了一个多钟头受不了了转而跑去吃湘绯。

## Work

\#1

本周学习进度

**CppCon 2021 | Lightning Talk: Creating a GPU With C++ and an FPGA - Iwan Smith**

- 用 FPGA & VGA 自制一个显卡并且用C++实现利用这个显卡的渲染
- 有点屌但是感觉没啥收获

**CppCon 2021 | The Upcoming Concurrency TS Version 2 for Low-Latency and Lockless Synchronization**

- 未来将会内置 hazard_ptr 和 rcu

**Modern CMake for C++ | 10. Generating Documentation**

- 自动化生成文档工具

**Modern CMake for C++ | 11. Installing and Packaging**

- 导出和包依赖，内容很多，干货也很多，需要好好咀嚼

**C++ 20 高级编程 | 10. 综合运用**

- 最后一章简单的过一下就行；后面需要研究自己实现 Task/Generator 的时候可以看看库实现
- 调度器部分就没必要看了，还不如看 muduo 或者 ASIO

\#2

为了要支持 plain email 和 3rd party client，老板让我和Jed研究一下 smtp auth，实现三方客户端的发件。

一周踩了N多坑，还好最后搞定了问题，可以用 Thunderbird 实现发件。

踩的坑就不展开了，把其他同事擦了一堆屁股...

不过期间学到两个小 tips:

1. `hexdump -C` 不仅可以额外展示 ASCII 解释还支持单字节展示；默认的 `hexdump` 是双字节，查看的时候有字节序问题
2. `view path/to/file` 可以以 readonly 模式打开 vim 查看指定文件

\#3

给 himsw 做了一个 breakoff time 的支持，并且默认设定 11:30 ~ 13:00 期间是 breakoff time。

这个作用是期间停止模拟按键，因为这个时候是我们的午休时间。停止模拟可以让我们的 IM 自动切换到 away 模式，避免中午休息的时候 US 那边的人还以为我还在工作...

不过 WFH 当天发现一个小问题：切换到 breakoff time 之后会顺利进入熄屏（符合预期），然后过了一会儿我的 VPN 就掉了（意外的BUG）。虽然这个是 global protect 的 BUG，但是人家没理由会修...

没办法估计只能额外做一个 enable/disable breakoff time 的 checkbox，WFH 的时候就关闭。

\#4

很早之前就想研究一下 CMake 的 install as package 的支持，但是一直没有契机不想学。

这次 _Modern CMake for C++_ 恰好看完了相应章节，并且因为这书的内容比较新，所以刚好可以作为一个契机学习一下。

于是就拿 esl 作为例子来验证一下。

拿 esl 作为例子之一是因为：

- 它是一个 library
- 它是一个 header only library

所以基本覆盖到了大半清情形。

经过几次不断尝试之后已经基本可以按照我设想的方式运作了，PR 可以参见 https://github.com/kingsamchen/esl/pull/10 中间也自然踩了不少坑。

不过我打算等到拿 uuidxx 作为另外一个 non-header-only library 情形验证之后，顺便再结合 vcpkg 私有包的机制跑通之后一起写篇 post 讲一下。当然前提是有时间并且我也不太懒的时候。

---

好了这周就这样，下周见
