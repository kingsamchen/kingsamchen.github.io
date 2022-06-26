---
title: 一周杂记 in Week 4 June 2022
categories: CODE-LIFE
date: 2022-06-26 22:17:57
tags: [杂记]
---
## Life

\#1

这周二我妈来杭州看牙齿，待了两天，我又睡了一天沙发。因为最近气温骤升，又闷又热，第二天终于让媳妇儿同意戴个耳塞在主卧睡了一晚。

媳妇儿后来描述说呼噜还有，但是声音比以前小了很多，现在是小呼噜娃。

说明最近一段时间的运动健身饮食控制还是卓有成效的，需要继续保持。

\#2

这周买了一双球鞋，收货后发现尺码比正常标准码小一号，没办法又得折腾的退回去。

但是购买的时候运费就是我出的，这一来一回光运费就亏了24块钱...要是我要换个大一号，又要贴进去十几块钱的运费...

我都怀疑这卖家就是赚运费的。

\#3

这周天气炎热的太明显了，周四晚上踢了一个小时就已经完全没有力气继续了。

汗水把短袖完全浸湿，全场下来直接干光了两瓶水。

下周打算先歇一周，这体能消耗太大了。

\#4

这周因为老婆要准备主治晋升相关的事情，家庭影院继续暂停。

Better Call Saul 和 The Hawkeye 都看了一半了，估计下周就能看完。

\#5

杭州在这周将核酸有效期从3天延长到了7天，算是个好事情吧。

## Work

\#1

给 Lumper 的 base/subprocess 加了 detach subprocess 的[支持](https://github.com/kingsamchen/Lumper/commit/0a55df9c95a977a9bfcb9ae5b4f556a2d0a89bb9)

用的思路就是 fork/clone twice and intermediate child process exits immediately.

这样一来孙子进程的父进程会变为 `init` 进程，我们的主进程就不用再考虑给孙子进程的收尸（`SIGCHLD`）问题了；init 进程会为我们代劳。

\#2

第一步的目的是想给 Lumper 增加类似 `docker run -d` 的功能；但是最后实现下来发现有问题。

不管我运行什么进程，只要 lumper 进程一退出，运行的进程就会立即退出，经过细致观察，我发现有些运行的进程甚至在 lumper 进程运行前就退出了。

对着 docker 研究了一番发现 docker 要能后台运行譬如 `top` 这样的程序，除了 `-d` 之外还要增加 `--tty` 标识，为容器分配一个 tty。

而 `sudo ./lumper run --it -i ubuntu:focal /bin/bash` 运行后输入 `tty` 会提示 _no tty_。

想来应该就是 tty 的问题了

Google 搜索了半天发现 tty 其实是一个有点冷门的 topic，找到的比较完备的 post 只有这篇：http://rkoucha.fr/tech_corner/pty_pdip.html

扫了一眼发现要深刻理解要花的成本还是挺大的；想到研究 tty 和一开始研究 docker container 原理并不太相关，投入产出太低，于是打算先跳过这部分内容，后面哪天有闲情再说。

PS1：自己动手写 Docker 这本书实际上压根没有正确处理 tty，我不清楚作者一开始是怎么做到后台运行的，但是 issue 列表里也有其他人反馈了这部分代码的行为不正确

PS2：[rubber-docker](https://github.com/Fewbytes/rubber-docker) 这个项目很机智的没有把这部分列入研究目标。

\#3

这周学习了一下如何在 VM 里跑一个 docker container 编译一些挑环境的开源项目，比如 folly，然后再用 vscode remote 附加到容器中开发。

1. 首先利用 vscode remote ssh 连接到 VM
2. 然后 Ctrl + P 选择 Remote Container: Attach to running container，接着选择你的目标容器就行

VSCode 把 remote developement 打磨的已经很不错了，竞品们要追赶的的东西也越来越多了。

---

好了这周就这样，下周见
