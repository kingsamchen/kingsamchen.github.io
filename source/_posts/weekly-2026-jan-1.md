---
title: 一周杂记 in Week 1 Jan 2026
categories: CODE-LIFE
date: 2026-01-05 21:01:56
tags: [杂记]
---
本周（12/29 ~ 01/04）是 2026 年 1 月份第一周，Happy New Year！又活了一年

## Life

\#1

这周四是元旦，正式迈入公立2026年，也是秋宝的第一个元旦。

因为周二周三提前休假，所以本来考虑周一WFH的，但是因为suyang节后马上就要飞去US出差了，所以想了一下还是周一去办公室和XDM一起吃个中饭+晚饭。

晚上suyang选了一家比三分银更加正宗的贵州菜，菜的味道确实够地道，酸辣拉满，当晚吃完回家就拉了一次，第二天上午又拉了一次 🤣 不过可惜K指导已经在家了没来，不然指不定也要拉几次

周二中午专门抽了个时间和suyang一起吃了个阿郭，然后在 Afterglow 喝了杯咖啡，这次点的风味拿铁特别的醇厚。

下一次和suyang吃饭估计要到春节前等他从US飞回来了

\#2

12/31早上带着秋宝去蒋村卫生院打疫苗，结果我刚停好车还没上楼呢，媳妇儿就通知说流脑疫苗虽然都是国产的也有好几个牌子，之前在三墩社区打的牌子这里没有...

没辙，只能再开车到三墩社区医院。这个医院停车麻烦很多（主要车位少），而且人特别多，不过万幸也没花多久就整完了。

到家后没多久又开车送媳妇儿去她医院边上的一个浙菜馆，她们科室中午聚餐；不过我自己好像点的McD

元旦当天过得比较平淡，本来想把之前我妈买的大铁锅来出来上油开锅，但是清洗的时候发现怎么都没法把锅的污渍洗掉，遂作罢

2号一大早开车送媳妇儿去医院查房，中午在衣之家吃了杭帮面馆；讲道理这家面馆是我吃过最好吃的，最符合我口味的杭帮面，比小狗都要强。

傍晚去乐刻稍微做了一下恢复性有氧，结束的时候媳妇儿说想吃太合的东京烤肉，结果到的时候发现一堆人在排队，老婆嘀咕了一句：这店平时也没几个人啊

最后换成了刚开业不久的泉市牛肉汤，不过味道还行，但是总感觉味道太单一，而且他家的滑蛋牛肉饭居然是甜的。。。

三号下午上了第四节拳击训练营，这次给练爽了，打的都快虚了。

周日 WFH 在家补班，整体节奏还行

\#3

这周本来想和媳妇儿去看阿凡达3，但是一直找不到合适的时间

下周周末看看有没合适的

主要这电影也太久了...

\#4

假期间突然收到手机短信说，之前常去的宠物医院又有啥啥变更，后来买牛奶的时候发现哪家医院已经关门了，大门上张贴着招租的信息。

算起来这家医院开了都有好多年了，在这家救助了好几只流浪猫。

年中给汤包绝育的时候就发现之前有两个医生和一个护士离职了，其中一个医生还比较熟。

这个算一个时代结束吧

说起来可怜的小蛋也没能见到2026年的到来，只能祝愿他在喵星一切都好了 😞

\#5

这周假期鸭科夫玩爽了，每天都2-3个小时嘎嘎搜刮，代码都没啥心思写了；何况工作上现在处境岌岌可危，完全不想有任何给不值得的人做事的心态。

## Work

\#1

**CppCon 2023 | Effective Ranges: A Tutorial for Using C++2x Ranges - Jeff Garland** https://www.youtube.com/watch?v=QoaVRQvA6hI&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=18

- 整体上还算不错的 ranges/views 推销 talk
- 不过 views 有几个坑还是挺蛋疼的，尤其 filter_views 内部 caching，所以连带导致接口不能用 const-ref 传参
- 另外我始终觉得 views 用多了反而导致可读性下降，尤其是后面几个例子比较明显，以前一眼就能看出来干啥的现在要想一下

**C++ Weekly - Ep 513 - How Many Ways Can You End a Program?** https://www.youtube.com/watch?v=ki9omnMeYS8

- 正常退出的就不说了，另外尽量别用UB的方式，虽然我以前在 win 上老用这个…
- 按照“惨烈”程度，应该是 std::abort ← std::terminate(terminate handler 默认调用abort）← std::_Exit（正常退出但是几乎不做清理） ← std::quick_exit（内部调用 _Exit) ← std::exit
- 但是要注意 std::exit 不会调用局部变量的析构函数，因为他**不会触发 stack unwinding**

**C++ Weekly - Ep 512 - reinterpret_cast is Finally Fixed!** https://www.youtube.com/watch?v=JtFVyXQ00PQ

- reinterpret_cast 罪恶滔天，除了 C++ 20 引入了 bit_cast 之外C++26 又引入了 start_lifetime_as 来实现”显式声明某个对象生命周期开始“，减少使用 reinterpret_cast 的场景

    ```cpp
    #include <complex>
    #include <iostream>
    #include <memory>

    int main()
    {
        alignas(std::complex<float>) unsigned char network_data[sizeof(std::complex<float>)]
        {
            0xcd, 0xcc, 0xcc, 0x3d, 0xcd, 0xcc, 0x4c, 0x3e
        };

    //  auto d = *reinterpret_cast<std::complex<float>*>(network_data);
    //  std::cout << d << '\n'; // UB: network_data does not point to a complex<float>

    //  auto d1 = *std::launder(reinterpret_cast<std::complex<float>*>(network_data));
    //  std::cout << d1 << '\n'; // UB: implicitly created objects have dynamic storage
    //                                  duration and have indeterminate value initially,
    //                                  even when an array which provides storage for
    //                                  them has determinate bytes.
    //                                  See also CWG2721.

        auto d2 = *std::start_lifetime_as<std::complex<float>>(network_data);
        std::cout << d2 << '\n'; // OK
    }
    ```

- 要注意的是这个也是操作指针类型的，所以T直接填类型就行
- 另外一旦 start lifetime as，就不能用原来的类型在进行读写了，这反而会变成UB

**C++ Weekly - Ep 215 - An RPG in C++20: Part 3 - Handling Command Line Parameters** https://www.youtube.com/watch?v=U_2WDRTdImk

- 直播写代码

**C++ Weekly - Ep 214 - Epic 10 Hour Port of Doom to C++** https://www.youtube.com/watch?v=0dkzLdqH9V4

- 这个牛逼了，直播10个小时，把 crispy doom 移植到 C++，并且最后还至少跑起来了
- 前面十几分钟看的比较认真，后面直接选了一点跳着看了

**C++ Weekly - Ep 213 - CTRE: Compile Time Regular Expressions** https://www.youtube.com/watch?v=8aRfJp1oZGA

- 介绍的 CTRE
- 就是不知道 runtime performance 比起其他库怎么样，如果 runtime 各项指标都能进top3，那真的赶紧进标准库吧 XD

\#2

这周抽了点时间打算给 fawkes 加一下 timeout 支持。

一开始的打算是先做 connection-level timeout，这个算是比较常见了

然后再考虑一下 per-operation cancellation 咋个支持，ASIO 自己支持的方案还不少的。

后者想了好久没有特别好的整合思路，所以索性先把前者做了吧。

前者做的时候发现还有一点点坑，问了 GPT/Gemini 几个流行的（其他语言）的框架的设计情况之后，自己也有了一个方向。

目前整体差不多了，因为总共改动没几行，不过因为假期主要都在放松（游戏）了，所以估计要下周才能正式 PR 🤡

---

好了这周就这样
