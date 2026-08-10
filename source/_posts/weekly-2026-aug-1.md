---
title: 一周杂记 in Week 1 Aug 2026
categories: CODE-LIFE
date: 2026-08-10 22:47:42
tags: [杂记]
---
本周（08/03 ~ 08/09）是八月份第一周，台风又来了 🤡

## Life

\#1

这周秋宝的疹子还没有痊愈，虽然大部分部位都好了，但是还是半夜时不时会痒醒，然后哭一阵；白天偶尔会抓的有点红，所以目前还是不能掉以轻心的。

另外秋宝现在是几乎不睡自己的小床了，一定要和媳妇儿挤在大床上，估计也是发现了大床舒服啊，宽敞又大，随便翻身。

\#2

周一凌晨被秋宝半夜吵得没睡好，然后一大早8点不到又起床去同德体检了，哎

大概四五十分钟全部流程走完，但是因为做了一个直肠指检，屁股都是润滑油的感觉，加上天气实在太热就索性先回家洗个澡，然后再开车去公司。

晚上下班后就开车带着 Jed 去新天地的星光佳映看蜘蛛侠4，还好我们下班没多久就出发了，给自己预留了一个半小时的时间，不然80%的路程堵车真的是会错过电影。

不过这次犯了一个错误，应该直接把车停在电影院地下停车场，而不是停在边上走过去，毕竟几百米的路程也不短，而且还没法用电影票抵消停车时长。

\#3

周中热的要死结果到了周末台风来了...

周六下午开始下雨，周日下了一整天的大雨，约好的拳击训练营也因为大雨取消了

更离谱的是公司说周一是否让大家居家看周一是否下大雨。。。

\#4

- **蜘蛛侠：崭新之日 Spider-Man: Brand New Day** 4/5 故事叙事上荷兰弟版目前最佳，比2还要好，排除同框情怀其实3叙事不太行。这版的凤凰女感觉有点天选，是个好开头；蜘蛛侠和罚叔的搭配有点狼贱的感觉，化学反应不错。马律师：咩？那我呢

## Work

\#1

**CppNow 2025 | Effective CTest - a Random Selection of C++ Best Practices - Daniel Pfeifer** https://www.youtube.com/watch?v=whaPQ5BU2y8&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=30

- 核心大意是，ctest 的 script mode 可以做非常非常多的事情

    update source
    → configure
    → build
    → test
    → coverage / dynamic analysis
    → submit structured results

- 这个分享有点诡异，作者应该是站在 pipeline maintainer 角度的，所以感觉有一些不必要的事情都想让 ctest 去做了

**CppNow 2025 | Mastering the Code Review Process - Boosting C++ Code Quality in your Organization - Peter Muldoon** https://www.youtube.com/watch?v=buWtKvShi0U&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=35

- 分享的现在如何做 code review 的一些想法，不过暂时没有包括 AI 生成代码的部分
- 目标：Correct + Maintainable + Fit for Purpose + Fast to Deliver + Healthy Long-Term
- PR/MR 自身应该满足一些最基本的要求，例如通过了静态分析的 pipeline job，包含最基本的测试用例，并且相关的测试用例都是通过状态 .etc
- Review for
    - correctness: 是否满足 claim 的需求 / 实现上是否有明显的缺陷 / 错误处理
    - design: 是否有合适的抽象 / 过度的复杂度 / 过早优化的 trick
    - maintainability: 后续的可维护程度 / 边界隔离 / 过分面向某个 case 的实现（not a general solution）；这部分有点介于整体设计和具体细节之间
    - tests: 测试匹配
- 关注（但不要过分关注）MR/PR 的一些指标，例如
    - 初次 review 和 review feedback 的处理都应该在 24 小时内
    - 单个 MR 变更代码量应该尽量控制在 500 LoC 内
- Review 也是一种团队文化的交流，D.B.A.D (Don’t Be a Dick 我猜的)
- 总结：code review quality is not measured by how difficult it is to get a PR approved; it is measured by how reliably the process produces good code, spreads understanding, and gets valuable changes safely into production

**CppNow 2025 | C++ Program Correctness and its Limitations - David Sankel** https://www.youtube.com/watch?v=In2elCXQ10A&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=34

- 这篇比较形而上学了
- 核心讨论：A program is never simply “correct.” It is only correct with respect to some specification, model, assumptions, and environment.

**C++ Weekly - Ep 544 - UB & Ranges of Enumerations & constexpr** https://www.youtube.com/watch?v=AhMlBVnTYkE

- C-style 的 non-scoped enum，underlying type 会用最小能容纳所有范围的值的 bits 数量，超过就是 UB

    ```cpp
    enum foo {
     a = 1,
     b = 2
    };
    foo f = static_cast<foo>(4) // underlying type contains two bits
    														// out of range
    ```


**C++ Weekly - Ep 102 - Learning "Modern C++" - 2: Hello World** https://www.youtube.com/watch?v=juJaaCf_yKc

- 更多是介绍 clang-tidy。。。

**C++ Weekly - Ep 101 - Learning "Modern" C++ - 1: The Tools**

https://www.youtube.com/watch?v=zMrP8heIz3g

- 介绍 vs community 2017 / clang-tidy / clang-format / cppcheck

**C++ Weekly - Ep 100 - All The Assignment Operators** https://www.youtube.com/watch?v=2gjroKLyWKE

- ref / rval ref qualified
- const qualified

**C++ Weekly - Ep 99 - C++ 20's Default Bit-field Member Initializers**  https://www.youtube.com/watch?v=w0rE7GqRPes

- C++20 支持 bit fields 的 in class member initialization

    ```cpp
    struct S {
      int i : 3 = 0;
      int j : 5 = 0;
    };
    ```

\#2

这周核心是把 scheduler 的 dockerfile 迁移到 ubi9，并且顺带把基础构建的 build image 从以前的 async-comm 里拆出来

大部分工作做完了，下周需要 dev 环境多试试，毕竟因为公司政策要求最终运行时镜像必须要走 ubi9-micro，dockerfile 变动还是大的

FYI，我一开始以为 ubi9-micro 连 shell 都没有，后来发现还是有 /bin/sh 和 /bin/bash 的，并且前者就是后者的 symbol link，只不过 micro 是真的没有 dnf/yum

\#3

周五和 suyang 借助 G 老师，把困扰已久的 httpd 进程退出卡住的元凶找到了，是 aws s3 client 的问题，直接引爆点是 conan package 升级到新版本后的行为变化，导致我们以前实现略微有点问题直接被暴露出来了。

确认的方式也有点搞笑，是让 OPS 在 QA 环境用 `kill -ABRT` 对 suspect process 强行生成 coredump，然后检查 callstack 最终确认。

因为直接在 pod 里无法使用 eu-stack 或者 gdb 检查调用栈，最终和 G 老师沟通过程中发现了可以直接 SIGABRT 这个邪修。

---

这周就这样，下周见
