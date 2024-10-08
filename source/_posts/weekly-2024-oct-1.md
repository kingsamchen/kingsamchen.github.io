---
title: 一周杂记 in Week 1 Oct 2024
categories: CODE-LIFE
date: 2024-10-08 23:17:17
tags: [杂记]
---
本周（09/30 ~ 10/07）是十月份的第一周，十一假期休息周。

## Life

\#1

9/30 在家办公了一天之后正式进入十一假期。

因为媳妇儿10/1需要值班，并且担心手头的病人会出现紧急情况，所以这次买了3号早上回老家的票，返程也定在了6号傍晚。

3号7点醒来就开始收拾，7点半左右就一起出门了。不过这次运气比较好，两班地铁都没久等，基本是前脚刚到后脚就上地铁。

到了车站之后发现时间还早，就选了老娘舅吃了个早饭。因为早饭提供的东西有限并且感觉都不是很好吃，所以吃的差不多就下楼了。

候车厅1F转了一会儿终于开始检票就上车了。因为起的有点早，所以车上两人都睡了好久。并且为了避免出现之前那种车上和别人出现争执的情况，我几乎全程带着Bose...

在老家几天没有怎么出门，主要是有点懒，而且媳妇儿孕早期精神状态一般。

照例看望了一下爷爷和外婆，简单的聊了一下近期的情况。

给家里买了一个石头T20Pro扫地机器人，自带水箱可自动洗涤拖布，感觉比我在21年迈的T7S要进步了不少。

给妈妈手机关联并且配置好之后，每次全屋尤其客厅的清洁都可以让扫地机器人代劳了。

这周媳妇儿算是孕期头一个半月了，虽然目前没有孕吐但是胃口明显变差，不太清淡的食物基本都有比较大排斥感。另外一个比较蛋疼的事出现了尿频，而且凌晨三四点的时候是高发期，这几天这个点开始就要来来回回上厕所...

媳妇儿解释说是因为子宫变大挤压膀胱导致尿感，只能期望过一段时间之后会症状自然消解...

同时因为目前是早期，很多检查没有做，尤其基因病筛查还没到时间，所以还没告诉家里人，免得现在太高兴立了一个 Flag 后面因为不可抗力要流掉...

6号晚上7点多出高铁站的时候感觉好冷啊，似乎杭州是这几天瞬间变冷的。回到家之后第一件事就是把凉席撤了换了床单和比较厚的被子 😅

\#2

假期看了两部电影

- **异形3 Alien³** 4/5 对白叙事部分有明显的大卫芬奇的风格，整体又回到了幽闭恐惧上，卡梅隆在2中风格大改很难说对后续剧集是好还是坏。老雷第一部的时候没有夹杂的宗教私货反倒在这部让芬奇给带上了；之前吐槽上一部有终结者2的风格结果这部的结尾直接搞了别无二致的处理... 不过这剧本一上来就把上一部拼死救回的人搞死这样真的好嘛....还有CG异形有点让人出戏
- **走走停停** 4/5 川渝版本的爱情神话，能和自己和解按照自己的意愿生活，废柴又如何呢。最后结尾堵车那段戏简直神来之笔；吴迪想约冯柳柳出去最后放弃了，剧中剧里最后约吴妈出去也是没有回应，生活那这么多如愿的转折。 

## Work

\#1

**C++ Templates 2nd | 15. Template Argument Deduction**

- 各种模板参数的 deduction cases…
- 引用折叠和完美转发，这部分应该是广大基础了
- SFINAE 必须作用在 immediate context 下，如果触发SFINAE需要完整实例化 function template body 而这部分操作有错误的话就不会触发 SFINAE 而是一个 hard error
- CTAD & auto 还有各种 deduced return type
- decltype & decltype(auto) 的各种推导 rules 和 tricks，尼玛也太细了
- structural binding 的推到规则
- CTAD，各种 subtleties 也太多了，感觉实际中还是最好约束一下，免得掉坑

**CppCon 2022 | Value Semantics: Safety, Independence, Projection, & Future of Programming - Dave Abrahams** https://www.youtube.com/watch?v=QthAU-t3PQ4&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=41&t=1s

- 这个 Talk 比较务虚，主要还是概念和方法论上使用 value semantics 的好处

**CppCon 2022 | A Lock-Free Atomic Shared Pointer in Modern Cpp - Timur Doumler** https://www.youtube.com/watch?v=gTpubZ8N0no&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=43

- C++ 11 引入的 atomic operation overloads for std::shared_ptr 有使用便利性问题和性能问题，实现通常需要用一个全局表来维护 shared_ptr 关联的 mutex，见 https://en.cppreference.com/w/cpp/memory/shared_ptr/atomic
- C++ 20 的方案是对 std::atomic<T> 做 std::shared_ptr 的特化，这样接口上和寻常的 atomic 数据类型是一致的；不过各家标准库实现上确实都不是 lock-free 的 😂，libc++ 甚至至今还未提供这个 feature…
- 有一些 3rd party 的 lock-free shared_ptr 实现，比如 folly 就有，而且应该是目前唯一一个跑在实际生产环境的实现
- 当前 modern cpu 都不支持 double CAS operations，所以这个是实现上的难点，一种可行方案是转换成 double-width CAS，这个操作现在新一些的主流CPU都支持；但是考虑到 shared_ptr 内部实现布局，需要很 tricky 的操作，talk 里提到的 folly 做法是 split refcount
- 还有另外一个方案是外部存放的内部数据的index，这样可以适配标准库对于 shared_ptr 的要求，比如 weak_count，aliasing ctor .etc 不过这个方案不可避免需要做内存分配，所以也有问题
- 最后因为 ABI-compatible 的原因，lock-free atomic shared_ptr 是不会出现标准库里的 😂
- 作者说自己在搓一个即可以跨平台又可以选择底层策略的实现…good luck 把

**CppCon 2022 | Using C++14 in an Embedded “SuperLoop” Firmware - Erik Rainey** https://www.youtube.com/watch?v=QwDUJYLCpxo&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=44

- 作者工作中遇到的 Amazon 运货无人机的控制模块的固件的开发环境的限制，比如不能有 heap, no blocking .etc
- C++ 新特性的一些优势。听起来 compile-time 类型的优势明显，而 STL 中只有不依赖 dynamic allocation 的容器，比如 std::variaint, std::optional 这些使用限制较少（虽然是 C++ 17)
- 另外嵌入式环境的 toolchain 更新比较慢，MISRA 和 Autosar 这两家应该是核心标准玩家

**CppCon 2022 | LLVM Optimization Remarks - Ofek Shilon** https://www.youtube.com/watch?v=qmEsx4MbKoc&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=45

- LLVM 现在会提供一些优化相关的反馈信息，包括某些优化为什么无法被运用 .etc
- 顺带讲了一些 edge cases 组织优化的案例，收获还是不少的
- 案例里面主要都是 aliasing 或者类似没法保证某个数据不会被修改而放弃优化的情况

**CppCon 2022 | C++ Coroutines, from Scratch - Phil Nash** https://www.youtube.com/watch?v=EGqz7vmoKco&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=47

- 通过改造一段代码来阐释 coroutine frame / coroutine handle / promise 的逻辑和关系
- 但是前摇也太长了，讲了快30分钟才开始到主题…

**CppNow 2024 | Keynote: Employing Senders & Receivers to Tame Concurrency in C++ Embedded Systems - Michael Caisse** https://www.youtube.com/watch?v=wHmvszK8WCE&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=11

- 介绍了一下嵌入式开发的一些基础背景，然后才是 S&R 模型可以在这个领域有那些用途
- S&R model 我觉的优势在于每个组件可以和乐高一样拼凑复用，灵活性很大，但是可读性/易于理解性我总觉得很容易就被破坏，而且查错调试的成本可能会明显增加

**C++ Weekly - Ep 447 - What Are Reference Qualified Members?** https://www.youtube.com/watch?v=5ELoDcqqyX4

- 介绍了成员函数可以指定 & && 这种 qualifier 的科普

**C++ Weekly - Ep 448 - C++23's forward_like** https://www.youtube.com/watch?v=AFcfRf5KWe0

- 介绍 forward_like 的使用，这个主要是和 deducing this 搭配使用
- 概括一下就是如下代码

    ```cpp
    class Widget {
    private:
        int _value;

    public:
        Widget(int value) : _value(value) {}

        // Getter using deducing 'this' and std::forward_like
        auto get_value(this auto&& self) {
            // Forward _value in qualifier as in sef
            return std::forward_like<decltype(self)>(self._value);
        }
    };
    ```


**C++20: Building a Thread-Pool With Coroutines** https://blog.eiler.eu/posts/20210512/

- 非常棒的一个 coroutine 实践入门的文章，这回是真刀真枪的写 demo 而不是那种浅尝辄止停留在最表面的入门文章
- 跟写过程中对 promise / awaiter 这些有了更深的理解，强烈推荐

**C++20 concepts are structural: What, why, and how to change it?** https://www.foonathan.net/2021/07/concepts-structural-nominal/

- C++ concepts 选择了 structural typing 而不是 nominal typing，作者认为前者比起后者有一些明显的缺点，例如做不到 sementics difference (i.e. *differentiating between concepts with identical interface but different semantics*) 因为这些不是 structural 的；对于 naming 过于要求
- 但是对 C++ concepts 来说 structural typing 是一个务实选择，因为它能很好的兼容老代码
- 如果需要在 C++ 中模拟 nominal typing，作者比较推荐的方案是 inherit base tag class
- 这篇内容还是不错的

**初探 C++20 协程** https://sf-zhou.github.io/coroutine/cpp_20_coroutines.html

**再探 C++20 协程** https://sf-zhou.github.io/coroutine/cpp_20_explore_coroutines.html

- 这两篇讲的都比较零散，广度深度也都一般，只能算作作者自己的 notes
- 可看可不看

**C++20 Coroutines** https://blog.feabhas.com/2021/09/c20-coroutines/

- 也是一篇比较早期的入门型文章，用 coroutine 实现一个 Future<T> 但是有很多地方的起步没讲清楚

\#2

假期期间抽了个时间把 vagrant vm 里的 clang 升级到了 v19，然后又修了一遍 esl 的 tidy issues

看用的 cpm.cmake 版本也很老了，就顺手也给他做了一个 upgrade

https://github.com/kingsamchen/esl/pull/18

\#3

上面有篇基于 coroutine 的 thread-pool 的post，通过跟着这篇 post 一起上手把相关逻辑写了一遍，对 coroutine 的理解有明显的加深

我在写的过程中特意加了一些注释来解释一些初看不是很直观的地方，MR 可以见 https://github.com/kingsamchen/Eureka/pull/27

---

好了这周就这样，下周见
