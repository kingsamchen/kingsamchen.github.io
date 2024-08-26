---
title: 一周杂记 in Week 4 Aug 2024
categories: CODE-LIFE
date: 2024-08-26 23:21:54
tags: [杂记]
---
本周（08/19 ~ 08/25）是8月份的第四周，下周还有一周...

## Life

\#1

因为丈母娘来杭州呆上小半个月，所以之前的计划是这周周末一起去听媳妇儿找的一个叫 海山 的乐队致敬 beyond 的演唱会。

但是很可惜，周二还是周三的时候收到短信通知说因为档期问题，演唱会取消了... 😅

最后换了一个方案，看沈腾的抓娃娃。

电影还不错，后面讲，所以一行人周末还算愉快

\#2

周六去媳妇儿家，和丈母娘一起给媳妇儿施加压力说办宽带，终于搞了一个移动宽带，500Mb 两年500块，量大管饱

反正几个人都不玩游戏，移动的一些缺点就不算啥了

周六下订周日工作人员就上门安装了，新小区装起来就是挺快的，周日上午我还没开车到媳妇儿家的时候就装完了。

周六下单买的路由器很争气的在我前脚到了，所以很顺利地拿到路由器装上并且设置好。

以后去媳妇儿家终于不用把我的联通手机放飘窗上改善信号了

\#3

这周就看了抓娃娃一部电影

- **抓娃娃** 4/5 笑点和梗管够，媳妇儿和丈母娘看的都很开心。东亚家庭和教育的槽点还是点得很到位的，就是收尾的地方软了一点

## Work

\#1

**C++ Templates 2nd | 12. Fundamentals in Depth**

- 这一章把函数模板类模板的一些语法细节都讲了，感觉有点太细致了

**CppCon 2022 | Reflection in C++ - Past, Present, and Hopeful Future - Andrei Alexandrescu** https://www.youtube.com/watch?v=YXIVw6QFgAI&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=17

- 对于 reflection proposal 的一些介绍和想法
- AA 开头就说了这个 talk 比较 out of character，纯粹是一种 open for communication 的心态，所以干活没那么多

**CppCon 2022 | Breaking Dependencies - C++ Type Erasure - The Implementation Details - Klaus Iglberger** https://www.youtube.com/watch?v=qn6OqefuH08&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=18

- 很棒的一个 talk，把 type erasure 的常规技法讲的很通俗易懂
- 后面还做了 small buffer 和 manual dispatche 的优化，不过大多数情况下普通的实现应该是足够了

**CppCon 2022 | What’s New in C++23 - Sy Brand** https://www.youtube.com/watch?v=vbHWDvY59SQ&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=19

- 用具体的例子来过一下 C++ 23 new features
- 感觉这种路子还挺好的

**CppCon 2022 | 10 Tips for Cleaner C++ 20 Code - David Sackstein** https://www.youtube.com/watch?v=9ch7tZN4jeI&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=20

- 提了10个工程开发上的建议
- 仁者见仁智者见智吧….

**CppCon 2022 | Back to Basics: Debugging in C++ - Mike Shah** https://www.youtube.com/watch?v=YzIBwqWC6EM&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=21

- 调试入门教学，适合新手

**Lightning Talk: How to Win at Coding Interviews - David Stone** https://www.youtube.com/watch?v=y872bCqQ_P0&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=23

- 主要还是以戏谑调侃为主，看的笑死我了

**C++ Weekly - Ep 276 - C++20's Conditionally `explicit` Conversions** https://www.youtube.com/watch?v=CMmkoy24XhU

- 介绍 C++ 20 引入的 explicit (true/false) 的特性
- 有时候我们希望 implicit conversion 同时需要明确表达这个语义，就可以用这个特性；并且 clang-tidy 也是 aware 这个特性的

**C++ Weekly - Ep 272 - Hello World, Hello Commodore** https://www.youtube.com/watch?v=w2L3Vz5X8JU

- 就介绍了一下怎么往 commodore 模拟器的屏幕上写输出…
- 拉屎的时候可以看一下就当作排遣了

**A general state rollback technique for C++** https://www.jfgeyelin.com/2021/02/a-general-state-rollback-technique-for-c.html

- 核心是分离需要回滚的状态内存，给这部分内存做快照，需要的时候整块内存复原
- 实现一个专用的 snapshot-allocator，并且利用 RAII 在需要启用回滚内存的逻辑部分将所有内存分配转入到这个 snapshot allocator
- 文中还提到了一些坑点，最显著的是要注意不同的 allocation context 的内存不能混用，并且要注意 snapshot allocation 的地址前后需要一致，即从这个 allocator 分配的内存中某些成员会分配并指向其他 snapshot allocator 的地址，还原的时候要保证这部分内存地址也是一致的
- 作者 Github 开源了这部分分配器，有兴趣可以看一下代码

**__int128 的安全使用** https://zhuanlan.zhihu.com/p/375376949

- 对 __int128 的操作在优化开启的情况下会生成 SSE/AVX 的指令，这些指令都要数据按照 16-bit alignment，如果数据在嵌套中没有注意对齐就会出现 crash

**Composing callables in modern C++** https://ngathanasiou.wordpress.com/2023/03/05/composing-callables-in-modern-c/

- 利用 C++ 17/20 的特性实现 function composition，骚操作
- 不过看实现感觉能 back port 到 C++ 11

**Lambdas as const ref** https://belaycpp.com/2021/05/25/lambdas-as-const-ref/

- 用 const reference 绑定 lambda，传递的时候可以省掉拷贝
- 讲道理我觉得意义不大…..如果 capture by ref 或者是 capture-less，传值拷贝能产生的 overhead 应该是微乎其微并且大多时候应该是要能被 optimized out 的，sample 里的情况我倾向是其他原因，而且同一个 lambda 被来回复制本身就有点不合实际

**Creating other types of synchronization objects that can be used with co_await, part 1: The one-shot event** https://devblogs.microsoft.com/oldnewthing/20210309-00/?p=104942

- 系列第21篇
- 这篇开始介绍 synchronization primitives library 的设计实现

**Creating other types of synchronization objects that can be used with co_await, part 2: The basic library** https://devblogs.microsoft.com/oldnewthing/20210310-00/?p=104945

- 系列第22篇
- 这篇代码比较多，直接展示了 awaitable sync object 的一个 general design & impl

**Creating other types of synchronization objects that can be used with co_await, part 3: Parallel resumption** https://devblogs.microsoft.com/oldnewthing/20210311-00/?p=104949

- 系列第23篇
- 这篇将 coroutine work 的 resume 放到了 Windows 提供的线程池里，避免 resumption 时候阻碍其他 coroutine work

\#2

跟着上面提到的 CppCon 实现了一把 type erasure 但是没有 talk 里面的那么 general & optimized

https://gist.github.com/kingsamchen/f5efc20b5878f3a8307a742a80fad12d

\#3

这周发现 anvil 生成新工程的时候，生成的附带 tests 模块的 CMakeLists.txt 文件 jinja 模板渲染的有问题

稍微看了一下代码发现了问题就顺带修复了一下 https://github.com/kingsamchen/anvil/pull/20

\#4

最近想着把之前的 nlohmann json serde 给单独抽出来放到一个 repo，以前是为了公司项目写的 experimental 类代码，但是实践中发现还真是挺好用的，并且搜了一下发现 github 上没有类似的开源实现 🤣

所以打算独立出来开一个 repo，并且把 options 这部分调整一下，之前胃口有点大，像把 options 做成支持 composition，但是实现过程中发现和 omit_empty 的行为不好整合在一起，会很别扭

最近突然想到一个比较合理的 workaround，所以独立之后也打算照这个方向试一下

---

好了这周就这样，下周见
