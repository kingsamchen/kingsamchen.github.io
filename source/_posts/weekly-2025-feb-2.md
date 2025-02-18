---
title: 一周杂记 in Week 2 Feb 2025
categories: CODE-LIFE
date: 2025-02-17 00:45:25
tags: [杂记]
---
本周（02/10 ~ 02/16）是2月份第二周，节后开始正式上班了

## Life

\#1

上一周刚发烧痊愈，这周又疑似得了诺如病毒或者胃肠道感染。

起因是周二晚上健身回家后洗了一小篮子小金橘，大概7~8个，一边看视频一边闲的蛋疼的吃金橘，但是我是直接连皮一起咀嚼后吞下的。

然后当天晚上睡觉就感觉胃部有点不舒服，胀气，总感觉胃被什么东西顶着，而且持续反酸。

凌晨大概四点多实在扛不住了起来催吐了一次，这一吐直接吐了好几波，吐出来的基本是没怎么消化的金桔皮和肉，吐完之后勉强终于睡着了。

但是估计是没吐干净或者胃部已经受伤了，所以周三上午起来后还是非常难受，所以请了假之后就又先继续睡觉。

睡到大概十一点起来先腹泻了一次，完事后休息了一会儿吃了包酸奶，喝了点水，然后过了没多久胃又开始难受了，并且一阵一阵有点想吐。

没办法又催吐了一次，这次连续吐了四波全都是之前喝下去的水 🤷‍♂️ 不过吐完之后好多了。

感觉应该没法正常进食了，就下楼买了一瓶可乐和雪碧，都是有糖的。

下午精神状态很差，可乐雪碧也勉强只能支撑；睡了一下午之后以为好多了，于是点了一个KFC汉堡外卖，结果吃完之后晚上睡觉时胃部又隐隐不舒服，但是没有之前那么严重。

而且可能是因为发低烧，全身感觉非常冷，烧的两个热水袋都顶不住...

因为需要睡一会儿换个姿势不然胃会难受，所以这一晚也没怎么睡好。

第二天早上一看手表的睡眠统计，持续睡眠时间只有3个多小时，非常糟糕 🤣 唯一好消息是感觉胃部好多了。

勉强支撑着完成了上午的工作，中午吃了易于消化的米粉，回到办公室就直接睡着了。午睡睡醒之后就感觉不对劲，拿耳温枪测了一下好家伙直接38.3℃。

找同事要了一粒布洛芬先顶着，然后在办公室一边摸鱼一边休息。摸到下午四点多事情处理的差不多了就直接请假回家睡觉休息了。

还好这个晚上睡觉比较踏实，估计因为烧也退了，周五差不多整个人也是恢复正常了。

这人年纪一大感觉就很脆皮，一点点身体不适能来回折腾好几天；以及，我和媳妇儿说以后我再也不吃金桔了，直接PTSD了 🫠

\#2

周五还在床上躺着呢，同事J给我发消息说同事A被刀了，我第一反应：啥？被杀了？

后来才反应过来：卧槽？真的有裁员啊？真的特么拿外包开刀啊

后来了解了一下，我们组被干掉两个，都是老张不喜欢的人。摊手

听说这次裁员本来是要裁US的，但是消息走漏之后US那边的人直接躺平怠工，高层一看这样不行就说不动US的人了；没想到直接一记回马枪，把外包拿来顶包了。

2F核心业务里被干了四个，好像都是老员工。

所以看起来99.99%的人应该都没法平稳工作到退休的，所以后面还是把计划改成趁着有工作多挣点钱吧。无语

\#3

周日要陪媳妇儿做2月的孕检，7:15一大早起来开车去市妇保城西院区。早起的汽车有停车位。

这次媳妇儿要做OTT实验啥的，整体上要抽7-8管血，分三批抽，每次间隔50分钟。

身上那个针眼和淤青感觉像是刚被容嬷嬷教育过一样 🤬

抽完血带媳妇儿吃了点新丰小吃，不过因为前面那个高浓度葡萄糖水消化不太行，媳妇儿吃啥都没太大胃口。

之后看结果还没出来，就步行几百米到了就在附近的西溪湿地一个入口，浅逛一下湿地。

说是浅逛是因为没打算入园掏钱，就白嫖一下免费的风景；其次是走太远容易累，到时候还得走回来。

只能说免费的风景确实千篇一律，开头那点欣喜劲过去之后就只想找个地方吃饭...奈何景区有家宋宴餐厅没位置了，所以又走回医院附近，吃了老娘舅。

吃完之后大部分结果也出了，媳妇儿找医生确认结果都没事之后我们就开车回家了。

要多嘴提一句，周日天气是真好，这车晒了半天上车后热的不行，谁能想到2月天我们在车里开冷气了 🤣

到家之后实在太累了，本来想着在床上休息一下结果几秒入睡，创造了我个人的入睡记录...

被老婆叫醒之后已经是两个多小时后了，一看都快五点了也累的没法跑步了我说那今天就休息吧，我这三十多半只脚快入土了，老身板遭不住啊

## Work

\#1

**C++ Templates 2nd | 27. Expression Templates**

- 把每个 expression/operation 用 clast emplate 包装，算是极端的优化情况了
- 主要针对的是频繁数值操作中小对象临时对象大量创建销毁，依赖 template + lazy eval 以及期望编译器的各种 inline + elimination 优化来生成高效代码

**CppCon 2022 | Understanding Allocator Impact on Runtime Performance in C++ - Parsa Amini** https://www.youtube.com/watch?v=Ctfbs6UVJ9Y&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=87

- 这个也挺一般的…
- 核心就是用 pmr 控制好 allocator 可以有 contaienr 的性能提升，但是这里只用了标准库的 pmr allocator adaptor + local array buffer 其他没了
- 内容实用度一般

**CppNow 2024 | Backporting C++ Safety - Taylor Foxhall** https://www.youtube.com/watch?v=0wtQ8hNDu30&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=35

- 讲的是 bloomberg 内部的 blazingmq 不能用太新的标准，然后他们怎么用不太新的标准同时保持 memory safety, type safety thread safety 这些
- 整体感觉一般吧…

**CppNow 2024 | C++ Zero Overhead Pass by Value Through Invocable C++ Abstractions - Filipp** Gelman https://www.youtube.com/watch?v=79Bb4L6txTw&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=36

- bloomberg 自己出的 function_ref，牛逼之处在于用了很多 tricks 可以让参数 pass by value with zero overhead

**C++ Weekly - Ep 394 - C++11's Most Overlooked Feature: Delegating Constructors** https://www.youtube.com/watch?v=G5ewfxJ0KMU

- 介绍 delegating constructor，说了几点注意
    - 用了之后不能再用 member initializer list
    - 被调用的 ctor 构造之后 lifetime 其实已经开始了，所以当前 ctor 适合做一些复杂的需要在 body 里干的事情

**C++ Weekly Ep 466 - invoke_r Should Not Exist** https://www.youtube.com/watch?v=GyAZg1LfPZo

- invoke_r 支持包裹的函数可以隐式转换到指定的返回类型
- 然而这个功能很多时候都是个坑…

**C++ Weekly - Ep 392 - Google's Bloaty McBloatface** https://www.youtube.com/watch?v=MY5DTDc3e-I

- 介绍 google 出的可以看 binary 容量占用的工具

\#2

这周状态不太行，只抽了点时间拿 Eureka/learn-cxx 做实验改了一下 cmake 配置，把一些 options 的判断放到具体的函数里面了，这样模块引用就不用在额外判断了。

并且为了让这个行为看起来更自然合理，把函数名字从 verb+noun 改成了纯 noun，多一些属性修饰的意味。属性里跟着 option 来调整是否开启行为要合理一些。

---

好了这周就这样，下周见
