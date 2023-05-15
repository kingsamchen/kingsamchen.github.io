---
title: 一周杂记 in Week 2 May 2023
categories: CODE-LIFE
date: 2023-05-15 22:57:42
tags: [杂记]
---
本周（05/08 ~ 05/14）是五月第二周。

## Life

\#1

周二参加了公司包场的《超级马里奥兄弟》大电影，满满都是童年，电影也特别欢乐。 4/5

虽然翠苑大世界观影效果一般但是周围都是同事没有啥熊孩子干扰也挺好的，而且票价免费不说还送爆米花呢

自己另外看了 R.I.P.D，没想到是13年的片子，整体中规中矩，算是及格线上的爆米花吧 3/5

\#2

这周有氧跑步虽然就跑了两次（不算和同事周三暴走马路），但是一次 40mins 6KM 一次 60mins 9KM 也基本达到了一周 15KM 的基本要求。

感觉未来可以尝试一下一周两次 9KM 外加一次 5~6KM。

\#3

周日晚上和媳妇儿在H108边上吃了石锅鱼之后就去了附近的小河直街转悠了一会儿，风景感觉还行，适合平日里散散步。

![](img/20230513_112624136_iOS.jpg)
![](img/20230513_111200617_iOS.jpg)
![](img/20230513_105432861_iOS.jpg)

## Work

\#1

这周学习进度如下

**CppCon 2020 | Not Leaving Performance On The Jump Table - Eduardo Madrid**

- 前面批判了一下传统虚函数运行时多态的性能问题，后半部和批评 std::function的实现导致的性能问题。
作者是 snapchat 做 infra 的，自己写了一个 zoo::function 作为 drop-in replacement，里面用了很多 micro-optimization。但是 slides 上写的不是很清楚（比起标准库）到底怎么做才减少了简介函数访问寻址。
不过作者 github 上开源了这部分实现内容 https://github.com/thecppzoo/zoo

**CppCon 2020 | Lakos’20: The “Dam” Book is Done! - John Lakos**

- 演讲者是 Large-Scale C++ Software Design 的作者。但是这个 talk 高概念太多…感觉还是回去看书比较合理

**现代C++白皮书** 看完了

- 6. 概念
Concept 特性演变的前世今生

- 7. 错误处理
BS 认为异常和错误码二者目前都是需要的，并且适用于不同场景。
同时 BS 认为异常如果成为函数类型系统一部分会引起及大混乱

**DDIA | 4. Encoding and Evolution** 看完了

- 依然很赞；本章最后部分讲了 RPC 和 Messaging

\#2

抽了点时间过了一下两个完整的 oauth2 server repo (https://github.com/RichardKnop/go-oauth2-server & https://github.com/go-oauth2/oauth2) 和一个 gin middleware 的核心代码，
算是给之前的 posts 理论部分做了一点加深

\#3

这周接了一个蛋疼无比的 task，要把 postfix 的 logging 接到我们用的 glog 上。这样其一可以统一日志格式，方便 devops 写 kibana parse，其二是可以给 postfix 的日志加上文件名行号信息，以后查问题不用再根据内容全盘搜。

一开始的想法就是用宏定义把形如 `msg_info` 的函数调用换成宏，然后再展开成我们提供的 glog wrapper，顺带附上 `__FILE__` 和 `__LINE__`。

但是因为 postfix 有一些场合会把日志函数存成函数指针，还得针对这些场景做兼容；而且这个场景就拿不到 source context 只能随缘了。

此外还有一些蛋疼的诸如初始化和编译的蛋疼问题，这里就不展开了。

上周成功搞出一个 PoC，不过还要跑一下全量 tests 才能确定是否 solid。

---

好了这周就这样，下周见