---
title: 一周杂记 in Week 2 Feb 2026
categories: CODE-LIFE
date: 2026-02-15 23:52:43
tags: [杂记]
---
本周（02/09 ~ 02/15）是2月份第二周，周六已经提前进入春节假期。

## Life

\#1

这周是节前最后一个工作周了，不过官方节假日安排上周六还要调休上班，真的离谱。

今年春节虽说表面上看有9天假期，但是扣掉调休和正常的周末，实际上只多给了一天，也就是只有5天的公共假期...真抠啊

虽然马上要放假了，但是工作强度依然不减，每天看到老崔那个伪君子的脸就想死，而且吃饭的时候聊了一下发现不止我一个人这种感觉

于是索性这周六调休直接开始请假，并且节后的第一个工作日也休假得了，这样加起来11天假期。

所以这周五下班之后就正式进入春节假期了。

\#2

之前按照两位G老师的说法，升级了硬盘固件，调整了 PCIE 的几个设置之后发现果然系统稳定了，再也没有黑屏掉盘死机的问题发生。

于是看 ASUS 又出了个主板 BIOS，索性心一横，直接刷了最新的 BIOS，然后重新打开 DOCP-I 的超频设置，现在内存又从5600回到了6400

不过用了几天后发现除了另外的问题：睡眠后半个多小时后这机器自己就醒来了。

一开始以为误触了键盘鼠标，但是后来发现确实是自己唤醒

重新问了 G 老师，说可以用 admin 权限运行

```powershell
powercfg /lastwake
```

看看是被啥唤醒了

逮到一次机会后发现是两个比较 general 啥 usb root 和 pcie xxx 唤醒的，但是具体是哪个设备也不知道。。

G老师说那既然如此，直接把除了键盘鼠标之外的设备的 wakeup 都给关了

嗯，好像确实可以了 🤔

\#3

这周和老婆因为娃喂奶的事情吵架了，然后我把用了好几年的 logi mx master 2s 给摔坏了...

于是又出血买了一个 mx master 3s...

🤷‍♂️

## Work

\#1

**CppNow 2025 | Advanced Ranges - Writing Modular, Clean, and Efficient Code with Custom Views - Steve Sorkin** https://www.youtube.com/watch?v=5iXUCcFP6H4&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=54

- 如果使用预置的那些 views 会有潜在性能问题或者 filter 会依赖外部状态的时候可以考虑创建自己的 views
- custom views 也有各种坑，实现尽量保持简单，对外部的 ref 尽量 by value，否则 composition 的时候可能会有问题
- Slides: https://github.com/boostcon/cppnow_presentations_2025/blob/main/Presentations/Advanced_Ranges.pdf
- Demo: https://godbolt.org/z/jaEr8Eaaf

**C++ Weekly - Ep 518 - Online C++ Tools You Must See! (2026)** https://www.youtube.com/watch?v=VAgC2bCwOQo

- 整理一下就是
    - [https://compiler-explorer.com](https://compiler-explorer.com/)
    - https://wg21.cmeerw.net/cppdraft/search
    - https://eel.is/c++draft/
    - https://github.com/timsong-cpp/cppwp
    - https://search.cpp-lang.org/#
    - https://wg21.link/
    - https://cppinsights.io/
    - https://github.com/lefticus/cppstdmd/
    - https://cppevo.dev/
    - https://cppstat.dev/
- 一些 proposal/draft 相关的普通人其实也没必要去看，太 lang lawful 了
- [cppstat.dev](http://cppstat.dev) 这个感觉挺不错的，之前在 reddit 上看到就再跟了

**C++ Weekly - Ep 196 - What is `requires requires`?** https://www.youtube.com/watch?v=tc0hVIOJk_U

- 经典的 requires requires
- 不过 Jason 提到了出现这个大多时候 imply code smell，应该单独创建一个 concept

**C++ Weekly - Ep 195 - C++20's `constinit`** https://www.youtube.com/watch?v=o0z3KT4gW7k

- constinit 是强制在编译期初始化某个变量（多为 global duration），但是变量本身不是 const，因而是可以修改的
- 目前的主要用途是解决 global initialization fiasco

**C++ Weekly - Ep 194 - From SFINAE To Concepts With C++20** https://www.youtube.com/watch?v=dR64GQb4AGo

- 介绍 concept，其中 terse syntex 部分讲了好多，并且 terse syntax 更加灵活：
    - 作为函数参数可以支持参数不必都是同一种类型，只要满足 concept 即可
    - 可以作为函数返回类型
    - 甚至可以直接作为变量定义时候的约束
- 不过我觉得如果没有必要，反而应该减少 terse syntax 的使用

**C++ Weekly - Ep 193 - C++20's `contains` Members** https://www.youtube.com/watch?v=4yDRsQyWqqc

- 各容器终于引入了 `contains()` 函数

**Don't Use shared_ptr's Aliasing Constructor** https://ibob.bg/blog/2022/12/28/dont-use-shared_ptr-aliasing-ctor/

- 作者观点是，aliasing constructor 会导致下面这种危险的用法

    ```cpp
    auto name_addr = &alice->name;
    alice.reset();
    std::shared_ptr<std::string> name(alice, name_addr);
    assert(name != nullptr);
    ```

- 虽然 `name.use_count() == 0`，但是自身不是 `nullptr`；并且 check against nullptr 是非常 idiomatic 的方式
- 作者给的方案是不允许直接使用 aliasing ctor

    ```cpp
    template <typename U, typename T>
    std::shared_ptr<T> make_aliased(const std::shared_ptr<U>& owner, T* ptr) {
        if (owner.use_count() == 0) return {};
        return std::shared_ptr<T>(owner, ptr);
    }
    ```

- 这个方案确实可以在一定程度上有帮助，但是其实更大的问题是 aliasing 的误用。绝大多数情况下 `ptr` 指向的应该是 `owner` 的一个 field；而 aliasing constructed after reset 我觉得是更严重的逻辑问题，不如在 `make_aliased()` 里先加一个 assert

**Recognizing stop_token as a General-Purpose Signaling Mechanism** https://www.vinniefalco.com/p/recognizing-stop_token-as-a-general

- C++ 20 引入的 stop_source / stop_token 可以看作 one-shot & one-to-many 的 signaling event mechanism；加上搭配的 stop_callback 可以做一些 event ready operations
- 作者在这个基础上想搞一个支持 reset 的更通用的，并且提了 proposal…

\#2

这周有不少时间在研究如何给 fawkes 做 graceful shutdown，总结起来需要的理论步骤如下：

1. 监听 signals，例如 `SIGINT`/`SIGTERM`
2. signal 触发后首先“停”掉 listener/acceptor，这一步是避免后续再有新的请求进来；这一步之后只需要处理存量 http 连接即可
3. 对于处在 idle 的连接可以直接 shutdown；而已经在 serve 中的连接要在发完 response 之后当作 keep-alive == false 的情况处理，关掉连接
4. 然后等到 executors 没有 outstanding work 自然退出即可

对于 ASIO 来说，因为 proactor 模型，所以需要一个机制取消 outstanding async operations；第二步里直接 close tcp::acceptor 就行，而 idle 连接的 async_read 是不能用直接 cancel 或者 close 整个 socket 的方式来取消的，因为这会导致 serving 连接的操作也直接被强制中断。

所以对于 ASIO 来说就要用到 cancellation_signal/cancellation_slot 这一对用来支持 per-operation cancelaltion 的机制

因为 http 各连接之间不需要互相通讯（和 chat 这类明显不同）所以我非常非常不情愿实现一个 session manager 类似的东西，因为用这个一定得引入同步机制，这月等于给自己上了一个枷锁。

所以这周很大一部分时间是在研究如何不改动当前各 http connection 独立的情况下加 cancellation 支持。

好消息是已经有个能 work 的 demo，剧透一下核心利用的 std::stop_source/stop_token 搭配 cancellation_signal/cancellation_slot

具体后面专门出几期的 posts 来展开讲一下。这部分内容比较多，而且涉及多个组件，所以估计得单开一个系列。

\#3

其实 cancellation 也是 structural concurrency 的一部分，所以其实也顺带研究了一下 ASIO 为 `awaitable<>` 提供的 parallel group 的语法糖，即

- `co_await (op1 && op2 && ...)` 本质是 when_all，会等待所有 op 结束或者其中某一个以异常方式失败，则其他的操作直接被取消
- `co_await (op1 || op2 || ...)` 本质是 when_any，只要其中一个成功，其他的操作被取消

这套语法糖对应的是 parallel group 和 when_op，并且深度利用了 per-operation cancellation

这部分内容也会在上面那个系列里讲到。

感觉这个春节假期的任务比较重？ 🤔

---

好了这周就这样，下周见。
