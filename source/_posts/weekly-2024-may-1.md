---
title: 一周杂记 in Week 1 May 2024
categories: CODE-LIFE
date: 2024-05-06 22:57:56
tags: [杂记]
---
本周（04/29 ~ 05/05）是五月份第一周，也是劳动节假期

## Life

\#1

周日老妈坐高铁回温州了，于是周一我就是独自一人开车去富阳接媳妇儿回家。

因为和媳妇儿月的下午四点到，所以我下午三点连同第二天4/30直接请假了，独自一人开车上路。

一开始还有点紧张的，还好开了十来分钟之后就放松恢复到之前的驾驶状态了；加上大部分路程都是隧道和快速路，所以保持专注注意控速就行，尤其我备刹习惯比较好

于是顺利的开了50多分钟之后到了富阳的学校。

接上媳妇儿之后她说想吃了晚饭再回去，恰好如果这个时候直接回去可能要在路上堵一段，所以我们导航去了富阳的万达。

选了一条看起来比较优秀的导航路线出发之后才大呼上当，虽然避开了拥堵主路，但是尼玛这条路都是乡间小路，路又窄又烂，001车宽两米我压根不敢开的太快...

更坑爹的时候后半段有个村头路口有限宽墩，我目测估计也就给了250cm左右的宽度空间...当时开到跟前人都傻了，第一想法是倒车，结果一看后视镜，坏咯，后面已经有3-4辆车堵住了我的去路...

没办法，思考片刻打开360，用怠速硬生生一点点挪过去的，还好最终有惊无险过了，再次感谢360...

到万达车库后选车位停车，找了半天只有一个合适车位，而且左右还都有车了；更蛋疼的是我准备倒车时候边上有辆车准备要出去...

还好有360加持，加上那天驾驶技能包确实发挥不错，依靠有序的调整，大约调整了三次就倒进去了

搞笑的是快倒车结束的时候才发现车位左边是一辆保时捷跑车，因为是少见的银色，一开始以为是911，所以还是比较紧张的，特意调整了一下离他稍远一点，避免开门碰到人家不是。

停好车之后我下车一看，哦，只是718 boxster 啊...刚才吓我一跳呢 🤪

上楼之后选了一家日料，但是实话说价格虽然只有大渔的50%，但是味道和体验和大渔差太多了。媳妇儿吃的也不是很开心

吃完后就启程回家了，路上也比较顺利，到家入地库倒车也是调整了一次就完成了。这波我给自己打99分。

\#2

4/30 高铁回老家，然后又一次把媳妇儿惹生气冷战了：快到家的时候我右后作为两个5-6岁的男孩玩ipad游戏太吵了，他们前排的一个男的首先出来让他们安静一点，我见状也加入

第一次没啥效果，于是我和他又第二次让他们安静一点，这次嗓门大声了一点，而且我顺口说了一句“再吵就把你们丢出去”

这次确实有效果了，另外一个男生的家长立马把孩子领了回去，另一个家长也连连让男孩安静

不过这也惹得媳妇儿非常生气，嫌我多管闲事，容易引起冲突，然后冷战了好几天...

我整一个大无语...

最后只能道歉啊，表示以后痛改前非啊之类的...

\#3

本周观影

- **飞驰人生2** 4/5 比起1叙事要连贯了许多去掉了很多坏毛病；后半段太燃了。沈腾告诉世人自己不当喜剧人的时候演技也是到位的
- **海王2：失落的王国 Aquaman and the Lost Kingdom** 3/5 温导还是牛逼，内忧外患之下还能给出一个合格的爆米花商业电影

## Work

\#1

本周学习

**深度学习入门 | 7. 卷积神经网络**

- 讲了卷积层和池化层
- 这张感觉讲的不行，看完了还是一头雾水

**深度学习入门 | 8. 深度学习（全书完）**

- 这张基本就是纯介绍性的内容，讲讲历史展望未来啥的…

**CppCon 2021 | The Basics of Profiling - Mathieu Ropert**

- profiling 很重要的一点是 profiler，talk 里用的是 https://github.com/bombomby/optick 并且讲了几个典型的 bottle neck 对应的 profiling 的呈现
- talk 里提到的一点：创造一个 reproducible case 很重要，不然需要分析的范围着实大了点
- 最后是 domain knowledge 对解释 bottle-neck 和后续优化也很重要

**CppCon 2021 | Back to Basics: Classic STL - Bob Steagall**

- STL 容器 迭代器等基础教学
- 适合新手

**[MUC++] Jonathan O'Connor - Template Shenanigans: Testing, Debugging and Benchmarking Template Code**

- 完整 talk 有点长，可以压缩到一个小时的话感觉会更好
- 第一个主题是获取模板实例化类型，talk 分享了很多tricks
    - SuperV1234/camomilla
    - print_me function template based on `__PRETTY_FUNCTION__`
    - 用只有声明但是没有定义的模板类来出发编译错误，信息就包含类型；一个类似的改进是使用 deprecated variable template，只触发告警而不会阻止编译
- Testing 的话核心就是 static_assert 使用，另外还提到了一个 always_false variable template 的 trick，主要是配合 if constexpr 使用
- [metashell.org](http://metashell.org) 和 Qt 前端 MSGUI（Windows ONLY)；没太看懂怎么用这东西，而且看起来已经很久没有人维护了…
- 一些相关的 tips：build time tracing, benchmark .etc https://github.com/ldionne/metabench

**对 ACE 网络库的几点吐槽** https://www.bilibili.com/video/BV1tt4y1t7hY/?vd_source=b1612988b780cebad3c7035af0de51bd

- 作为考古视频是非常不错的，而且还能学习/复习一下非阻塞网络编程的做法

**Non-Terminal Variadic Parameters and Default Values** https://www.cppstories.com/2021/non-terminal-variadic-args/

- 介绍了一些 tricks 来处理一个函数用了 variadic template args 之后如果最后一个参数希望使用默认参数的情况
- logging 函数希望搭配 std::source_location 可能就会遇到这种问题
- 在有提案解决这个问题前，我个人觉得利用 CTAD 的方案比较干净一些

**Does throw x implicit-move? Let’s ask SFINAE** https://quuxplusone.github.io/blog/2021/03/18/sfinae-on-throw-x/

- C++ 20 的 P1155 提案引入了 throw x 能触发 implicit move semantics，类似 return x
- 会影响到 SFINAE 匹配 throw p 是否是 well-formed 如果 p 是一个 move-only type
- 作者自己有一个提案（P2266 Simpler Implicit Move）想统一这种情况下的实现，但是对于一些情况会和 C++ 20 的方案不一致

\#2

之前给 esl 加上了 cpplint 来保证 line length，但是最近发现它还有一个 include what you use 的检查挺好用的，尤其对标准库的头文件有奇效

但是如果直接加到 commit hook 就有点冗余，所以想了一下还是引入 `CPPLINT.cfg` 文件来管理，这样省事的多

PR 见 https://github.com/kingsamchen/esl/pull/16

\#3

重新搞了一个研究 abseil-cpp 代码的 docker container，这次直接在 container 内部 clone repo 并且作为研究工程的子模块连结，好处是跳转和调试更方便

顺带又更新了一下 cxx-dev-dockerfile，把 gitconfig 和 vimrc 加上了，因为在 container 内部网络和宿主机隔离，需要用 docker eth 来访问宿主机的代理

更新有个附加的优点，apt 的包全都给更新了一遍，毕竟上次都是快两年前了

PR 在 https://github.com/kingsamchen/Eureka/pull/21

乘着这股风，Status 的代码终于快看完了，估计下周就可以收工发点笔记

\#4

之前每次在台式机上用 scoop 更新 git 的时候都因为有一对 long running background git processes 而导致更新失败，但是我的笔记本上却没有这个问题。

这次五一实在受不了了打算研究一下，发现每次不管是 `scoop update` 还是 `scoop status`，运行之后都会产生不会退出的后台 git 进程。

一开始以为是 scoop 自己的问题，但是翻遍了 issue list 也没有，后来猜想是不是 git 配置的问题，切换一个全空的 `.gitconfig` 之后果然问题消失了

于是对着 config 一个个筛查，最后发现是台式机上我开了 `fsmonitor = true` 导致的...

那些后台进程就是 fsmonitor daemon，这个可以用 `git fsmonitor--daemon` 命令来复现

虽然 vagrant/ubuntu-22.04 我也有这个设置，但是因为 ubuntu 的 git 只有 v2.34，fsmonitor daemon 是 v2.36 才支持的.... 🤦‍♂️

---

好了这周就这样，下周见
