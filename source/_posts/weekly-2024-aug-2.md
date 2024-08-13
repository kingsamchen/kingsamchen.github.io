---
title: 一周杂记 in Week 2 Aug 2024
categories: CODE-LIFE
date: 2024-08-12 22:39:21
tags: [杂记]
---
本周（08/05 ~ 08/11）是8月份的第二周。

## Life

\#1

这周几乎整周都是高温，所以只有周二和周四去了办公室，而且还是因为周二要体检。

有时候在家 wfh 久了也觉得挺无聊的，所以去办公室除了蹭一下办公室的空调之外还能和同事面对面的扯个淡。

\#2

之前约了周二早上体检，所以一大早8点就到了同德。

因为来得比较早，所以前面的大部分项目都检查的比较顺利，直到最后一个超声排到我之后等了大概足足半小时，因为前面一个女的检查实在太慢了。

以至于我中间抱怨了一次问了医护，医护说女同志检查超声是会慢一些，我当时都想直接让他们人肉做一次 reblance 把我放到其他超声室去排队了...

检查完之后和 Roro 一起吃了早饭，同德搞得西兰花意外的好吃...

等到周末之后体检报告也出了，这次大部分之前有问题的数据都有了明显的好转，低密度蛋白和高密度蛋白全都回到了正常范围，除了甘油三酯还是有点爆表。

尿酸也只比正常范围多了几十，和老K差不多的水平，考虑到我的运动健身强度和蛋白质摄入，我觉得这个算是正常的水平

至于甘油三酯，我总觉得是因为体检前一天晚上和 iker 吃了啫啫煲导致的...

不过不管怎么样，说明我之前的生活运动带来的改变非常明显，所以后续也要一直继续这个状态

\#3

本周观影

- **异人之下 3/5** 纯路人观感，没那么出众但也没有那么难看。叙事节奏问题比较大，前半部分节奏比较正常，交代人物也比较利落，但是后半部分明显过于拖沓。主角塑造有点聊胜于无，冯绍峰是没有演上繁花不爽吗还是啥，想的是体面上海宁，结果演的这么油腻。。。
- **Days of Thunder 3/5** 中规中矩，没有太大的亮点，最后的比赛的戏份还可以。当年的阿汤哥是真的嫩啊，就是太矮了，跟妮可基德曼站一起更明显了
- **The Hunt 4/5** 结局打戏扣一星，能把一个这么壮国民警卫队的人几下撂倒结果开放空间打一个只练了8个月而且强度存疑的这么费力....政治隐喻还可以，左党嘲讽拉爆

## Work

\#1

**CppCon 2022 | The Most Important Optimizations to Apply in Your C++ Programs - Jan Bielak** https://www.youtube.com/watch?v=qCjEN5XRzHc&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=9

- 演讲者还在上高中，真是青年俊才
- 核心内容就是各种 performance tips

**CppCon 2022 | C++ Lambda Idioms - Timur Doumler** https://www.youtube.com/watch?v=xBAduq0RGes&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=10

- Lambda 的各种奇技淫巧，非常不错的一个 talk
- templated lambda 有点骚气

    ```cpp
    template<typename T>
    const auto num_cvt = [](auto v) {
        return static_cast<T>(v);
    };
    ```

- C++ 20 开始 lambda 可以用在 unevaluated context 下，配合 one lambda expression, one closure type 可以实现 unique type generator

    ```cpp
    template<auto = []{}>
    X struct {};

    X x1;
    X x2; // x1 and x2 are different types
    ```

- Immediately Invoked Lambda Function 的 trick cppcon 2021 有个 talk 已经提到了，非常实用

**CppCon 2022 | C++ Coding with Neovim - Prateek Raman** https://www.youtube.com/watch?v=nzRnWUjGJl8&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=11

- neovim 的 C++ 环境设置以及一些使用 neovim 的技巧分享
- 我自己还是用 vscode 吧，也没差，工具党可以冲冲

**CppCon 2022 | C++20’s Coroutines for Beginners - Andreas Fertig** https://www.youtube.com/watch?v=8sEe-4tig_A&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=13

- 讲了一个简单版本的 generator 和 multi-task scheduling
- 体感一般，觉得不如 github 找个 repo 研究学习一下

**CppCon 2022 | Back to Basics: C++ Smart Pointers - David Olsen** https://www.youtube.com/watch?v=YokY6HzLkXs&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=12

- 基础内容，感觉没啥特别的…

**C++ coroutines: The problem of the DispatcherQueue task that runs too soon, part 4** https://devblogs.microsoft.com/oldnewthing/20191226-00/?p=103268

- 系列第14篇
- 这里解决了上篇中提到的因为同步引入的 memory barriers，最终发现甚至都不需要同步，直接只让 lambda 自己去更新 `m_queued`

**Creating a co_await awaitable signal that can be awaited multiple times, part 1** https://devblogs.microsoft.com/oldnewthing/20210301-00/?p=104914

- 系列第15篇
- 开始进入实现一个可以被多次 co_await 的 awaitable_event

**Creating a co_await awaitable signal that can be awaited multiple times, part 2**

https://devblogs.microsoft.com/oldnewthing/20210302-00/?p=104918

- 系列第16篇
- 这篇讨论的是如何解决 awaitable event 可以被 copy，方案有两个，一个是利用 Windows 的 DuplicateHandle 来增加 Event 内核对象的计数，另外一个方案是内部用 shared_ptr 包一下 Event 内核对象，这样可以做到相同的效果并且引用计数的变更更加轻量

**Creating a co_await awaitable signal that can be awaited multiple times, part 3** https://devblogs.microsoft.com/oldnewthing/20210303-00/?p=104922

- 系列第17篇
- 换成了前面用户态的 slim_event + atomic flag，并且用一个队列(vector actually)来保存 suspended coroutine handles，resume 就是上个锁然后做一个 swap trick 然后逐个 handle resume

**Creating a co_await awaitable signal that can be awaited multiple times, part 4** https://devblogs.microsoft.com/oldnewthing/20210304-00/?p=104926

- 系列第18篇
- 用一个存在 awaiter 里的 node 来串联形成链表来解决 vector 使用内存分配导致的不稳定

**How strands work and why you should use them** https://www.crazygaze.com/blog/2016/03/17/how-strands-work-and-why-you-should-use-them

- 提了一下使用 strand 可以不避免 explicit synchronization 的优点
- 然后我以为是讲 asio/strand 的架构结果是他自己手搓的A货…那就没啥好看的了

**C++ Weekly - Ep 270 - Break ABI to Save C++** https://www.youtube.com/watch?v=By7b19YIv8Q

- 讨论了为什么应该 break C++ 标准库的 ABI
- 最后提供了一些 tips 对于那些实在需要一些 old binary lib 的 workaround

**"Should we break the ABI" is the wrong question** https://nibblestew.blogspot.com/2021/05/should-we-break-abi-is-wrong-question.html

- 要不要 break abi 的本质其实是有没有方法可以平稳过渡
- 文章中提到的一个主要方法是 debian 系列用 ELF 中的某个 version field 来做 multi-arch 特性，可以用这个路子来做 multi-abi

**A Default Value to Dereference Null Pointers** https://www.fluentcpp.com/2021/05/14/a-default-value-to-dereference-null-pointers/

- 文章核心就是给指针加上类似 `std::optional::value_or()` 的 `value_or`…

**Detecting memory management bugs with GCC 11, Part 1: Understanding dynamic allocation** https://developers.redhat.com/blog/2021/04/30/detecting-memory-management-bugs-with-gcc-11-part-1-understanding-dynamic-allocation

- part 1 讲的主要是 gcc 利用 attribute malloc 来做到动态分配类型创建/释放调用不匹配时可以触发编译告警
- 例如 用 free() 释放 fopen() 的指针就能触发告警
- 评价是对于 C++ 来说意义不大…

**Detecting memory management bugs with GCC 11, Part 2: Deallocation functions** https://developers.redhat.com/blog/2021/05/05/detecting-memory-management-bugs-with-gcc-11-part-2-deallocation-functions#

- part 2 主要讲的是 operator new/delete 的配对使用问题，以及对 non-heap 对象进行释放的问题
- 目前 false positive 和 false negative 都会存在，预期未来在这块会有一些逐步改进

\#2

这周比较偷懒， C++ Template 一章都没看完，线性代数也没看 🤣

下周继续努力把

---

好了这周就这样，下周见
