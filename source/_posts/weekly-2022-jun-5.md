---
title: 一周杂记 in Week 5 June 2022
categories: CODE-LIFE
date: 2022-07-03 11:45:24
tags: [杂记]
---
这周是六月最后一周，七月的开头，正式进入下半年。

## Life

\#1

继之前河南村镇银行暴雷之后最近又听说有一些地方银行也出现在风险危机边缘，比如前几天闹得沸沸扬扬的南京银行。

上半年因为疫情封控导致的经济下行应该是板上钉钉了，但是因为房企暴雷带来的连锁反应感觉还在发酵中。

就是不知道下半年会不会烟花接着一个又一个。

虽然更新完上半年的投资收益后发现整体收益已经转正，但是我对下半年的整体局势实在悲观的很。尤其我还有 100W 的资金跟着我爸投了南宁的房地产……🤪。希望南宁这种三四线城市能够晚一点触及雷区🤦‍♂️

加上这两天突然爆出上海警务数据泄露，差不多有十亿人的完整个人信息泄露，不知道这个夹杂着经济下行会不会成为一个隐藏的定时炸弹。

不管怎么样，已经第一时间提醒家里人要注意电话诈骗了。

\#2

这周看完了美剧鹰眼，整体上中规中矩，即使后面看到了 Kingpin 而且还是同一个演员，也只是让我惊喜了一会儿。

主要剧情实在太平淡了，完全没法和 Daredevil 相提并论。

Better Call Saul 上半季还剩下最后一集，最近每一集都看得我心惊肉跳，剧情张力实在太强了。

周日趁着老婆值班，中午抽空看完了 Eternals(永恒族)，我个人评价大概在 3/5 左右，不算佳作但是也没有豆瓣上评论的那么差，属于基本合格的作品。

大致上能感受到赵婷的个人风格，但是感觉她目前驾驭群像商业剧的能力还是有所欠缺。出场人物众多，性格也很难树立，一些转变和形式逻辑的动机看来也不是很充分。

不过整体上还是及格的爆米花。

\#3

不知道最近是啥情况，连续两次面试候选人第一面的时间还没到就给我发短信说拿到 offer 不再继续后续流程了。

可能确实还有些“大厂”还是舍得花大价钱招人把。

上周组里有个做前端的同事来了大概只干了两个月就跑去字节了。虽然说个人职业选择我一个外人没什么好指指点点的，但是我是觉得这个时间点去字节这种卷烟厂不是一个太好的选择。

可能这就属于另一种“何不食肉糜”？

## Work

\#1

这周主要给 Lumper 加了记录 container 信息和 command ps。

信息以 JSON 格式存储在磁盘上，选择的库是工作中用到的 nlohmann-json。

实话说，不苛求性能的话，这个 JSON 库是真的好用，大概是我用到现在感觉最好用的了；比 chromium base 下面那个 JSON library 高明不知道哪里去了。

另外顺带修了几个问题，比如：

- 容器进程的 wait 由一个 scope-guard 在作用域退出时自动执行，但是之前忘了这个 scope-guard 可能会因为异常出发而执行。
  恰巧这个 scope-guard 内部执行的东西也可能会抛异常，比如某些极端情况下的 `subprocess.wait()`。所以为了避免 double active exception 导致直接 abort 需要 explicitly swallow 内部的异常
- 另外一个是之前 volume data 的 path 如果不存在，抛的异常是 `std::invalid_argument`，但是根据上下文这里其实抛 `command_run_error` 更好

\#2

顺带研究了一下 CMake 中怎么只 clean 某个指定的 target。

如果 Generator 用的是 Makefile 可以 cd 到目标目录然后 `make clean`，但是如果是 Ninja 呢？

首先发现可以用 `--clean-first` 这个参数，但是这样做不仅会清理 target，还是把 target 依赖的 target 也给一并清理了...显然不是最优

研究了一下 ninja 的命令参数，发现似乎可以用 `ninja -t clean target`，但是试了一把发现效果其实和前面的 CMake 的 `--clean-first` 一样会把 deps 也清理了。

又做了一番研究之后发现正确的姿势是使用 `ninja -t clean -r CXX_COMPILER__lumper_Debug`；这里的 `CXX_COMPILER__lumper_Debug` 是 rule。

完整的 rule list 可以通过 `ninja -t rules` 获得。

不过想了想可能最好用的方式还是直接删除目标 target 的构建目录....

\#3

本周看完了论文 _Introduction to Distributed Systems_ 和 [Covariance and Contravariance in C++ Standard Library](http://cpptruths.blogspot.com/2015/11/covariance-and-contravariance-in-c.html)

论文没什么好说的，分布式 101 必读。

这篇 post 其实有点意思，尤其是 `std::function<>` 的参数类型可以用来做 contravariance 这个是我之前没有想到的。

周中看了几页 _Software Engineering at Google | Chapter 8 Style Guides and Rules_；这个章节快看完了。

\#4

又继续开始刷极客时间的课程，这次选择的是“性能工程高手课”。

读了前几章，实话说感觉有点水，看看后面的内容能不能带来惊喜。

---

这周就这样，下周见。
