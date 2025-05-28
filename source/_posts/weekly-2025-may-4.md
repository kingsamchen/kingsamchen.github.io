---
title: 一周杂记 in Week 4 May 2025
categories: CODE-LIFE
date: 2025-05-25 23:43:28
tags: [杂记]
---
本周（05/19 ~ 05/25）是五月份第四周

老婆22号顺利分娩，喜得一女，开始进入人生新阶段了

## Life

\#1

之前当众把老张阴阳了之后老张一直心中耿耿于怀，等着机会干我。

上上周四下午因为带老婆去医院检查，错过了HR喊我续约合同，周五HR又自己有事...

然后到了周末jiancheng找我说，老张找他说不让我续签合同，当时想着估计得拿小礼包走人了

还好杭州这边大manager对我们组情况比较了解，力保我，周一上午速度续签了合同，让jiancheng对老张说是他和site mgr要求的，有问题找他们俩

所以目前算是顺利挡开一剑。不过只要 Eric 还信任老张，我拿礼包走人还是板上钉钉的事情，区别就在于能拿多少礼包了。

现在只能期望10月份 Eric 验收不过关把老张干了，探手。

\#2

老婆周三中午吃完饭后做了一个体检，发现胆汁酸高出标准值太多，于是就办理了住院。

医生说周四周五差不多就应该要生了，不然指标有点不太好。

周三下班吃完饭之后带着一堆东西开车到医院陪护。

周四上午主动破水，等待开宫口。期间老婆因为宫缩太痛，每次剧烈宫缩的时候宝宝胎心就掉；见状立马摇来医生打无痛。

等了大半小时之后终于打上了无痛，宫缩的疼痛也慢慢减轻。

下午一点开了十指就医护就立马来辅助接生了；中间医护出来说有点脐带缠绕，头只出来一点点，可能要侧切然后用钳子钳出来，签了同意书之后就紧张的在一边等待

终于到了 14:56 孩子终于出生了。又在外面等了半小时之后，终于进去见到了老婆和宝宝。

因为是女孩，所以名字就用之前定好的 秋羽，英文名是我想的 Chloe。

周五、周六连续在医院休养了两天，周日联系好月子中心之后，他们就开车接送老婆和宝宝进驻月子中心了。

这周最后几天实在太累了，还好定的市妇保的一体化VIP病房，还有专门的护工阿姨陪护，不然估计更折腾。

虽然一天4800，但是这些都可以进医保，我的历年医保直接覆盖了；最后算下来之花了孩子的800多和护工阿姨的1920。

---

月子中心定的离老婆房子近的萧山医院自己的月子中心，月子期间还会有专业的医护上门，整体还是很不错的。

而且月子中心配备的阿姨也很专业靠谱

估计这段时间在月子中心还是比较稳的。

## Work

这周因为老婆待产，孩子出生，要忙得事情太多了，所以这部分内容会少很多。

\#1

**CppCon 2022 | Using std::chrono Calendar Dates for Finance in Cpp - Daniel Hanson** https://www.youtube.com/watch?v=iVnZGqAvEEg&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=120

- 大量使用 c++ 20 datetime 的例子，干货非常多，虽然不涉及底层实现，但是这些例子我觉得是上手学习的绝佳，尤其考虑到这个 datetime 抽象的很好但是门槛略高

**C++ Weekly - Ep 481 - What is Tail Call Elimination (Optimization)?** https://www.youtube.com/watch?v=cjiraPsZQIs

- 介绍尾调用优化的，尾递归优化实际上是尾调用优化的一种特殊形式 🤔

**C++ Weekly - Ep 342 - C++20's Ranges: A Quick Start** https://www.youtube.com/watch?v=sZy9XcGHmI4

- 几个好处：
    - 不用显示传递 begin/end 减少 access to tmp 的风险
    - pipable syntax 表达逻辑优势；搭配 drop/take 这些可以比较优雅的解决一些过去处理起来比较 nasty 的点

**What does the C++ error “A pointer to a bound function may only be used to call the function” mean?** https://devblogs.microsoft.com/oldnewthing/20220926-00/?p=107212

- 其实就是成员函数调用的时候忘了 `()` 即 `v.front.c_str()`
- 后面还讲了一个小故事。。。不过现在 editor 都能提示吧

**Why load fs:[0x18] into a register and then dereference that, instead of just going for fs:[n] directly?** https://devblogs.microsoft.com/oldnewthing/20220919-00/?p=107195

- x86 上 win 访问 tls 要走 `fs` 寄存器；x64 上走 `gs` 寄存器，这里已经有平台差异了。考虑到还有众多其他 Archs，所以如果要做 folding，效果不一定好。
- 因为有些 arch 不支持 folding，alpha 上 tls 本身就是 sys call；追求极致性能把代码搞得乱乱的不是很合适（TEB 里面存了好多东西）

\#2

这周在写公司一个 task 的时候需要把邮件 header 里的 `Date` 日期转换为时间戳，踩了两个坑，这里记录以下。

首先用的日期库是 Howard Hinnant 的 Date library，这个库也是 C++20 calendar/timezone 部分的实现原型；质量是很高的。

第一个坑： `std::istringstream` parse 之后如果出现错误，还想复用这个 string-stream 和地下的 content，只是想换一个 pattern 重新 parse，那么需要 `seekg()` 调整一下位置

第二个坑：

现在邮件的 `Date` 头的日期基本是长

```
Date: Tue, 27 May 2025 17:43:55 +0000 (UTC)
```

这是 RFC5322 规定的格式

但是不排除有些邮件系统还在用更老的 RFC822 里规定的格式

```
Date: Tue, 15 Nov 1994 08:12:31 GMT
```

区别在于时区信息用的名字缩写

问题在于时区名字缩写不是唯一的，美国中部时间和中国标准时间都是 CST，所以这种缩写 date library 是不认可的，也没法处理。

标准的名字时区格式是 `Asia/Shanghai` 这种

只能说一些老旧系统确实坑比较多

\#3

这周把 http-router 的 locate 相关的 tests 也补充好了，locate 实现上基本没什么问题。

这样一来，router 的核心功能都实现好了。

考虑到 go 版本的实现支持 case insensitive 的查询，但是我对这个行为有点不太赞同。于是我顺带研究了一下 pistache 的行为，发现他也是不考虑 case-insensitive find path 的

所以后续就不做这块了。

剩下的工作就是 router 的上层封装了，目前还是比较底层的 segmented/radix tree 而已

---

好了这周就这样，下周见
