---
title: 一周杂记 in Week 4 Oct 2026
categories: CODE-LIFE
date: 2026-10-26 22:39:19
tags: [杂记]
---
本周（10.19 ~ 10.26）是10月份的第四周。10 月份过的还是挺快的哦

## Life

\#1

这周大部分时间都比较平淡，除了周五。

周五下午4点的时候参加公司的 zoomtopia 暨游西溪湿地答题活动，虽然说是公司相关的知识答题，但是组里有 keqiang 这种学霸在，我只需要作为气氛组负责徒步就行了。

不过比较搞笑的是，几个组答题上全都是满分，最后通过每个组派出的志愿者数量的加分来决定名次了。

虽然我们和另外两个队伍并列三等奖（其实是并列最后一名，因为0志愿者），但是还是每个人能分到66块钱的奖金 😅

所以也算还行吧

周五晚上还和 Hogan 一起约了包子在铂澜大象城的团课。

本来我只打算上一个核心就回去的，后来想来都来了，干脆划水上个 BC 得了，虽然第二天还有包子的拳击训练营 🤔

不过虽然 BC 比较划水，但是其实消耗也还是不小的。

送完 Hogan 回到家都快九点八了，才开始啃我的 McD 🤡

更不好的消息是确实影响了当晚的 HRV，不过周六上午后来又补觉了1个多小时，所以整体也还算可以

\#2

周六城西银泰搞周年庆，结果把交通搞烂了，交警直接把两个银泰的入口都封了，只出不进。

这个本来没啥，但是特么我周六下午有拳击训练营，而且因为下午还下着小雨，所以我想着开车去因太好了，虽然只有几百米；结果就这几百米，我开了40分钟，因为两个路口都被封了，路上都是车，等我知道只出不进的时候我起的都想献忠。。。

最后直接把车开回家换了电驴；不过到操房的时候已经是16:40了，直接迟了40分钟 😅

不过还好这节课练得还不错，强度也拉爆了，算没白上。

\#3

这周开始打了鸭科夫的海岛特辑，感觉还行。

不过缺点是打通几遍之后就没什么感觉了，和 hades 这种我玩了320多个小时的 rouge 还是有一些差距

\#4

这周依旧没看电影，连 POI 解说都没继续了，因为最后几集每一集都是刀子。。。想着先等等。。。

另外这周 HRV 炸的厉害，感觉得先放松放松。。。

## Work

\#1

**CppCon 2023 | Safety and Security for C++: Panel Discussion - Hosted by Michael Wong** https://www.youtube.com/watch?v=R10pXWHpPn4&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=74

- 这个算是比较“沉重”的话题，也是近N年来面临的最直接的问题
- 首先定调，safety 不止包含 memory safety，还有其他的很多的范畴，只解决 memory safety 不会必然地让 C++ 程序没有安全问题
- unsafe 是 C++ 非常重要的一个组成，但是我们可以通过 modern features 把他们单独隔离开
- safety critical 其实是一个需要整体 certified 地过程；tooling 和 guidelines 也是必不可少地
- absolute safety 不实际/不靠谱，也不经济；就算没有 memory safety issues，各种逻辑漏洞也会导致 CVE

**CppCon 2023 | C++20 Modules: The Packaging and Binary Redistribution Story - Luis Caro Campos** https://www.youtube.com/watch?v=-p9lvvV8F-w&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=75

- 只能说（演讲的时候）modules 的路还有很长，这个视频主要是作为包管理生产方的视角
- 首先不同编译器不兼容，甚至如果你的 pacakge 想带上 bmi 的这种二进制信息，编译器版本还要一致，而且用 clang 的话编译库的 flags 和 consumer 的 flags 还要一致…
- 然后就是各种实际体验的小问题
- 感觉先解决包规范之后 modules 反而容易解决？

**CppNow 2025 | How the Linux User/Kernel ABI Really Works - Greg Law** https://www.youtube.com/watch?v=5UkVl0xK-3E&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=22

- 这块有点零散，没有明确主线，所以基本是一块块的
    1. glibc 是 syscall 的 wrapper，大多数时候用的也是 glibc；现代架构上系统调用有 `syscall` 或者 `svc` 这个专用指令，早前是 `sysenter` 或者更素朴的 `int $0x80`

        c calling convention 和 syscall calling convention 对寄存器的使用方式不一样

    2. Linux 上会提供 vDSO(virtual Dynamic Shared Object) 由 kernel 直接注入进程地址空间，时间相关的调用 gettimeofday / clock_gettime 不需要再发出真正的系统调用，不需要 user-mode → kernel mode
    3. syscall 失败会使用负数表示具体错误，libc wrapper 处理的时候返回 -1 然后把具体错误值取个反，存进 `errno` 即：内核如果返回 `-EACCES` 那么最终 libc wrapper 会设置 `errno == EACCES`

        errno 是 TLS 应该大家也都知道了

    4. linux 进程/线程本质都是 task ，区别在于 cloen 的范围这个应该都知道了；pthread mutex 其实就是 futex syscall + userspace atomic

        另外这里有个 fun fact: glibc 2.25 之前 getpid() 调用会被 cache

    5. signal handling 是再 user space 被调用的，signal frame 是内核专门构建的；执行 signal handling 需要的上下文切换信息会存在 ucontext_t 里
    6. syscall 被打断以及自动 restart 这个应该看过现代点的书/manual 的也都知道了，不过 restart 这个是通过 rewind instructio pointer 来重新执行
    7. ptrace 这个东西又大又复杂（可以用来写调试器）
    8. /proc 暴露了一堆信息，可以直接读取，这个应该也都是常识了；不过这里有个 auxv / `getauxval()` 我也是第一次知道
    9. fd 是 index，ref 的是 file description，保存 offest .etc

**C++ Weekly - Ep 555 - C++26's observable_checkpoint** https://www.youtube.com/watch?v=xDZLopV4NyI

- 这个东西有点意思，一句话概括就是：这个调用之后的UB不能对调用前的逻辑产生影响

    ```cpp
    volatile int error_flag = 0;

    void process(int* p) {
        if (p == nullptr) {
            error_flag = 1;
        }

        ++*p;
    }
    ```

    调用 `process(nullptr)` 会触发 UB，所以编译器不保证 `error_flag = 1` 会被执行

    ```cpp
    #include <utility>

    volatile int error_flag = 0;

    void process(int* p) {
        if (p == nullptr) {
            error_flag = 1;
        }

        std::observable_checkpoint();

        ++*p;
    }
    ```

    现在后面的 UB 依然是 UB，但是不能对前面的可观测性逻辑做影响


**C++ Weekly - Ep 69 - C++17's Searchers** https://www.youtube.com/watch?v=YUCW_MnksjQ

- 省流：C++ 17 加入了 searcher，可以选择 bm/bm-horspool 做大文本优化

**C++ Weekly - Ep 68 - std::iota** https://www.youtube.com/watch?v=fW69R_BtHmU

- 介绍 iota，可以从起始点累进 integer 的东西
- jason 觉得这个很鸡肋，可以用 std::generate + stateful lambda 替代

**C++ Weekly - Ep 67 - C++17's std::gcd and std::lcm** https://www.youtube.com/watch?v=akak2jYXOVg

- 介绍 gcd / lcm

\#2

这周给 fawkes 做了两个改进

- Add rejection response hook https://github.com/kingsamchen/fawkes/pull/45
- Add TCP_NODELAY for request sockets & stop request reading/parsing as soon as possible, if server is shutting down https://github.com/kingsamchen/fawkes/pull/46

这两个都是在迁移过程中才发现的并且做的 patch/fix 于是一起迁移过来了。

有一说一，有了 AI 后感觉做事情的速度确实有提升，就是人变更累了。

\#3

还记得老早之前我吐槽的这个 [post](https://ddanilov.me/usage-of-shared_ptr) 吗？

我觉得他的做法纯属给自己找麻烦，如果能接受短暂的读到的数据时旧的配置，则可以用 shared_ptr 做 copy & swap

于是我让 GPT 帮我验证了这个想法

> 我基本同意你的判断。对于需要组合读取多个字段的配置，让 Config 持有一个不可变快照更合理：版本一致性、字段引用的生命周期，以及读取开销，都更容易处理。
>
> 原文的 Config 保存的是 manager 中那个 shared_ptr 变量的引用，每次 getter 再原子加载一份临时 owner。于是你指出的两个问题都成立：
> - name() 和 version() 之间发生更新，就可能读到不同版本。每次读取各自安全，不代表组合起来满足配置的约束。
> - 如果 name() 返回引用，getter 里的临时 owner 销毁后，一旦 manager 也替换了旧版本，引用就可能悬空。按值返回 std::string 能解决这个问题，但确实需要复制字符串；uint16_t 这种小字段按> 值返回倒很合适。
> 作者也明确承认跨 getter 的版本问题，并记录了将 name() 改为按值返回的修复

并且很容易的就可以让 GPT 6.1 Sol 按照我的想法给一个可以跑的 demo

```cpp
#pragma once

#include <atomic>
#include <cassert>
#include <cstdint>
#include <memory>
#include <string>
#include <utility>

namespace snapshot_config {

// Mutable input value. The manager publishes a separate, const instance.
struct ConfigData final {
    std::string name;
    std::uint16_t version{};
};

class ConfigManager;

// An owning handle to one immutable configuration version.
class Config final {
public:
    Config(const Config&) noexcept = default;
    Config& operator=(const Config&) noexcept = default;
    Config(Config&&) noexcept = default;
    Config& operator=(Config&&) noexcept = default;

    // Borrowing is safe while an owner keeps this version alive.
    [[nodiscard]] const std::string& name() const & noexcept {
        return data().name;
    }

    // Do not lend a reference from a temporary snapshot.
    [[nodiscard]] std::string name() const && {
        return data().name;
    }

    [[nodiscard]] std::uint16_t version() const noexcept {
        return data().version;
    }

private:
    friend class ConfigManager;

    explicit Config(std::shared_ptr<const ConfigData> data) noexcept
        : data_(std::move(data)) {}

    [[nodiscard]] const ConfigData& data() const noexcept {
        // A moved-from Config may only be destroyed or assigned a new value.
        assert(data_);
        return *data_;
    }

    std::shared_ptr<const ConfigData> data_;
};

class ConfigManager final {
public:
    // A manager always starts with a valid configuration.
    explicit ConfigManager(ConfigData initial)
        : current_(std::make_shared<const ConfigData>(std::move(initial))) {}

    ConfigManager(const ConfigManager&) = delete;
    ConfigManager& operator=(const ConfigManager&) = delete;
    ConfigManager(ConfigManager&&) = delete;
    ConfigManager& operator=(ConfigManager&&) = delete;

    [[nodiscard]] Config get() const noexcept {
        return Config{current_.load(std::memory_order_acquire)};
    }

    // Allocate and construct before publishing. make_shared can throw
    // std::bad_alloc; a failed call never reaches the noexcept store below.
    // Concurrent full replacements are allowed; their atomic stores determine
    // publication order.
    void reload(ConfigData next) {
        auto replacement =
            std::make_shared<const ConfigData>(std::move(next));
        current_.store(std::move(replacement), std::memory_order_release);
    }

private:
    std::atomic<std::shared_ptr<const ConfigData>> current_;
};

}  // namespace snapshot_config
```

不得不说，有了 AI 之后一些东西的研究其实都可以变得很深入。

---

这周就这样，下周见
