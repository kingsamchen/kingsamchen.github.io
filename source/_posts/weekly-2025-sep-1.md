---
title: 一周杂记 in Week 1 Sep 2025
categories: CODE-LIFE
date: 2025-09-08 22:53:46
tags: [杂记]
---
本周是9月份第一周，8月终于结束了。

## Life

\#1

周日晚上我妈带娃的时候整体都还行，但是周一开始娃就突然不爱吃奶了，我媳妇儿自己喂也不好使。有一次还是我妈抱着一边哄着一边喂。

没办法，丈母娘周二事情处理完之后就立马回到了杭州，但是娃厌奶的臭毛病还是没改。

我妈周三回去的时候娃还是要花各种办法才能正常喝奶，弄得媳妇儿很崩溃，最后决定娃喝一点就喝一点，不惯着，让她自己饿到不行认错 🤷‍♂️

然后拉锯了1-2天，周四娃就恢复正常了，而且奶量直接翻到了180-200ml每次。

丈母娘觉得娃是故意的，她本来想歇一周再回来结果都还没开始休息就被拉回来继续当保姆了

这周末到媳妇儿家的时候发现才几天没见，感觉娃又长大了，而且不仅好动了不少，话更多了，动不动就能自己咿呀呀呀讲个半天。

这周末的时候娃已经三个半月多一些了，时间真的是过得飞快啊

\#2

这周的跑步计划有两天在周四周五中午完成，感觉最近心肺有明显提高，中午跑心率也有明显进步。不过不知道是不是和睡眠变好了有关。

周五 10.1 kmh 的配速大部分时间都能把心率压在 160bpm 以内，而且运动之后下午还是感觉精力充沛。

\#3

这周稍微有点空闲之后周四就约了 keting 吃了个火锅，主要是闲扯。

因为担心 ZCP 的同事所以特意选了远一点的那家贵州黄牛肉火锅

期间主要吐槽老张，以及老张下课后这个老崔也是天天智熄操作

\#4

周三电影俱乐部看了今年柏林电影节金熊奖影片：性梦爱三部曲：梦

- **性梦爱三部曲：梦 Drømmer** 3.5/5 挪威女中学生的爱情萌芽 同性的安排感觉可能是导演的私货 这片大部分男角色都是npc

## Work

\#1

**CppCon 2022 | GPU Accelerated Computing & Optimizations on Cross-Vendor Graphics Cards with Vulkan & Kompute** https://www.youtube.com/watch?v=RT-g1LtiYYU&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=79

- 介绍 Vulkan和 Kompute 的
- 完全不懂，就当路过

**CppCon 2022 | Generating Parsers in C++ with Maphoon - Part 1 of 2 - Hans de Nivelle** https://www.youtube.com/watch?v=Ebp5UPi9ny0&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=83

- 教人写 parser 的

**CppCon 2022 | Lightning Talk: majsdown: Metaprogramming? In my Slides? - Vittorio Romeo** https://www.youtube.com/watch?v=vbhaZHpomg0&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=145

- 搞了一个支持 JS 执行的 majsdown，还支持自动把 cpp 代码块创建到对应 godbolt 上

**C++ Weekly - Ep 291 - Start Using `as_const`** https://www.youtube.com/watch?v=w996YXhkpkE

- C++ 17 引入 `std::as_const()` 比 static_cast 和 const_cast 都更推荐

**C++ Weekly - Ep 292 - Safely Using `goto` In C++** https://www.youtube.com/watch?v=ELCc7JYW49k

- 编译期会阻止一些不安全的使用
- 不过还是不要用 goto

**C++ Weekly - Ep 293 - RPG in C++20 Project: Major Updates!** https://www.youtube.com/watch?v=IWEdaC9evBc

- 移植到了 6501 commodore 模拟器上

**C++ Weekly - Ep 494 - Tool Spotlight: Modern Safe scanf: scnlib** https://www.youtube.com/watch?v=Y3yAVejU0Wk&t=14s

- scnlib 可以直接通过给定的 pattern 去 parse content
- 好像比我一开始想象的要高端实用一些

**C++ Weekly - Ep 495 - Custom Formatters for std::Format** https://www.youtube.com/watch?v=Rq4apPjnrsU

- 只有最简单的 formatter 介绍，然后提了一嘴 format string 是 compile-time的

**CppNow 2024 Lightning Talks | Submarines and Spies - Sherry Sontag** https://www.youtube.com/watch?v=lyw7vptZgKE&list=PL_AKIMJc4roVqkYxO--bh9Dn-wym8zGsY&index=2

- 纯闲扯型内容

**CppNow 2024 Lightning Talks | Fun? with NTTPs in C++ - Ben Deane** https://www.youtube.com/watch?v=Vi8aDhjDYvM&list=PL_AKIMJc4roVqkYxO--bh9Dn-wym8zGsY&index=6

- 恶搞 NTTP（non-type template parameter）
- 20/23 开始很多东西都可以塞 NTTP 里。。。包括类如果包含私有成员可以包一层 lambda 解决…

**Sometimes perfect forwarding can be too perfect: Lazy conversion is lazy** https://devblogs.microsoft.com/oldnewthing/20221123-00/?p=107443

- forwarding reference 把 derived* → base* 这个转换延迟到了 make_unique 内部，而在内部无法完成这个转换因为 derived 是 private inheritance from base…
- 虽然这个 private inheritance 是 by mistake

**How To Use Exceptions Correctly** https://thelig.ht/correct-exceptions/

- 原文说 exception 和 error code 是近乎等价只是表现形式不同，这点我是同意的
- 但是这俩只能处理 system error 显然是有问题的，用户违反原则输入不管是用 assert 还是直接 abort 都是有问题的，只能说这作者还是经历少了

\#2

这周开始研究如何给 fawkes 增加 request url query string 的处理。

看了 ada-url 和 boost/urls 两个库

综合考虑下来 boost/urls 可能更合适一些，因为 fawkes 的需求更多的是 url 配套的请求处理，需要更好的灵活度，而不是浏览器这种需要大量解析/校验 url 的场景。

不过具体怎么实现还在思考中

---

好了这周就这样，下周见
