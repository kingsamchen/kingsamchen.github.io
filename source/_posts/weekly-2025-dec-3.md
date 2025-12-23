---
title: 一周杂记 in Week 3 Dec 2025
categories: CODE-LIFE
date: 2025-12-22 23:22:51
tags: [杂记]
---
这周（12/15 ~ 12/21）是12月第三周，下周就是圣诞了，虽然中国不放假但是美国放假啊。

## Life

\#1

这周不知道是什么原因，我老婆说我太焦虑了，但是我总觉得是感染了甲流病毒但是是无症状，导致睡眠的 HRV 奇低，而平时的心率很容易升高，静息心率也从平时的48一路升到了58.

这导致虽然每天睡了好久但是其实都没有怎么恢复，精力还是很差。

而且稍微一个强度的运动就会把心率打高，有氧跑步也明显有水平上的退化。

不过好消息是周五 WFH 开始，HRV 和 RHR 都开始明显改善。

希望下周能恢复正常，毕竟这周都没怎么正经运动，下周想上下拳击训练营。

\#2

这周日是冬至，和秋宝的第一个冬至。

晚上跑完步回来吃了个大闸蟹+汤圆的晚宴，然后洗个热水澡，和秋宝一起耍耍。

我妈回老家后也有半个月没看到秋宝了，开了个视频通话给她和秋宝闲扯几句。

下周一秋宝就正式7个月了，她这周已经开始讲 mama-mama 了，感觉再过一个月就能说些更复杂的表达了

\#3

这周喂猫终于遇到了 乐乐 和 皮蛋，尤其是皮蛋，给我吓死了，以为他也和小蛋一样遭遇不测。

万幸这俩都挺健康吃的还不错，乐乐应该是一直在小区内有人投喂，皮蛋估计是经常会半夜来吃饭。

周日晚上也给他们开了好多猫粮和罐头，让他们冬至也吃个饱饭。

\#4

这周电影俱乐部看了火车梦，给我看差点破防（正向评价）

- **火车梦 Train Dreams** 4/5 一个被上帝将就的人，经历着世间的变革，又失去了妻女变成了孤身一人，带着对女儿幸存唯一执念，最终跟随着时间走完了自己的一生。后劲比活着更大。

## Work

\#1

**CppCon 2023 | File I/O for Game Developers: Past, Present, and Future with C++ - Guy Davidson** https://www.youtube.com/watch?v=1CdduHa-KgA&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=19

- 感觉上了一节历史课…
- 没有太多代码层面的东西，主要还是那种历史发展的回顾

**CppNow 2025 | Extending std::execution - Implementing Custom Algorithms with Senders & Receivers** https://www.youtube.com/watch?v=zcNip8ydpmM&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=43

- 完全不知道这哥们在说啥。。。听了一半弃了
- 看评论不止我一个人这么认为

**CppCon 2023 | C++ Modules: Getting Started Today - Andreas Weis** https://www.youtube.com/watch?v=_x9K9_q2ZXE&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=16

- 从这个演讲上来推算，现在编译器和构建系统对modules的支持都已经算是可以上 production-ready 了，应该只需要一些 corner cases 的处理需要调整
- 不过三方库的演进应该会是一个比较久的过程
- 另外演讲最后1/3提了很多现有项目如何演进 modules 的 tips

**Ep 511 – C++ move(obj).fun() vs move(obj.fun()): The Reference-Qualifier Gotcha** https://www.youtube.com/watch?v=nLjrMcjsa0Y

- std::optional::value() 区分了 & / &&  qualifierhttps://en.cppreference.com/w/cpp/utility/optional/value.html
- 这个评论 https://github.com/lefticus/cpp_weekly/issues/489#issuecomment-3422282035 说的很到位，前者语义明确一些，而且调用之后不在乎 obj；后者调用之后 obj 自身状态很容易违反 invariant

**C++ Weekly - Ep 510 - The AMAZING Performance of array (and span)!** https://www.youtube.com/watch?v=u0mVnuUh46w

- std::array 带编译期的长度信息，可以帮助编译器做优化；std::span 类似
- 核心点就是，编译期的信息越多，编译器能做的优化也越多 🤔

**C++ Weekly - Ep 223 - Know Your Standard Library: std::nextafter** https://www.youtube.com/watch?v=-F0j2VN4xEU

- 介绍的浮点数相关的一个标准库 util

**C++ Weekly - Ep 222 - 3.5x Faster Standard Containers With PMR!** https://www.youtube.com/watch?v=q6A7cKFXjY0

- 应该是 jason 第一篇讲 pmr 的，没有任何优化的简单使用平均有 3.5x 的提升，主要来自 stack alloaction 的优化
- 讲道理 pmr 这套把之前的 stack allocator 直接给秒了

**C++ Weekly - Ep 221 - Generating a .clang-format For C++ Doom** https://www.youtube.com/watch?v=FNLWtKxXQ0o

- 直接从现有的代码风格生成一个 .clang-format，但是看起来不是很好用，而且时间也太久了
- 不过我倒是挺想要一个能从现成 cmake 文件产生 cmake-format 的方法

**基于 asio 异步机制实现目录监控** https://www.jackarain.org/2023/06/11/dirmon-base-asio.html

- 核心其实就是这个函数 https://github.com/Jackarain/autogit/blob/master/autogit/include/watchman/linux/linux_watchman.hpp#L88
- 本质上还是前面提到的 asio async_initiate 的使用

**关于在 asio 使用 transfer_at_least 数据传输时性能优化** https://www.jackarain.org/2023/06/13/asio-transfer_at_least-performance.html

- asio 自带的 `transfer_all()` / `transfer_at_least()` 内部的 buffer 大小是 64-KB
- 演示了自定义一个 completion_condition，核心是签名符合就行；返回的结果就是后需要 prepare 的buffer 大小
- 但是问题是超过系统缓冲区的buffer有必要吗？

**关于在 asio 使用 tcp::acceptor 时性能优化** https://www.jackarain.org/2023/06/14/asio-acceptor-performance.html

- 对这篇其实存疑，问了 GPT，答案和我的想法比较接近
- 双 accept 如果有用，很大程度上是 accept 调用前/后干的事情太多，以及没有充分隔离 acceptor 使用的 executor

**c++ 中 if constexpr 的 always_false** https://www.jackarain.org/2023/09/22/static_assert.html

- 这篇有点意思，目标是

    ```cpp
    template <typename T>
    void foo() {
        if constexpr (std::is_same_v<T, int>) {
            //...
        } else if constexpr (std::is_same_v<T, float>) {
            // ...
        } else {
           static_assert(...);  // --> force compiler to fail
        }
    }
    ```

    让 eles 这部分被选取的时候触发编译错误

- 个人意见是如果有需要在函数内部定义那个 always_fail 就好了。。可读性好得多

**关于 c++ asio 性能小记** https://www.jackarain.org/2023/10/14/asio-performance.html

- 有些 tips 还行

**Sender/Receiver 模式的设计缺陷** https://www.jackarain.org/2025/07/17/sender_is_bad.html

- 核心就是说 S&R 在 partial success 方面提供的机制并不友好，而网络编程经常要面对 partial success，比如对面数据发了一半就直接断开链接了
- 只能说先围观，反正我也不懂 S&R，等29没准有基于 S&R 的网络库呢

**论 c++ 中 error_code 的设计** https://www.jackarain.org/2025/08/20/error_code_design.html

- 评价是不如看我的 https://kingsamchen.github.io/2024/02/25/dive-into-error-code-error-condition-and-error-category-in-cxx/

**使用 boost::url 定制 URL 解析器** https://www.jackarain.org/2025/11/24/boost_url_eazy.html

- 巧了，我在写 fawkes 的时候也大量用了 boost/urls，这个库确实很牛逼
- 核心
    1. 定义一个 foo_bar 作为 parse 产物
    2. 定义一个 foo_bar_rule_t 作为 parsing rule，并且内部定义 value_type 为 foo_bar
    3. 为 foo_bar_rule_t 实现一个 parse 接口

        ```cpp
        boost::system::result<value_type> parse(char const*& it, char const* const end) const noexcept;
        ```

    4. 使用者通过调用

        ```cpp
        boost::urls::grammar::parse(string_view_content, foo_bar_rule_t{});
        ```

        来完成解析

\#2

这周抽了点时间把 io-thread-pool 加好了，花的时间比预期少 https://github.com/kingsamchen/fawkes/pull/17

因为一开始就是打算把 io thread pool 做成可选的模式，所以接口设计直接“借鉴”了 `asio::thread_pool` 但是实现过程中发现很难做到 `thread_pool::join()` 的语义；因为不依靠侵入到 exeuction_context 中没法准确获得 outstanding tasks 的数量，这样也就做不到只有当没有 pending requests 时才去 reset work guard

否则就会出现 pool 中有的 thread 因为没有 task 并且 guard 也 reset 了直接退出了，而有的 thread 还在跑。

严格来说来说有个 workaround：比如按顺序（顺序逆序本质没区别）先 reset work guard 然后 join 这个 thread，这个 thread 退出之后再处理下一个，本身调用 join 会直接强制 wait 整个 pool 结束，并且 `asio::thread_pool::join()` 也可能出现 infinite wait 的情况

不过考虑到这个可能和后面的 graceful shutdown 有关，所以这里先留了个口子，等到后面做的时候再想想

\#3

顺带抽了点时间优化了一下 CMake：

- 引入了 `fawkes_source_folder()` 简化了 source group 使用
- 几个漏了 fawkes_ 前缀的自定义函数把前缀加回来了，避免冲突

PR 见 https://github.com/kingsamchen/fawkes/pull/18

---