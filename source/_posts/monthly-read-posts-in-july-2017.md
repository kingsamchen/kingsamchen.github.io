---
title: Monthly Read Posts in June 2017
categories: PROGRAMMING
date: 2017-08-01 23:13:52
tags: [cppcon, tcp, c++, gdb]
---
[Starting a tech startup with C++](https://medium.com/swlh/starting-a-tech-startup-with-c-6b5d5856e6de)

又名，Facebook 开源项目宣传广告。

不过实话说，以 Facebook 写 C++ 那部分代码的恣意姿态，如果团队的平均水平够不到中上的槛，弄不好项目还没发布自己就先挂了。

Andrei Alexendrescu：我仿佛听见有人在背后黑我...

---
[Tips for Productive Debugging with GDB](https://metricpanda.com/tips-for-productive-debugging-with-gdb)
[CppCon 2015: Greg Law " Give me 15 minutes & I'll change your view of GDB"](https://www.youtube.com/watch?v=PorfLSr3DDI&t=24s)

GDB Tips...

第二个是 CppCon 2015 的一个 talk，感觉还凑合吧。不过实话说，15分钟我都能撸一发了，何必浪费时间在这个上...上个靠谱的前端不才应该是正确的姿势么...

---
[CppCOn 2015: Visualize Template Instantiation](https://www.youtube.com/watch?v=PorfLSr3DDI&t=24s)

这个 talk 的团队基于 Eclipse 做了一个 Cevelop，杀手锏功能就是 able to visualize template instantiation

不过比较蛋疼的是演示关键部分的时候，摄像机没有给对位置...

（希望 JetBrains 早点吧这个功能抄过去...）

PS：这个 speaker 神似我的初中语文老师....

---
[A better date and time C++ library](http://mariusbancila.ro/blog/2016/10/31/a-better-date-and-time-c-library/)

安利了 Howard Hinnant 写的一个 date lib。

而且据说这个 lib 很有机会进入标准库；现在标准库的 chrono 只是针对 timing 设计而没有对 date 的有力支持确实太蛋疼了

---
[理解基于 TCP 的应用层通信协议](http://www.jianshu.com/p/71cf3ce064b8)

大水文一篇，基本没有一点营养。

不客气地说，现在国内开发者刊物上，大部分内容都是这种没有什么营养、容易误导读者，而且还浪费时间的文章。

挂出来批判一下

[就是要你懂 TCP](http://jm.taobao.org/2017/06/08/20170608/)
[就是要你懂 TCP | 最经典的TCP性能问题](http://jm.taobao.org/2017/06/01/20170601/)
[关于TCP 半连接队列和全连接队列](http://jm.taobao.org/2017/05/25/525-1/)

阿里中间件团队官方博客的三篇 TCP 相关分享。

算是有水平的内容文。

第一篇介绍了一下 TCP 的基础内容；第二篇介绍了 delayed ack 和 nagle's algorithm；第三篇涉及 sync queue 和 accept queue 的处理问题

[TCP Peculiarities for Games, part 1](http://ithare.com/tcp-peculiarities-for-games-part-1/)
[TCP Peculiarities as Applied to Games, Part II](http://ithare.com/tcp-peculiarities-as-applied-to-games-part-ii/)

一个游戏服务端开发大牛的两篇关于游戏服务端 TCP 开发相关的文章。

两篇文章内容不光已经涵盖了前面阿里中间件团队的前两篇内容，还谈到了一些容易被忽略的点，例如 Head-of-Line Blocking。

第二篇还专门针对游戏服务端的业务特点，给了一些建议