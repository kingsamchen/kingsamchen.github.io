---
title: 一周杂记 in Week 2 Sep 2026
categories: CODE-LIFE
date: 2026-09-14 21:47:11
tags: [杂记]
---
本周（9.7 ~ 9.13）是9月份第二周。

## Life

\#1

周一晚上 Ricardo 请了我们几个人吃了向东海，包厢低消要1200，还是不低的。

不过向东海自身是台州菜，所以海鲜随便点点也很容易超，整体上向东海的海鲜品质能和老邻舍对打，还是不错的

周日晚上 Miracle 请 Ricardo 我还有chao吃火锅，但是 chao 临时有事没来

那家重庆火锅感觉确实算是比较不错的，微辣我也可以接受，就是感觉牛羊肉一般，但是牛筋和虾不错

一个插曲是 Miracle 来的路上车被追尾了，然后他还和他自己媳妇儿吵起来了，囧，不过最后他媳妇儿还是来了。 🤣

\#2

周二晚上花了不少时间给老婆的电脑弄好了 Codex/ChatGPT app，方便她读博的时候做一些实验方案设计

老婆深度使用了 ChatGPT 之后大为赞赏。

因为我给媳妇儿用的方案是 clash 走系统代理，然后设置专门的规则，ChatGPT 的几个域名都走代理

但是这个方案我觉得不太合适我，所以我换了一个思路，我自己的电脑上，我装了一个 ProxyBridge，然后把 ChatGPT/Codex 相关进程名的流量都导到梯子上，当然要跳过 localhost 这些

这样好处是我的 clash 可以不作为系统代理，而且可以开全局。主要我更喜欢具体的分发规则，基于 Geo 和别人整理的规则总感觉不适合我

\#3

周五醒来公司又搞了一波 layoff，看人数的话，我们大组应该已经都超过10%的比例了，US=6/CN=2

小道消息说后面还会有一两波，我直接：???

\#4

周六下午拳击课，花了不少时间在矫正勾拳姿势上，感觉还是需要继续练习啊

另外最近迷上了B站上一个讲解 POI 的UP主，我觉得他讲的非常对我口味 https://www.bilibili.com/video/BV1GKMRzjE22

这周已经听了两季了，而且每季看完我都会给他充电25块钱

## Work

\#1

**Boost Libraries | Promise-Cpp with Boost.Beast** https://www.youtube.com/watch?v=YnTaumB5HVM&list=PLJDO7P5jAoXznYam4ucdMMcKJky8UHbhC&index=4

- 主要的介绍点是用 promise-cpp 这个库，这个库还是一个中国人写的
- 不过我更推荐 continuable 这个库，只要C++14就行，而且封装的更优雅；当然如果能上协程直接用协程好了

**CppCon 2023 | Cache-friendly Design in Robot Path Planning with C++ - Brian Cairl** https://www.youtube.com/watch?v=Uw7FF5MLxZE&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=63

- std::map/multimap 的缓存友好型太差，换成了 std::unordered_map，但是还有提升空间
- 最后都换成了 std::vector 变成邻接矩阵的表示，加上 reorder 优化空间性
- 最大的提升时间少 data cache miss

**C++ Weekly - Ep 548 - Intro to C++26 Contracts** https://www.youtube.com/watch?v=_H-RfRipIY0

- C++ 26 的 contracts 介绍，目前 gcc trunk 还是初步支持版本
- 默认情况下 contracts violation 会直接 abort/terminate，不过有编译选项可以控制最终行为

**C++ Weekly - Ep 85 - Fuzz Testing** https://www.youtube.com/watch?v=gO0KBoqkOoU

- llvm 提供了 fuzz testing 的接口

**C++ Weekly - Ep 84 - C++ Sanitizers** https://www.youtube.com/watch?v=MB6NPkB4YVs

- 介绍 address sanitizer 和 memory sanitizer
- 那个时候 gcc 还不支持 memory sanitizer

**C++ Weekly - Ep 83 - Installing Compiler Explorer** https://www.youtube.com/watch?v=I2cKVRzJhS0

- 如何在本地 setup compiler explorer
- 现在这个需求应该很少很少了吧

**C++ Weekly - Ep 82 - Intro To CTest** https://www.youtube.com/watch?v=ZlMbqFcJEzA

- Travis CI 上如何设置 CTest
- Travis CI 现在应该已经无了吧？

**Composing callables in modern C++** https://ngathanasiou.wordpress.com/2023/03/05/composing-callables-in-modern-c/

- 给了两个方案，一个是纯手工递归 compose http://coliru.stacked-crooked.com/a/a4a6937a77c2660a
- 感觉可以基于这个手工版本搓一个 `operator |` 语法糖 🤔
- 另外一个借用 C++ 20 `ranges::view::transform` 做包装，不过输入也需要用 `ranges::single_view{}` 包装一下 https://godbolt.org/z/b7q7z69oY

\#2

这周迁移 httpd 在处理前置任务，其中一个是 fawkes middlewares 的机制不是完全的 onion model，即：如果 middleware-a 的 pre_handle 执行了，那么它的 post_handle 也应该被执行

fawkes middlewares 的设计下如果 pre_handle 返回 abort 会直接跳过 post_handle，对于一些做审计日志相关的场景就无法适配了

研究了一下之后发现主流的 rest framework 的中间件都是遵循这个 onion model 的

那没什么好说了，改吧

不过因为现在有 AI 了，所以直接让 AI 出了个方案，我结合现有代码 review 之后觉得差不多就让它实现。

虽然实现有很多地方都需要我自己来做微调，但是90%的代码基本都是符合我的要求的，所以效率确实提高不少，尤其是对我这么挑剔的人来说。

\#3

下面是一些变更 backport 到了公开版的 fawkes

- [Update compiler conf & clang-tidy rules for better alignment #30](https://github.com/kingsamchen/fawkes/pull/30)

这个 PR 调整了编译告警和 tidy rules；把 `io_thread_pool::stop()` 变成 no-throw，因为 Windows 实现上有概率是会抛异常

（我突然发现这个 PR 里的 fix 是不对的，其中一个 throw 的话会跳过其他的）

还加了 vcpkg 的 binary cache，减少每次要完整拉取的开销

- [Make fawkes/request safe on copy- #31](https://github.com/kingsamchen/fawkes/pull/31)

request 的 `path_params` 之前 value 也是 `string_viwe`，但是 ref 的是 request 自己的 `path_`，这会导致 request 复制之后 value 指向错误

- [Use boost/small_vector for path params- #32](https://github.com/kingsamchen/fawkes/pull/32)

老早之前想做的，其实很简单，小 vector 直接用 `boost::small_vector`

---

好了这周就这样，下周见
