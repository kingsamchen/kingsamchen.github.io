---
title: 一周杂记 in Week 4 July 2023
categories: CODE-LIFE
date: 2023-07-30 23:44:35
tags: [杂记]
---
本周（07/24 ~ 07/30）是7月份的第四周，也是最后一周；虽然下周二才是8/1但是下周大部分时间都是在8月份 XD

## Life

\#1

这周四组里又跑出去团建了，开心的一笔。

中午在乐堤港吃了顿人均300的海鲜大餐，然后在乐堤港的影院看了封神1，到家后还跑了个 5KM 😆

加上周三中午和两个同事请了几个小时假跑到西湖文化广场看碟中谍7，看完回来继续上班，现在这生活状态还是不错的 🤣

\#2

这周看了三部电影

- 碟中谍7： 周三中午和跑出去看的，勉强给4星吧，不知道是不是拆成上下两部导致这部节奏水了一点，IIsa 死的有点草率。最核心的叛变AI这段感觉一般，看的时候满脑子都是 POI 里的两个超级AI对攻的桥段
- 封神1：4/5 有做成中国指环王的野心，有些台词interesting；不过节奏太快有些副角色人物没太立起来，李雪健的身体对声音影响太大了，一说台词就出戏
- 玩命记忆（Uknown）：4/5 这剧本有点有意思，最后那个反转猝不及防。另外没想到李四这么早就和豆豆有合作了 🤣

\#3

周六下午在健身房🏃‍第一次跑了10KM，虽然花了1:04:xx，不过好歹是坚持下来了。

跑到 9KM 的时候花了 55mins，其实那会儿如果保持 9.5kmh 的配速跑完最后一公里的话应该一个小时多一分钟就跑完了，但是无视了自己当天状态一般，尤其中午吃饭太晚，吃完都两点了，下午四点开始跑休息不够充分，开 12kmh 冲刺，结果才冲了 1min 就开始胃部不适，降到 8kmh 都缓不下来无奈只能降到 4kmh 走了一会儿。

有点像投资，时机不对近谨慎强上。

## Work

\#1

本周学习进度如下

**CppCon 2021 | Lightning Talk: Better Support For Emotive Programming in C++ - Pablo Halpern**

- 挺欢乐的一个搞笑类 talk，用 emoticon 来表达程序员情绪

**CppCon 2021 | Visualize Time Enabled Data using ArcGIS Qt (C++) and Toolkit - Gela Malek Pour**

- 某个做GIS的公司做的一个控件产品的 demo/presentation；个人不是很感兴趣

**CppCon 2021 | Lightning Talk: Curry-Howard Correspondence - Jefferson Carpenter**

- 不知道是不是 category theory 部分的东西，太高端了听不懂

**CppCon 2021 | Back to Basics: const and constexpr - Rainer Grimm**

- const, constexpr, consteval, constinit 基础教育

**CppCon 2021 | Lightning Talk: Memoizing Constexpr Programs - Chris Philip**

- 如何给 constexpr 递归函数做 memo 以加速编译避免被编译器 gank

**Modern CMake for C++ | 4. Working with Targets**

- 讲了 target 的基础概念和 generator exprs；也终于明白了为啥叫这个名字了，因为这是为了在generation stage 使用算是二次 configurate
- 另外 cmake 自己支持了使用 graphviz 来生成依赖图

**Modern CMake for C++ | 5. Compiling C++ Sources with CMake**

- 这部分我比较熟悉了

**DDIA | 7. Transactions**

- 覆盖了 ACID 和重要的 isolation level；对 dirty read/write read skew, lost updates, write skew, phantom reads 都做了展开；干货满满

\#2

本周开始研究 abseil 的 once 部分源码。

不过这周花了不少时间在“学习”上，所以这部分进度几乎算没有，放下周重点吧

---

好了本周就这样，下周见
