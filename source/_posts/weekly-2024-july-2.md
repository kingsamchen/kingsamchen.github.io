---
title: 一周杂记 in Week 2 July 2024
categories: CODE-LIFE
date: 2024-07-15 22:54:47
tags: [杂记]
---

## Life

\#1

之前周日发烧了，所幸恢复的比较快，周一开始就退烧了，但是还是持续性的咳嗽和连续几天的鼻塞

中间实在受不了连续两天晚上吃了泰诺，然后神奇的症状就缓解了。

到了差不多周四的时候所有症状都消失了，感觉整个人又恢复了 🤣

\#2

周六晚上吃完饭准备去买酱油和牛奶，骑着电驴刚出了小区就在公交车站边上的停靠着一排车的地方看到一只小橘猫，而且小橘猫就在一辆车车轮边上。车流来去，小橘猫有点无措的蹦跳穿插。

我觉得这样太危险了就趁着一个间隙直接抓着小猫的后颈把他转移到了边上的绿化边上。小橘有点受了惊，直接躲进了绿化带，整只猫“藏”到了一撮草里。

我感觉这橘猫有点虚弱，又四处找不到猫妈妈的影子，想了几分钟还是决定先救了这只小橘再说吧，不然弄不好他活不了几天了。

我把小橘放到布袋里，骑着电驴到了附近的一个凯特喵喵宠物医院。选这一家除了因为离家比较近之外还是因为这家医院养了好几院猫，让我比较有好感。

经过简单的筛查之后小橘大概一个月出头，除了营养不良之外没有其他的传染病，考虑到家里媳妇儿不让养，再加上小橘有点虚弱所以索性直接给他办理了住院，先在医院静养，有专业的医护每日照料喂食也比较方便。

到了周日晚上小橘状态已经比较稳定了，下一步就是给他找个家了。

希望小家伙给力点 😭

\#3

这周终于把愤怒的公牛看完了...整体上我个人的观感大概是 4/5 的水平，有点冗长..略有点流水，我个人更喜欢 Cinderella Man

## Work

\#1

**CppCon 2021 | Evolving a Nice Trick - Patrice Roy**

- 从如何处理一类类型设计函数重载开始将 03 ~ 20 这么多个版本的 meta programming 的演进

**CppCon 2021 | Why You Should Write Code That You Should Never Write - Daisy Hollman**

- 干货（trick）是真的不少
- 利用 static local variable + immediately evaluated lambda 来实现 thread-safe call once 甚至 abuse 中间抛异常代表初始化失败后续会重新初始化来实现 run n times…（正常做法还是应该用 call_once 和 mutex）
- 然后 parameters pack 的操作也不错

**CppCon 2021 | Data Orientation For The Win! - Eduardo Madrid**

- 讲了很多但是我还是没太get到 data orientation 要咋做…

**CppCon 2021 | Building an Extensible Type Serialization System Using Partial Template Specialization**

- 作者参考了 fmt formatter 的做法，用模板偏特化来支持类的序列化和反序列，需要用到 SFINAE/concepts 来针对一些内置类型处理，比如 array 和 string-like
- 顺带提了一嘴 ADL 实现的做法，一个例子就是 nlohmann-json 的做法
- 最后就是 static reflection 的提案

**C++ Weekly - Ep 266 - C++20's std::shift_left and std::shift_right**

- 介绍 c++ 20 引入的这两个算法
- 一开始以为是 rotate 的变种，后来发现是单纯的挤掉元素；需要注意的是，原来的元素如果不在调整后的区间内，那么状态是 unspecificed，因为内部应该使用了 move 来处理元素

**C++ Weekly - Ep 267 - C++20's starts_with and ends_with**

- C++ 20 给 string/string_view 增加的 starts_with 和 ends_with
- 不过有点搞不懂为啥不增加一个 util 函数来搞这个…

**Roi Barkan "Argument passing, Core guidelines and concepts”** https://www.youtube.com/watch?v=TEPKY6SoG7g

- 其实是老生常谈的话题了，不过里面提到的函数模板类的场景让 caller 来决定的情况还是可以考虑一下的，pass by value by default，调用者需要 pass by reference 的话就用 `std::ref()` 或者 `std::cref()`

**The Sad Truth About C++ Copy Elision** https://wolchok.org/posts/sad-truth-about-cxx-copy-elision/

- copy elision 的应用场合比较受限，因为不能过于激进对代码产生副作用；目前只有 RVO 和 NRVO 能稳定 elision
- 另外 post 里的例子我怎么看都觉得不适合 elide…

**static constexpr unsigned long is C++’s “lovely little old French whittling knife”** https://quuxplusone.github.io/blog/2021/04/03/static-constexpr-whittling-knife/

- 真语言学…就是探讨 inline static constexpr const 这些 qualifier 怎么排序好

**Optimizing string::append is harder than it looks** https://quuxplusone.github.io/blog/2021/04/17/pathological-string-appends/

- std::string append 的规格是 strong exception safety，这导致了一些优化实现上的困难。
- libc++ 通过检查 noexceptness 来作了一些 clever trick 但是都有一些问题

**Eliminating Data Races in Firefox – A Technical Report** https://hacks.mozilla.org/2021/04/eliminating-data-races-in-firefox-a-technical-report/

- mozilla firefox团队使用tsan的经验总结
- benign data races 虽然不会有什么 harmful 但是因为通常都需要观察生成的汇编代码才能确认，成本太高，甚至高过直接 fix 这个 data race，所以团队采用了 no data race, even benign data race, policy
- 分享了一些他们遇到的比较“有意思”的案例

\#2

这周在公司的项目上连续踩了两个大坑。

第一个是 httpd 对于 query string 中只有 key 没有 value，i.e. `?key1&key2=value2` 会直接把 key1 对应的值设置成字符串1...

我也不懂为什么要这么搞，但是这真的是一个神经病行为，花了不少时间 debug 就因为这...

https://github.com/omnigroup/Apache/blob/master/httpd/server/util_script.c#L833

另外一个是经典的 libcurl 的 expect 100-continue header。

虽然我们用的 http client 是 libcpr，但是 libcpr 只是在 libcurl 上包装了一层所以依然会有这个行为。

但是蛋疼的地方在于 libcpr 实现的时候没有考虑过这种情况，导致读取对端 100-continue response 上“有时候”会直接认为是所有的 response body 从而导致真正的 response body 被丢了。

不过这个 bug 的发生概率比较低，所以也是最近才发现。

另外之前对于 expect 100-continue header 的行为被知乎上某个回答给忽悠了，害得我一直以为这个请求是两个 request/reply 的行为...

实际上不管是看了 RFC 还是抓包看一下就会发现，这个 header 只是发生在一次“完整”的 request/reply：

1. 如果 client aware 自己的 request 需要 server 做 100-continue 响应，则应当先只发 http 请求中的 header 部分，这个 header 包含了 expect 100-continue
2. 然后等待 server 返回 100 continue status line 示意继续，这里只有 status line
3. client 继续把 body 部分发过去
4. server 返回正常的 response

所以 client 只是把一次请求分成两部分发了而已

因为我们用的 libcpr 短时间内不太好直接升级到最新版，所以一个简单的 fix 就是给 POST/PUT 请求加上空的 `Expect` header 来抑制这个行为

\#3

周六终于把线性代数及其应用 chapter 1.1 的习题终于做完了。。。orz

周日开始看 ch 1.2，这章正式引入了标准的 row reduction 的处理方法：

1. 先利用 forward phase 生成 echelon matrix
2. 再利用 backward phase 在前面基础上生成 reduced echelon matrix

ch 1.2 的课程内容部分也看完了，这周要抽时间把课后习题做了

---

好了这周就这样，下周见
