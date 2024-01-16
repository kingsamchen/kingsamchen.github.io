---
title: 一周杂记 in Week 2 Jan 2024
categories: CODE-LIFE
date: 2024-01-15 00:58:21
tags: [杂记]
---
本周（01/08 ~ 01/14）是1月份的第二周。

## Life

\#1

周二晚和同事去乐刻练了一把力量，结果上肢练的有点过火，第二天开始酸疼，并且后续两天酸痛感越来越强，一直持续到周末...

周六在家边上的乐刻跑了一次7km的有氧，跑完后感觉右腿有点说不上来的疼，一开始没咋注意，结果第二天周日发现提腿这个动作会触发明显的疼痛感，这下一时间不知道是膝盖受伤了还是哪受伤了。

本来打算周日也继续跑个7km，但是现在有点损伤，想了想还是不练了，改为下楼在小区走了几圈，当作轻度有氧吧 🤣

\#2

这周没有参加周三的电影俱乐部，在家抽了点时间看了一部分的 blade runner 2049，还没看完...

感觉最近观影的激情不是很充足 😂

## Work

\#1

本周学习进度

**HBase原理与实践 | 13. HBase 系统调优**

- hbase 和 hdfs 的参数调优
- 偏运维向

**HBase原理与实践 | 14. HBase 运维案例分析**

- 讲了几个作者实际中遇到的案例，主要涵盖了 region server 因为各种原因（STW GC，CLOSE_WAIT 过多导致端口不够 .etc）宕机，业务方读写出现延迟阻塞等

**Building Microservices | 14. User Interfaces**

- 前端页面的业务划分范式（其实如果走前后端分离的话其实后端服务不用太关注这个）
- 聚合网关（aggregating gateway services）和 BFF

**CppCon 2021 | C++20 Templates: The next level: Concepts and more - Andreas Fertig**

- 干货不少，从非常实用的角度讲了类模板和函数模板中 concepts 的使用，以及相对之前 SFINA 简化了什么
- 强烈推荐观看

**CppCon 2021 | Plenary: Small Inspiration - Michael Caisse**

- cppcon 开场性质的talk，主要用于打鸡血和调动气氛
- 不过实话说干货还是有一些的，比如提到了需要重视社区的反馈，年轻人梯队的培养（talk 中是拿自己小时候好奇心和一些喜欢电子设备的年轻人举例）
- 可以当作英语听力练习 XD

**Jonathan Müller - The Static Initialization Order Fiasco - Meeting C++ 2020**

- 众所周知不同TU里的全局变量的初始化顺序是unspecified的，这也是 static initialization order fiasco 标题的由来
- 这个talk比较仔细地讲了这个问题，以及提供了 constant initialization, lazy initiailization and nifty counter 三种方案
- 不过我个人的看法和作者最后的意见基本一致，尽量避免全局变量（含 singleton）之间存在互相依赖，以及尽量避免在 main 之前或者之后有过多的操作
- 另外作者夹带私货的推荐了他自己的 atum 基础库，里面有一些 utilities 可以辅助全局初始化

****Leverage the richness of HTTP status codes**** https://blog.frankel.ch/leverage-richness-http-status-codes/

- 介绍了一些小众的 http status code 用于 rest api 的场景
- 实际上 MDN 上有更多的 status code，不过我这么多年的经验，除非你设计的 http api 确实是朝着 rest style 走的，并且考虑到了 browser 的交互，否则很少人真的会把 api 认真设计成 rest 的样子

\#2

研究了一下 CMake 中 Ninja Multi-Config 的配置问题，学到了如下几个点

- configuration stage 会生成所有 `CMAKE_CONFIGURATION_TYPES` 包含的 config，并且第一个 config 就是默认 config
- 如果希望生成的 configs 只有 Debug/Release，则可以用
  ```shell
  # 只生成 Debug/Release 两种 config，并且默认 Config 为 Debug
  CMAKE_CONFIGURATION_TYPES="Debug;Release"
  ```
  其中 Debug 是默认 config
- 如果想维持 configuration types 列表，但是又针对某种情况希望 Release 是默认 config，可以设置变量 `CMAKE_DEFAULT_BUILD_TYPE`:
  ```shell
  CMAKE_DEFAULT_BUILD_TYPE="Release"
  ```
- 默认 config 会影响到生成的 compile_commands.json，进而影响 clangd 或其他 LSP 的感知
- 默认情况下 ninja 就是采用的并行编译，并且会默认使用核心数作为 job 数，所以不用在额外的使用 `-jN` 了
  这点重要是因为在 presets 中没有很自然的方式拿到 number of cpu processors

\#3

有了上面的打底之后，给 esl 做了 CMakePresets 的改造，即去掉了以前用来编译的 build.py，全面使用 CMakePresets.json 来管理工程。

目前给 Linux 和 Windows 分别建立了各自的 config presets，并且因为 CMake v3.25 开始支持 workflow presets，可以定义一个 workflow 来跑配置好的 config/build/test/package 等任务。

不用想，这是冲着 modern pipeline 去的

并且使用 CMakePresets 有个好处：VSCode 配合巨硬官方的 CMake 插件是能够理解 presets 的，这样一来 VSCode 里可以直接选择想要用的 presets，能直接和 CLI 模式下 unify 在一起，越来越像一个 IDE setup 了。

相关的 PR 见：https://github.com/kingsamchen/esl/pull/11

\#4

这周在赶一个 task 的时候需要使用 Howard Hinnent 的 Date Library，也即进入 C++ 20 的那个库。

在试图获取当前时间戳对应的日期的 hh:mm:ss 时我写了如下代码（这里直接用 C++ 20 来描述）

```cpp
auto tp = std::chrono::hh_mm_ss{std::chrono::system_clock::now().time_since_epoch()};
std::cout << tp ;
```

本来我预期会输出 `12:39:48` 这种形式的结果，等跑实际代码的时候发现居然是 `473604:39:48`；比如 https://gcc.godbolt.org/z/fnohv8GcK

想了很久都没想明白为什么会这样。

直到吃晚饭的时候才回过神来：C++ 标准库这套 chrono/date 的本质就是 time_point，剩下的其他类型都是 time_point 在各自类型/量纲上的投影，即以目标类型来解释这个 time_point。

duration 这种很自然的就不说了，这里讲讲 hh_mm_ss

这个结构其实是一个 hours/minutes/seconds 的复合结构，对于某个给定的 time_point 来说，要套用这个类型 interpret 就会变成这个 time_point “折合”约为多少 hours 又 minutes 和 seonds

所以输出结果的 hours 会非常巨大，因为他反应的是完整的 time_point 对应的 duration

如果想要拿到这个 tp 对应的 date 中的 time of day，则应该需要做一次变换：

```cpp
auto tp = std::chrono::system_clock::now();
auto dt = tp - std::chrono::floor<std::chrono::days>(tp);
std::cout << std::chrono::hh_mm_ss{dt};
```

即这个 time_point 首先得是 time of day 的，才能用 hh_mm_ss 去解释。

至于为什么这么设计，我没去翻 proposal，猜想和性能以及灵活性有关。

因为几乎所有类型底下都可以用一个 time_point 来表示，占用空间少，且日期操作更容易。

如果是其他语言那种首先把 time_point explode 成一个 datetime struct，虽然直接读某个分量比较容易，但是日期的算术操作就会变得麻烦的多，而且这个 datetime struct 明显大得多

不过不得不说标准库这样设计确实有一点点使用门槛过高

---

好了这周就这样，下周见
