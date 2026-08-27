---
title: 一周杂记 in Week 3 Aug 2026
categories: CODE-LIFE
date: 2026-08-24 23:33:01
tags: [杂记]
---
本周（8.17 ~ 8.23）是8月份的第三周，这周是有点忙。

## Life

\#1

周三的时候和几个同事约了参加组织的西湖文化广场的奥德赛观影活动，组织者是前同事 iker（真羡慕他现在的状态啊）；于是周三下班后一行人就做公司楼下的19号线直接四站地铁到了西湖文化广场。

到了之后买了个 McD 就当作晚饭了

电影评价下面再说，就是要吐槽一下西湖文化广场这个浙影的空调是真太不给力了，看的一群人热的不行。

看完后出来先做了地铁回西溪湿地北，然后到西湖文体开上车回家了。

感觉也只有晚上十点多之后隧道是通的，一路火花带闪电，到家就花了8分钟 🤡

\#2

之前买的拳击课这周是最后一节了

现在1对1发现有个好处是可以有比较充裕的时间纠正技术细节，细节可太重要了，不然动作变形了效果也大打折扣。

一个插曲是后面那个团课结束之后还帮教练录了一个新团课的演示视频，其实就是一起上了一个20分钟的试验课程。

所以整个结束都已经快两点了，恰好媳妇儿图书馆学习结束要来银泰给秋宝买东西，所以我就在赛百味一边吃一边等。

一上午这运动刷了800多大卡的热量🤡

\#3

- **奥德赛 The Odyssey (2026) 5/5** 和当年的特洛伊一样都做了去神化处理但是特洛伊过于青春偶像了 诺兰把人性复杂底色处理得好得多 海伦和西农的处理堪称遛狗一手 就差告诉你换只猪上去战争也得打而西农更像奥德修斯人性一面的映射

## Work

\#1

**CppNow 2025 | The Sender/Receiver Framework in C++ - Getting the Lazy Task Done - Dietmar Kühl - C++Now 2025** https://www.youtube.com/watch?v=gAnvppqvJw0&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=31

- 感觉更像是 std::execution 基础 API 走读…

**CppCon 2023 | Lightning Talk: ClangFormat Is Not It - Anastasia Kazakova** https://www.youtube.com/watch?v=NnQraMtpvws&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=56

- 有点想吐槽大会
- clang-format 不是一个 curated tool，只是一个差不多够用的东西，所以开源项目里只有8%的用了，而且 clang-format 对于 token 的解析处理并不严谨

**CppCon 2023 | The Absurdity of Error Handling: Finding a Purpose for Errors in Safety-Critical SYCL - Erik Tomusk** https://www.youtube.com/watch?v=ZUAPHTbxnAc&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=55

- 这个演讲有点意思，不是讲具体如何做 error handling，而是从最开始出发，思考当出现错误时，什么 actions 才是有用的；你处理完这些错误，系统能恢复到正常、一致的状态吗？“*Detecting an error does not imply that recovering from it is meaningful*”

**CppCon 2023 | Advanced SIMD Algorithms in Pictures - Denis Yaroshevskiy** https://www.youtube.com/watch?v=YolkGP-rb3U&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=57

- 简要介绍了一些常见算法如何（概念上）用 simd 可以加速以及效果
- 原理的部分不多，更多是 show benchmark
- 另外 presenter 是 eve library 的作者之一

**C++ Weekly - Ep 546 - Lambda's Members Are WEIRD** https://www.youtube.com/watch?v=x2lmqGMIcok

- 第一个 case 是如果 lambda 包含一个 move-only member，那么手写 callable struct 模拟会很麻烦
- 第二个 case 是 captures 对应到 lambda 内部的布局问题，和 class member 类似，会因为 padding 导致占用不一样；而 lambda explicit capture 规定了顺序，implict captures 顺序和实现有关，不过 Jason 实测下来 gcc trunk 并没有做 layout optimization

**C++ Weekly - Ep 93 - Custom Comparators for Containers** https://www.youtube.com/watch?v=sbiF1HDcG7U

- 如何实现 associative container 的 custom comparator，以 `std::set` 为例

**C++ Weekly - Ep 92 - function-try-blocks** https://www.youtube.com/watch?v=vmtGCJbNKNE

- 对 ctor/dtor 使用 function-try block 不会吃掉这个异常，标准要求要么手动 rethrow，要么编译器帮你 rethrow
- 不过这个也容易理解，不然就造成 loop hole 了
- 不过 function try block 这个特性确实有点，奇葩，虽然我还真用过

**C++ Weekly - Ep 91 - Using Lippincott Functions** https://www.youtube.com/watch?v=-amJL3AyADI

- 一种收敛 exception handling 的 idiom，常用于在 C API 边界转换异常到错误码；或者避免多个有类似异常需要处理的函数每个都写一次异常处理

    ```cpp
    std::error_code translate_exception() noexcept {
        try {
            throw;  // rethrow the currently handled exception
        } catch (const std::invalid_argument&) {
            return make_error_code(std::errc::invalid_argument);
        } catch (const std::bad_alloc&) {
            return make_error_code(std::errc::not_enough_memory);
        } catch (...) {
            return make_error_code(std::errc::io_error);
        }
    }

    std::error_code do_something() {
        try {
            risky_operation();
            return {};
        } catch (...) {
            return translate_exception();
        }
    }
    ```

- 更现代的方式是先转换成 exception_ptr，这样还可以切换线程触发 handler

**C++ Weekly - Ep 90 - Using Codecov and Project Badges** https://www.youtube.com/watch?v=FOcCFGkkQ9c

- 使用 codecov 做覆盖率，以及给 github 项目加覆盖率的 badge

\#2

这周和 OPS 配合，解决了几个小问题之后，终于把挂了个把月的 scheduler 的 clang-tidy corp-jenkins job 搞好了。

这个 job 每次会跑两个多小时对 scheduler repo 做一次完整的 clang-tidy check，后面可以让 oncall 针对完整的结果做 code quality improvement 了

\#3

趁着周末没人，把 scheduler 的编译器更加严格的告警加上了，现在基本可以和 fawkes 自身的告警保持一致了

而且在过程中发现 gcc 的 `-Wshadow` 行为和 clang 的不太一致，而且比较搞笑的是 GCC 默认的 `-Wshadow` 比 clang 的严格多了，但是 GCC 可以通过 `-Wshadow=local` 切换到只检测 local shadow 的行为，避开 class member 和参数或者局部变量重合的情况，这个在写一些 struct 时经常会遇到。

而 Clang 的粒度则没有这个

不过这个编译告警导致的失败是真的修的要死要死，就算 Cursor/Grok 限免跑得过来我也 review 不过来...囧rz

---

这周就这样，下周见
