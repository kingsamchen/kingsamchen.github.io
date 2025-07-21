---
title: 一周杂记 in Week 2 July 2025
categories: CODE-LIFE
date: 2025-07-14 00:35:42
tags: [杂记]
---
本周（07/7 ~ 07/13）是七月份的第二周，这周真的是阴雨天+潮湿天快给热嗝屁了

## Life

\#1

前面说到我们项目进入灾后重建了，所以这周开始会陡然多了好多，经常一天要开三四个会，而且隔三岔五要早起和美国那边开会。

讲道理我觉得有些会可能开早了...不过可能也和新老板的一些规划有关。

目前重建的事项拆分成了三大块，我被拉去 drive 打头阵的 quality workstream，主要负责服务梳理、各种监控接入和推动服务迁移到公司的云平台，整体上算是业务SRE。

（兜兜转转隔了6-7年好像，又开始搞业务SRE了 🫠）

虽然讲道理我更喜欢做服务架构这方面的事情，但是既然组织已经决定了，那我也没辙了。只能改变心态，把这块做好先；目前权当未来有大饼能兑现

\#2

这周周末两天还是去老婆家带娃

时隔一周，感觉娃有长大了不少，下周周末就该2个月了

我觉得屎总各方面的掌握都比寻常娃快一些（按照育儿百科书籍上的说法），希望未来智力在线 🤔

另外娃现在真的是太可爱了，表情丰富，虽然脾气和我一样差，但是看到娃的表情，感觉搬砖都有动力了。

\#3

这周周三和 iker 还有他媳妇儿在电影俱乐部看了“我想聊聊杜拉斯”，确实有点文艺，而且 iker 说如果没人的话他打算做攻略了；我一开始以为是后续放映会的攻略，没想到是他和他媳妇儿要去西班牙玩了 🤡

周日上午一大早和俩发小在西投银泰看了超人，看完后一起吃了个饭，稍微叙旧。因为那地方离媳妇儿家进，基本20分钟就到，所以看完电影就去媳妇儿家了。不过没想到到了之江大桥遇上堵车 😂

- **我想聊聊杜拉斯 Vous ne désirez que moi** 3/5 女dominant的pua及禁脔纪实，全片全靠男主的情绪表达扛着
- **范海辛 Van Helsing** 3/5 剧情其实挺一般，把frankenstein融进来倒是有点新奇，整体上总感觉有点没头没尾。20年前的狼叔是真的帅
- **超人 Superman** 4/5 DCEU用一部钢铁之躯塑造了一个新神还得用一部BvS把神变成了人；滚导则很干脆的直接摆出来：这其实就是人。 霍尔特的luther塑造的是真的好。另外不得不佩服滚导群像戏的把握真的是炉火纯青，不知道雷霆特工队要是滚导来拍会是什么样子

## Work

\#1

**Asynchronous Programming with C++ | 11. Logging and Debuggin Asynchronous Software**

- 介绍了 spdlog 以及调试器的基本 + time-travel 用法，但是整体都比较浅
- 甚至 debugging coroutine 那部分感觉都是口水话 🤷‍♂️

**CppCon 2022 | Nth Pack Element in C++ - A Case Study - Kris Jusiak** https://www.youtube.com/watch?v=MLmDm1XFhEM&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=77

- 这个 talk 比较抽象，核心是比较各种方法实现的 compile-time nth-element 的优劣，包括对 tuple 的 std::get<N>，然后我感觉隐藏的意思是希望采纳 circle 直接把 tuple 做进 language core

**CppCon 2022 | A Pattern Language for Expressing Concurrency in Cpp - Lucian Radu Teodorescu** https://www.youtube.com/watch?v=0i2MnO2_uic&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=75

- 介绍 S&R 模型的
- 但是我觉得这套写网络业务就很丑，感觉非常不协调；另外演讲者说 S&R 可以取代多个并行请求访问同一个 common cache 需要加锁的问题，我保持怀疑态度，除非又是类似 strand 这种东西

**C++ Weekly - Ep 487 - AI: Not Just Autocomplete** https://www.youtube.com/watch?v=acD9F8dzjik

- 演示 claude.ai 的功能？
- 看上去确实有点fancy

**C++ Weekly - Ep 318 - My Meetup Got Nerd Sniped! A C++ Curry Function** https://www.youtube.com/watch?v=15U4qutsPGk

- 开脑洞系列，curry 函数如果参数不够就返回一个柯里化函数部分，否则就直接执行函数；if constexpr 搭配 requires clause 直接亮瞎了
- 实际代码别用就是

**C++ Weekly - Ep 317 - Do You Really Understand Member Functions?** https://www.youtube.com/watch?v=4etjb2_KAaE

- 成员函数调用对应的汇编寄存器传递，第一个参数隐式的 this
- 成员变量的访问也是通过 this，const 成员函数中 this 都是 const-qualified
- 这些感觉应该都是基础了

**C++ Weekly - Ep 316 - What Are `const` Member Functions?** https://www.youtube.com/watch?v=bqd9ILyQRxQ

- 这一期比较水…
- 就是字面上的介绍 const member functions

**C++ Weekly - Ep 315 - constexpr vs static constexpr** https://www.youtube.com/watch?v=IDQ0ng8RIqs

- 这期有点意思，比较不同编译器和不同优化下 constexpr 和 static constexpr 的性能区别
- clang 和 gcc 在优化场景下还是有区别的，clang 优化场景下两个效果一样；gcc 则是跟数据量大小有关系
- 看了这期，我觉得小数据量，比如 64 bytes 以内？直接 constexpr 就好了，如果是用一个 unordered_map 这种那就带上 static

**async_simple无栈协程和有栈协程的定量分析** http://purecpp.cn/detail?id=2318

- stackless 的切换速度要显著高于 stackful，非常适合 IO 场景
- 函数调用深度显著增加后，stackless 的开销会高于 stackful，stackful 对调用栈深度不敏感，差不多到64级开始二者会有开始接近1倍差距

**阿里云块存储团队软件工程实践** https://zhuanlan.zhihu.com/p/572540319

- 整体感觉一般，coding style 方面多一些

**io_uring + Seastar** https://blog.k3fu.xyz/seastar/2022/10/03/iouring-seastar.html

- Seastar 当前使用 io_uring 的 pattern 作者觉得有改进的地方
- 对这两块都不懂，稍微瞄了一眼然后先 mark 一下

\#2

这周专门创建了 fawkes 的仓库，然后 Init 了一下 project。

现在光 Init 这部就花了不少时间，囧

希望下周有时间能开始搞起来，不然后面公司项目重建的时候真不知道拿啥做 http server，有些业务用 golang 还是有点蛋疼的毕竟。

---

好了这周就这样，下周见
