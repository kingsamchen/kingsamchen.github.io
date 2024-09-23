---
title: 一周杂记 in Week 3 Sep 2024
categories: CODE-LIFE
date: 2024-09-23 21:46:23
tags: [杂记]
---
本周（09/16 ~ 09/22）是9月份第三周，本周一和周二是中秋假期，周三才开始上班

## Life

\#1

上个周日晚上在媳妇儿富阳值班室一起挤小床对付了一晚，周一上午就一起开车回来了。

媳妇儿开始了舒爽的 周一 ~ 周四的四天小长假。

周四傍晚的时候一起去了一趟西溪银泰的迪卡侬买泳衣泳裤，因为打算下周周中休几天假去千岛湖玩玩，水上项目那自然得安排上。

周四晚在西溪银泰吃的一家号称是吴越菜和浙江菜融合的店，但是我感觉味道一般吧，感觉有点偏预制，而且比起新白鹿性价比实在太低了。

另外因为西溪银泰和其他银泰属于不同老板，所以不能用喵街，停车费的话需要消费每满200元才能兑换一个小时停车券；不过还好多嘴问了一句，迪卡侬的消费可以直接抵扣两个小时，所以又跑回迪卡侬门店要了停车券 😂

周五一大早七点出发开车送媳妇儿去富阳，这次值班是本年度（也有可能包含以后 🤣）最后一次去富阳轮岗了。

考虑到此时返程会刚好遇到早高峰所以在值班室休息了到九点多然后直接在值班室开始上班，中午下班之后才开车回家，直接50分钟到家，完美

\#2

周五的时候试了一下 KFC 的汁汁和牛堡套餐，但是需要开大神卡才能享受30多块钱的折扣价，所以就索性开了一个大神卡。

结果到了周六中午还想再点的时候发现周末居然大神卡也没有折扣价了，我尼玛....

选了另外一个可以选汁汁和牛堡的四拼，但是价格一下就冲到50多了，还被迫选了冰淇淋，感觉一天都白练了。

KFC 这骚操作真的太离谱了

\#3

本周观影

- 电影 **黑洞频率 Frequency** 4/5 对父子情的刻画属实另辟蹊径，最后的happy ending算是弥补了John多年的坎坷人生。当年的李四叔的演技还是青涩阿
- 电影 **老狐狸** 4/5 廖爸这个老实人为人好的有点刻板了，这种长得高帅正直又有点窝囊的男人确实吸引女性阿233。小朋友在两个“爹”的思想对攻下做事既有原则又有手段，有点意思。另，老狐狸怎么给人一种北野武的感觉。。
- 电影 **异形 Alien** 5/5 开山之作，有限的特效依然能呈现出震慑心魄的效果；哪怕是最新的 Romulus 在相当部分结构上也直接“借用”这一部

## Work

\#1

**C++ Templates 2nd | 13. Names in Templates**

- 拖了一段又重新开始看书了
- 这章比较抠细节，把模板中涉及到的各种 names 相关的例如 lookup，dependent/non-dependent 都讲了一遍

**CppCon 2022 | The Observer Design Pattern in Cpp - Mike Shah** https://www.youtube.com/watch?v=4GU2YNsHrwg&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=36

- 比较传统的基于 OOP 的 observer pattern
- 我觉得现在是不是直接上个 signal/slot lib 会好一些…不提 std::function 是因为它不支持绑定多个 listerner/subscriber

**CppCon 2022 | C++ in the World of Embedded Systems - Vladimir Vishnevskii** https://www.youtube.com/watch?v=HFgq6EtVbDM&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=37

- 稍微科普了一下嵌入式开发中要操作的一些基础知识，GPIO MMIO  .etc 然后 C++ 有些新特性能提供帮助
- 同时因为标准库 STL 对于 heap allocation 和 exception usages 和嵌入式的要求不够匹配，可以使用第三方的 EASTL ESTL 某些场景可以考虑 boost

**CppCon 2022 | Back to Basics: C++ Move Semantics - Andreas Fertig** https://www.youtube.com/watch?v=knEaMpytRMA&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=38

- move semantics 基础
- 这个感觉也是重复了以前的 talk 没啥新内容

**CppCon 2022 | What Is an Image? - Cpp Computer Graphics Tutorial, (GPU, GUI, 2D Graphics and Pixels Explained)** https://www.youtube.com/watch?v=zi57OkPwzbk&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=39

- 讲了一些图像入门的东西，小哥还是很有激情的

**C++ Weekly - Ep 281 - N Times Faster Code With Parallel Algorithms** https://www.youtube.com/watch?v=NSamMd17Csk

- 正确使用 stl parallel algorithm 可以有 10x 的 performance boost

**C++ Weekly - Ep 282 - Quick Perf Tip: Don't Repeat Your Work** https://www.youtube.com/watch?v=IcoNGRL-K5c

- 避免重复构造/计算
- 如果这部分内容是编译期可以确定地，那就挪到 compile-time 利用 constexpr

**C++ Weekly - Ep 283 - Stop Using const_cast!** https://www.youtube.com/watch?v=iuLwHoMFP_Y

- 修改 const qualified data 是 UB，don’t do it
- 现在没有什么必须要用 const_cast 的场合，甚至对于之前 scott meyers 提到的 non-const/const 函数实现几乎一样利用 const_cast 来实现 non-const version 调用 const 的做法，也可以利用函数模板配合 `decltype(auto)` （since C++ 14）来解决，更不用说现在还有 deducing this

**CppNow 2024 | C++Now 2024 Session Preview - Interview With Zach Laine** https://www.youtube.com/watch?v=ZF3xwx_LCxo&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=2

- Zach Laine 是 boost.parser 的作者，这是一个 parser combinator 的库，刚被 boost 接受，目标是取代 boost.spirit
- 问了作者喜欢写 library 还是写 application，然后之前 cppcon 的感悟啥的
- 这老哥在这次 cppnow 上的 talk 有三个小时… 😂

**C++ coroutines: What happens if an exception occurs in my return_value?** https://devblogs.microsoft.com/oldnewthing/20210401-00/?p=105043

- tl;dr `return_value()` 是 coroutine body 的一部分，编译器生成的代码已经把这部分用 try..catch 处理并且设置到 unhandled_exception 中
- 所以结论是不用管

**C++ coroutines: Making the promise itself be the shared state, the inspiration** https://devblogs.microsoft.com/oldnewthing/20210402-00/?p=105047

- 提出想法：可以学习 shared_ptr 把对象指针和控制块压缩在一起，可以把 result holder state/refcounted 放到 coroutine frame 里面

**C++ coroutines: Making the promise itself be the shared state, the outline** https://devblogs.microsoft.com/oldnewthing/20210405-00/?p=105054

- 加 refcounted

**C++ coroutines: Building a result holder for movable types** https://devblogs.microsoft.com/oldnewthing/20210406-00/?p=105057

- result holder 支持 movable types

**strcpy: a niche function you don't need** https://nullprogram.com/blog/2021/07/30/

- BTW：这 post 是针对 pure-c 的，C++ 基本只需要围观一下就行；并且一些比较成熟的 C project 一般也会有自己封装的 c-string，比如 redis
- 大部分使用 `strcpy` 的场合都可以并且应该用 `memcpy`，后者更合理并且更容易写出正确的处理 buffer size 的C 代码
- 只有在 1) 能确定 src string 最长长度，并且这个长度小于 dest buffer size 2) src string 比较长，提前用 strlen 获取一次长度不够划算 3) 这个场景的操作会被反复使用
这三个条件满足的时候才用 strcpy，所以可以看出尽量别用这个函数

**How fast can you pipe a large file to a C++ program?** https://lemire.me/blog/2021/08/03/how-fast-can-you-pipe-a-large-file-to-a-c-program/

- 基本没啥意思，就测一下system pipe 的吞吐性能而已….

**How Template Template Parameters Can Simplify Template Classes** https://www.fluentcpp.com/2021/08/13/how-template-template-parameters-can-simplify-template-classes/

- 简要地介绍了一下 template template parameter 可以提供的帮助

\#2

这周做了一些善后工作之后，就顺利的把 nlohmann-json-serde 给 make public 了

Repo：https://github.com/kingsamchen/nlohmann-json-serde

反正自用着还不错，别人觉得有用自然好，觉得没啥用也无所谓咯 😎

\#3

抽了点时间稍微看了一下 [https://github.com/Eren121/CPP20Coroutines](https://github.com/Eren121/CPP20Coroutines/tree/master) ，感觉一般，代码质量有点糙，只比 medium 上的那些印度人写的垃圾文章强一些

Generator 的实现还是看 tatalarmar 那个版本把

---

好了这周就这样，下周见
