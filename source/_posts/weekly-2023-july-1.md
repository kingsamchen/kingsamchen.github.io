---
title: 一周杂记 in Week 1 July 2023
categories: CODE-LIFE
date: 2023-07-10 20:55:46
tags: [杂记]
---
本周（07/03 ~ 07/09）是7月份的第一周

这周是真热啊...

## Life

\#1

周五 WFH 中午准备去买留夫鸭在靠近北门那片楼听见喵喵叫，循声走进发现是端午期间喂的小三花，她看到我就冲过来绕着我脚边转，显然是在要吃的。

考虑到我没带罐头并且回家里拿再过来猫可能就不见了所以我做了一个大胆的决定：我抱着三花走到了我的楼所在的那片草地。把猫放下之后三花有点害怕火速钻进了草丛里。

本来我想抱着猫上楼吃饭的但是既然这样的话还是先上楼拿罐头吧。

拿完罐头瞄了几声得到回应之后我找到了三花躲藏的位置。我比了几个动作但是她还是不出来，索性直接把罐头打开，没想到一开罐她嗖一下就冲出来四处找是哪里的香味...

我看猫估计要吃一会儿我也先去买留夫鸭然后上楼吃饭了。我吃完之后下楼发现她也吃完了并且她对这附近不是很熟悉所以躲得比较深。

考虑到她还是适应一开始的位置，我又开了一个罐头把她骗了出来，然后抱着她绕了半个小区走到了她熟悉的草地。估计是太阳太晒了她也叫了一路。

刚放下她就闪到了树荫比较多的一个小坡躺下，做出贵妃躺的姿势。我把罐头放到她面前，本来想摸摸她脑袋，没想到她烦了突然给了我一爪子...而且还尼玛抓出血了。

想了一下上次打完疫苗都是一年多前了，安全第一下午提早下班又跑到了红十字挂号打疫苗。

还好这次只需要补三针，所以一周内可以打完。Orz

\#2

这周看了三部电影

- MONDAYS / 如果不让上司注意到这个时间循环就无法结束 3/5 在公司和同事看的。这电影我只能感觉一般，没有啥特别亮眼的地方
- 喜丧 4/5 这电影真实到你可以当作纪录片来看，过于压抑了
- 大卫戈尔的一生 4/5 家庭影院重新开张。我个人还是比较喜欢这个剧本的

\#3

凑个三吧，这周实在是太热了。

## Work

\#1

本周学习进度

**CppCon 2021 | GraphBLAS: Building a C++ Matrix API for Graph Algorithms - Benjamin Brock & Scott McMillan**

- 用于 graph 的邻接矩阵表示的矩阵库

**CppCon 2021 | Lightning Talk: Using the IFC Specification - Cameron DaCamara**

- MSVC team 做的一个可以在web中交互显示某个模块 export/non-export 符号展示的东西

**CppCon 2021 | Lessons Learned from Packaging 10,000+ C++ Projects - Bret Brown & Daniel Ruoso**

- Bloomberg 内部的包管理方式。和我现在组里有点类似，都是对三方依赖打成系统包的方式集成
另外他们直接强制依赖之间必须得是DAG，既不能互相依赖；并且要考虑支持多种构建系统，虽然CMake是majority
同时提到他们不认为C++的依赖适合使用 semver，因为 ABI break 导致几乎不可能 at large scale
- 细节还是很多的，这个talk干货很多，推荐观看

**CppCon 2021 | Lightning Talk: FINALLY - The Presentation You've All Been Waiting For - Louis Thomas**

- 其实就是 SCOPE_GUARD（https://github.com/kingsamchen/esl/blob/master/esl/scope_guard.h）的一个简化实现

**CppCon 2021 | Lightning Talk: C++ Tip Of The Week - Kris Jusiak**

- https://tip-of-the-week.github.io/cpp/ yet another tip of the week

**C++ 20 高级编程 | 6. constexpr 元编程**

- 如标题

**Modern CMake for C++ | 1. First Steps with CMake**

- 第一章个人的感觉非常不错，很翔实，而且复杂的地方点到为止留给后续章节，没有过多展开
  不过感觉还是有一定 CMake 经验的更合适

其他的像 DDIA ch6 因为还没看完就不计入在内了。

\#2

周五的时候要把 x-sender 服务的一个新 feature 部署到 dev，但是我们的部署都只能由 devops 做，所以拉着 devops 花了差不多整整一个下午才部署完成...

对我们组甚至我们公司的基建水平无力吐槽。

第一次部署的时候费了老大劲才弄完，结果 OP 说前面创建db的时候(手抖了)AWS上的名字typo了，需要重新建。

因为服务业务特殊，只能先回滚，而且必须要按照服务的发布逆顺序回滚。

回滚之后 OP 又花了半小时重建DB，然后重新走发布流程。

结果这次发布 OP 把前面讲的服务发布顺序搞错了...发布期间搞出一大堆错日志来，还好这些错误都能够自愈...

实在是无力吐槽了

现代上规模的后端系统实在太考验基建水平了

\#3

hashids 这周把 encode 部分除了 minimum length 之外的都写完了。

算是缓慢推进吧。

---

好了这周就这样，下周见
