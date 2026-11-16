---
title: 一周杂记 in Week 2 Nov 2026
categories: CODE-LIFE
date: 2026-11-16 23:19:45
tags: [杂记]
---
本周（11.9 ~ 11.15）是十月份第二周。

## Life

\#1

最近天气变冷了，媳妇儿都不愿意自己骑电驴去学校了，哪怕紫金港离家就2-3公里。

所以我最近早上开车去上班捎带媳妇儿去学校的频率就增加了

不过开车上班时间确实舒服一些就是下班太堵了，而且下班高峰开车容易把压力指数弄高

\#2

这周六拳击课，练的爆炸了，晚上虽然一点多就睡着了，但是HRV和RHR暴跌，导致周日第二天恢复不太行，不过也这个也算预期内

不过确实练的好爽~

\#3

这周周六周日两天中午都跑去浙大食堂吃饭了，因为要送媳妇儿去学校那就顺带吃个饭，也省得我妈中午还要给我做饭，给她减负。

毕竟要照顾秋宝就已经非常不容易了。

不过实话说浙大食堂味道还是不错的，而且学校也有补贴，能省不少钱呢。

## Work

\#1

**CppCon 2023 | Leveraging the Power of C++ for Efficient Machine Learning on Embedded Devices - Adrian Stanciu** https://www.youtube.com/watch?v=5j05RWh1ypk&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=82

- 感觉没啥内容，连具体到哪个特性合适都没，就单纯说连 rasperry pi 都能拿来跑，够牛逼吧的意思。。

**CppCon 2023 | Concurrency TS2: Improved C++ Concurrency and Lock-free Programming - Maged Michael & Michael Wong** https://www.youtube.com/watch?v=wS8xFF-Ha0s&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=84

- TS2 里的 RCU 和 hazard pointer 都已经进标准了，等 C++ 26 library update 提供把，这两个都是为 lockless / low latency 准备的
- 另外还有synchronized_object<T> 这种内嵌 mutex 的东西，记得 folly 就有这个设施

**CppCon 2023 | Better Code: Exploring Validity in C++ - David Sankel** https://www.youtube.com/watch?v=wxzQXj6WW2E&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=85

- 又是有点哲学意味的分享
- 核心是，validity 本质是和 operation 挂钩的，不管是 nullptr 是否 valid 还是 empty vector 是否 valid 都是取决于后需要执行的关联 operations
- 后面展开的 substance / essence / meaning 都是这个核心论点展开

**CppCon 2023 | Lightning Talk: Un-Undefining Undefined Behavior in C++ - Jefferson Carpenter**  https://www.youtube.com/watch?v=S49fKs0Bv1Q&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=87

- 核心是 obfuscate the compiler 让 compiler 不好确认是不是UB，比如把代码桥接到 .so 或者用搞内存页的方式跑代码。。。
- 太IMBA了，权当娱乐吧

**C++ Weekly - Ep 558 - C++23's from_range and ranges::to** https://www.youtube.com/watch?v=KPKYN6A1VcQ

- 省流，两个用法一览

    ```cpp
    std::vector<int> v{1,3,5,7};

    std::vector<int> result(std::from_range, v | std::views::drop(1));

    auto result2 = result | std::views::take(2) | std::ranges::to<std::vector<int>>();
    ```

\#2

这周把 fawkes-based 的 scheduler-api-v2 正式上 Go 了，结果出了一个插曲：部署之后 OPS 忘了设置 DNS 和 cert 导致内部自动化验证测试失败了。

和 OPS 研究了一下之后她加上了 dns/cert 结果因为生效需要时间，时间还没到就直接更新 ngx 接流，导致自动化验证又失败了。

更糟糕的是公司级别的 bcv 告警了，NOC team 直接来问有没有 customer impact，还好 Go 环境上没有客户，否则直接拉 warroom 了。

过了几分钟后 retry 了一下所有自动化验证都过了，悬着的心才放下来...

\#3

给 api v2 的 init components 专门写了一个改版的 AtExitManager，并且全局做法也是重新调整过的

直接看实现把 https://github.com/kingsamchen/Eureka/pull/58

---

好了这周就这样，下周见
