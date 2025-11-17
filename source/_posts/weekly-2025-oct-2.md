---
title: 一周杂记 in Week 2 Oct 2025
categories: CODE-LIFE
date: 2025-10-13 22:20:50
tags: [杂记]
---
本周（10/6 ~ 10/12）是十月份第二周，因为标准假期后给自己加了三天假期，所以这周也是休假日。

## Life

\#1

10/6 这天全家在老婆家和娃一起过了一个中秋，吃了顿中秋饭，虽然媳妇儿和娃没吃上，但是也算还行吧 🤣

6号傍晚就开车一家人去东站坐高铁回老家，7号中午外婆90大寿寿宴。

寿宴是十一一周前临时决定的，所以就办了三桌，来的都是外婆这边非常亲的亲戚，好多比较熟的都是确实自从春节之后就没见到了。

给外婆过完寿之后再外婆家坐了一会儿，然后又让我妈开车送我到车站准备回杭州了。

因为8号是最后一天假期，所以7号傍晚回杭州的高铁坐满了人，过道上也都是人，还好我提前候补了一等座。

\#2

因为娃下周就要搬到拱墅这边的家来了，老婆对18年买的海尔洗衣机一直不满意，其实我也不太满意不过也能将就用所以用了这么多年。

这次终于下定决心要换掉了。

调了一圈之后选了松下洗烘套装，原因就这么几点：

- 老家用的也是松下，不过是洗烘一体，所以松下至少有切实体验
- 混合模式也能一个小时多一点洗完，比起海尔的1h48mins，时间缩短太多了
- 松下号称有个超洗净功能，这次核心就是冲着这个去的，毕竟娃的衣服洗涤剂一定要少残留
- 单独烘干机可以用热泵，少毛絮，更省时省电

所以趁着有国补赶紧下单了，差不多1w4不到。

预约了下周一直接上门安装

\#3

9号晚上机缘巧合，直接用航空箱抓到了奶黄包，把它带到了医院做了check-in并且预约了第二天绝育手术。

不过因为我突然忘了之前给他起名叫奶黄包了，所以check-in的时候随便想了一个叫旺财...

anyway，整体检查下来都是正常的，所以第二天就正式做了绝育手术，正式成为公公了。

奶黄包之前是皮蛋的好基友，非常怂，但是有点好是不会挠你。不过这次来医院做检查也是开始应激的非常厉害，直接把一个乳牙弄断了，不过还好住院几天后慢慢缓和下来了。

下周住满7天后就可以带他出院放归了。

\#4

周日晚上开车去东站接我妈，下周开始她就要进入带娃节奏了，并且这次要持续2个月。

希望她先把最基础的喂奶和哄睡给快速上手了先。

\#5

十一抽空和媳妇儿在家终于又看了一次家庭影院

- **An Inspector Calls(2015)** 3.5/5 最后的那个“上帝审判”的转折真的...另外说教味儿确实有点重了

## Work

\#1

**CppNow 2025 | import CMake; // Mastering C++ Modules - Marching Towards Standard C++ Dependency Management** https://www.youtube.com/watch?v=uiZeCK1gWFc&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=24

- CMake 支持 modules 的一些介绍，干货不少
- 目前 generator 里支持 modules 的只有 Ninja 和 VS；同时 CMake 还要做很多依赖扫描的事情
- 乐观得看，5年内如果包管理 metadata 这些能敲定，modules 的应用应该会迎来一个明显的上升趋势

**CppCon 2023 | Plenary: Cooperative C++ Evolution - Toward a Typescript for C++ - Herb Sutter** https://www.youtube.com/watch?v=8U3hl8XMm8c&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=7

- 正戏在后半段，前半段有点大炮打蚊子的感觉，尤其逮着 enum 这么一个小点大做文章
- 在 Herb Sutter 的设想中，cpp2 就是 Typescript 之余 Javascript，100-compatible 提供丝滑的迁移和混编，同时给 JS 自身提供一些进化方向
- 不过我不觉得 cpp2 自身能有太大成功，因为相对而言，cpp 没有 javascript 这么拉跨啊

**C++ Weekly - Ep 254 - C++23's signed / unsigned size_t Literals** https://www.youtube.com/watch?v=iadZWeOWr0U

- `z` 和 `uz`

**C++ Weekly - Ep 249 - Types That Shall Not Be Named** https://www.youtube.com/watch?v=jWgSkM8N0Bc

- lambda / local struct / non-named struct 这些都没必要给类型赋予名字，而且实际操作起来也麻烦

**C++ Weekly - Ep 250 - Custom Allocation - How, Why, Where (Huge multi threaded gains and more!)** https://www.youtube.com/watch?v=5VrX_EXYIaM

- 正确使用 pmr allocator 可以有明显的性能提升
- 但是如何正确使用 pmr 估计是个有挑战的活，目前对这个也不是很熟

**C++ Weekly - Ep 251 - constexpr Parameters With C++20's CNTTP** https://www.youtube.com/watch?v=iIizz2bbkiA

- Class non-type template parameter，也就是说有些符合要求的 class 可以作为 template parameter 了
- 需要 C++ 20 支持

**C++ Weekly - Ep 252 - Real World PMR: 2-3x Faster JSON Parsing with Custom Allocation Strategies** https://www.youtube.com/watch?v=R5PHRY5Pnlg

- boost.json 可以利用 pmr allocator 来大幅加速性能
- rapidjson 的 custom allocator 不能使用标注的 std allocator，而且使用 custom allocator 的加速效果不如 boost.json/pmr

**C++ Weekly - Ep 253 - C++20 is Official! How To Get Your Copy of the Standard** https://www.youtube.com/watch?v=fWWBG7zekL8

- ISO 的标准要钱，可以看免费的 draft…

**Lifetime extension of temporary objects in C++: common recommendations and pitfalls** https://pvs-studio.com/en/blog/posts/cpp/1006/

- part 3 充满干货，各种使用的 caveats 需要注意

**Using final in C++ to improve performance** https://blog.feabhas.com/2022/11/using-final-in-c-to-improve-performance/

- 使用 `final` 可以帮助 de-virtualization 提升性能
- 但是要注意，调用函数参数必须要是 derived class 本体才行，final 这里的作用是让编译器知道这个 derived class 不可能再有 more derived class 了，所以放心直接用 direct call，所以感觉使用场景有限
- 后面用 template 的版本其实可以考虑设计之初就不要引入 virtual-call 这套，比如考虑 CRTP 或者 concept 来做 ad-hoc polymorphism

\#2

这周抽了点时间研究了一下 gin / cinatra / pistache 的中间件实现。

然后在这个基础上构思一下怎么给 fawkes 加中间件。

gin 的实现很有意思，通过 recursion call 来实现 pre/post process 的处理时机，但是对于 fawkes 来说因为采用了 stackless coroutine，比较担心最终的 route handler 处在比较深的调用栈顶会对性能有影响（如果 middleware 用得比较多的情况下）

其二是总觉得这种方式不太清真，看代码就可以发现 gin 自己用来 track middleware handler 的 index 从调用结束开始的时候肯定是不准的

pistache 的就不说了，很简单的实现，类型擦除的 `std::function` 组成的链，缺点就是没法做 pre/post 的同时支持。

不过可以在这个基础上展开一下，就是自己实现类似 function 这种 type erasure，支持 pre/post 两个接口，但是缺点是需要自己做很多微优化，至少要做 SSO 减少动态内存分配，所以感觉还是有点麻烦。

第三个是 cinatra 的实现，这个实现有点意思，所有 middleware 全都是 compile-time 的，通过 variadic template args 提供，更秀的是，利用 move-capture 塞到 lambda 里，然后通过 type erase 存储到 `std::function` 中。

不过他的实现也有两个问题：

第一个是他的 post process 也是按照 middleware 注册的正序来做的，通常这种场合 post process 应该按照逆序来处理，不过这个也容易处理，可以把不定参数换成 tuple，然后把 index 倒过来就行

第二个问题比较麻烦，是没法支持 global middleware 和 per route middleware 两种

目前在考虑 fawkes 能否做一些尝试，在 cinatra 的基础上做到 global 和 per-route 的同时支持。

希望下周有时间可以搞这个

---

好了这周就这样，下周见
