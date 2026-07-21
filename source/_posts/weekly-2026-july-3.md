---
title: 一周杂记 in Week 3 July 2026
categories: CODE-LIFE
date: 2026-07-21 00:33:45
tags: [杂记]
---
本周（7.13 ~ 7.19）是7月份的第三周，这周实在太热了，台风一走直接变到酷暑高温。

## Life

\#1

台风结束之后（虽然杭州本身也没收到什么影响）杭州立马进入了酷暑状态。

不过也合理，这都已经7月下旬了，按照往年看，7月下旬和8月上旬是杭州一年中最热的时候。

这周热到有几天我甚至直接开车去公司了 囧rz

\#2

周中有天老婆把秋宝哄睡之后忽悠我开车去寺坞岭，说是晚上10点多可以看到绝美星空。

结果开了块一个小时到了目的地，还真是一个非常土的半山腰，而且只能看到有限的星星，绝美星空只能靠自己想象力了。

更蛋疼的是半山腰蚊虫特别多，各种吓人的昆虫，上了个厕所里面看到的虫子都一度让我尿不出来。

最多待了半小时后就赶紧回家了，结果开出去没多久车里混进来一直蟋蟀擦到我大腿，给我吓个半死，还好那会儿还没上高架快速路，半路停车先把虫子给撵出去。

尼玛这破地方以后再也不来了。

\#3

因为媳妇儿8月份就要去邵逸夫实验室干活了，而7月最后一周要先去培训1-2天，所以之前令人期待的暑假其实就变成了一个月而且只剩下了两周。

于是媳妇儿决定这周日带上秋宝，一家人回灵溪<strike>避暑</strike>

其实我是不太想回去的，主要这大厦天灵溪也热成狗阿，有啥好玩的，而且秋宝也不一定适应；再加上我在家办公一周估计也挺难受。

不过没辙，还是跟着一起回去吧，于是周日一早收拾差不多就一家人开车去东站了。

这次为了放边我直接把车停东站P2地下停车场，基本就是拿钱换体验。主要上次五一停媳妇儿老医院实在有点折腾了。

\#4

这周训练营因为周日要回家，所以换成了周六下午。

不过实在太热了，打到最后半小时实在没体能了，高鞭腿也完全使不上劲。

这次两个小时不到我喝了四瓶水：一瓶农夫山泉，一瓶佳得乐，两罐魔爪。

\#5

因为最近几周连日风雨，所以太久没去猫舍那边。

周六打完拳回来发现之前他们的饭碗都发黑发臭了，于是傍晚直接扔了从家里拿了一个干净的IKEA的碗替换上，喝水碗也清洗了一下

其实也是主要碰到了皮蛋，感觉他还是有点认识我的，处于那种想靠近但是有点怕怕的状态，所以想着看在皮蛋的面子上清理一下猫舍，又给他换上了新的猫粮和罐头。

走的时候还看到皮蛋吃得津津有味。

晚上去药店给我妈买海露滴眼液的时候经过，还看到皮蛋躺在草丛边上休憩，有点意思。

## Work

\#1

**CppCon 2023 | How Visual Studio Code Helps You Develop More Efficiently in C++ - Alexandra Kemper and Sinem Akinci** https://www.youtube.com/watch?v=Soy3RjGYUJw&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=49

- vscode 的新品宣发会

**CppCon 2023 | Coroutine Patterns: Problems and Solutions Using Coroutines in a Modern Codebase - Francesco Zoffoli** https://www.youtube.com/watch?v=Iqrd9vsLrak&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=48

- 分享 meta 使用 folly 提供的 coroutine 的设施，整体比较短
- folly 的 coroutine 是 sticky executor，并且提供了一些 handy functions 减少无用
- 一些比较有代表性的 coroutine 的坑点
    - lazy 下只有在 co_await 才会执行，要特别注意生命周期
    - 并发的 coroutine task 要考虑异常出现时的处理，并且因为 destructor 没法变成 coroutine 所以 scope guard 这里无用
    - 要特别注意锁的使用，轻则降低吞吐性能，重则导致死锁
    - 如果提供了 blockingWait/blockingRun 这些能强制同步执行 coroutine 的设施，一定要注意析构里不要使用否则会出现互相等待的调度死锁

**CppCon 2023 | Back to Basics: Testing in C++ - Phil Nash** https://www.youtube.com/watch?v=7_H4qzhWbnQ&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=47

- Catch2 的作者给新手上课：（单元）测试应该是怎么样的，以及一些最佳实践

**std::initializer_list in C++ 2/2 - Caveats and Improvements** https://www.cppstories.com/2023/initializer_list_improvements/

- initializer_list 最大的问题就是它会在背后创建一个 hidden array，把你给的值靠进去，这样才能保证迭代器访问的是一块连续的存储
- 这就会带来几个问题：
    - 返回 local 的 initializer_list 几乎总会有 dangling reference，因为 hidden array 销毁了
    - 元素需要拷贝到 hidden array，有开销，且元素不一定 copyable
    - 如果初始值特别大，当前实现下还会导致 stack overflow

**SWAR find any byte from set** http://0x80.pl/notesen/2023-03-06-swar-find-any.html

- SWAR 是把要搜索的字符做 bits 编码然后利用 SIMD 做搜索加速 的技术，技巧性非常强
- 作者这里甚至把 Ada 里用的算法做了进一步的限定，然后能得到一个性能更强的实现

\#2

这周开始当 scheduler 的 release owner，要开始逐渐熟悉并适应 scheduler 的 release 流程

这周 paperwork 部分还比较顺利，周三 Go 的发布也比较 OK，就等下周 PROD 了

另外，之前 OCI 环境的 redis tls issue 应该算是解决了；GOOCI 环境部署之后，没有出现之前的错误，并且业务运行看起来也正常。

该说不说，scheduler 这边让每个成员都参与到 release 工作，并且 dev 环境可以自己构建发布，这个状态对提升团队的 ownership 还是很有帮助的。

\#3

之前刚转到 scheduler 这边的时候 mail 那还留了两张表的迁移工作

随着这周六早上最后一张表的 US01 顺利迁移结束，mail 那边也没有什么遗留工作了，算是了却了一桩心事

以后可以减少和老崔的直接接触了，免得哪天我又忍不住阴阳他几句

\#4

这周给 scheduler 搞上了 santizier，融合进了 vagrant flow，然后发现了三个陈年 memory leak，但是都是固定的泄漏，不会导致 api server OOM

所以 api server OOM 还真的就是需要那么多内存，这感觉还是有点尴尬。

不过之前拿 AI 分析了一边新增逻辑确实没找出潜在泄漏的情况，而这次 leak sanitizer 可以说是直接证实了。

另外发现 sanitizer 最多只能和 `-O1` 一起用，一开始给 ci preset 用的是 Release mode 的 -O3，导致编译链接变得巨慢无比，甚至9-10个小时都编译不完

---

这周就这样，下周见
