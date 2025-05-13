---
title: 一周杂记 in Week 2 May 2025
categories: CODE-LIFE
date: 2025-05-11 23:38:00
tags: [杂记]
---
本周（05/05 ~ 05/11）是五月的第二周，过完了周一五一最后一个假期，又得开始恢复搬砖生活了。

## Life

\#1

周一是5/5，愉快的假期总是如此的短暂。

不过还好这周就只需要上4天班，可以缓一缓情绪。

假期期间一直没搞落户的事情，假期结束之后反正进入上班了，那就开始搞起吧。

线上流程之前跑过了还算比较快的，线下因为需要老户口本加盖迁出章，所以线上寄送新户口本没啥用，派出所还是让我线下跑一趟。

不过还好派出所离我家不算远，开个车10分钟内能到；其次因为线上流程都跑完了，所以线下其实也超快，轮到我之后印象中差不多3-5分钟就弄完了。

终于变成杭州人了 🤣 这下和媳妇儿各自都成为户主了

\#2

这周媳妇儿基本足月了，所以假期结束开始上班之后我就开始早上开车接送媳妇儿上班。

晚上12点之前就要上床入睡了，为了缓解睡眠焦虑，每晚都要听十几分钟纯音乐辅助；还别说，效果还行，连续一周早上 6:55 起床 Garmin 的睡眠分数还能维持在80分上下，睡眠时间至少6个半小时以上。

周六带了媳妇儿去做了应该是产前最后一次产检，因为项目多差不多花了半天时间。

而且媳妇儿吐槽胎心监护的护士是个新手，很多操作都不对，而且第一次出来的记录医生说分段了让重做了一遍 😂

中午等报告间隙开车到了西溪印象城吃了一顿一席地，顺带开了一张山姆的 premium 会员卡，把副卡给丈母娘了。刚好媳妇儿家边上就有另外一家山姆

周日下午开车陪媳妇儿回她家，和丈母娘一起整理了一下待产包和待产衣服，吃了个晚饭就回来了。

不知道是不是周日的缘故，下午3-4点就开始堵车了，晚上7点多回来的时候反而很顺畅。

\#3

这周电影看得不少，重启了家庭影院，和媳妇儿看了“因果报应”；蹭了 Hogan 的招行观影打折券看了“荒蛮故事”；自己抽了一个晚上时间把“生物突击队S1”看完了。

- **因果报应** 4/5 叠加反转有点意思，但是多条时间线剪辑这块容易产生混乱。另外警察收钱还真给办事啊，笑死
- **荒蛮故事** 4/5 南美电影真的是大胆而且充满想象力，虽然现实社会往往比影视作品更加荒诞
- **生物突击队S1** 3.5/5 一晚上刷完了，滚导的群像叙事还是水准在线的，就是这动画剧集单集太短，想挖挖人物只能选那么一两个来下功夫了

## Work

\#1

**CppCon 2022 | Lightning Talk: Finding Whether a Number is a Power of 2 - Ankur Satle** https://www.youtube.com/watch?v=Df-qEsWjzQw&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=113

- C++ 20 引入了一些 [bit manipulation functions](https://en.cppreference.com/w/cpp/utility/bit)，例如 popcount，编译后能生成对应的内建指令，例如 popcnt
- 不过在 compiler explorer 试了一下，需要手动指定 `-mavx2` 这种让编译器启用 vectorization 才会生成对应指令 🤔

**CppCon 2022 | Lightning Talk: Ref, C++ const ref, immutable ref? - Francesco Zoffoli** https://www.youtube.com/watch?v=OupN6FMZbmA&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=115

- 问题的症结看起来像是 aliasing 不过作者想的是另外一套方案

**CppCon 2022 | High Speed Query Execution with Accelerators and C++ - Alex Dathskovsky** https://www.youtube.com/watch?v=V_5p5rukBlQ&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=114

- 听起来是做存储计算的，太高端了，完全没听懂

**CppCon 2022 | Architecting Multithreaded Robotics Applications in C++ - Arian Ajdari** https://www.youtube.com/watch?v=Wzkl_FugMc0&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=116

- 过于专业领域了，没法评价
- 粗看 robotics 领域可以用 pthread 和 MPI 做多线程

**CppNow 2024 | The C++ Vector Challenge - Lisa Lippincott** https://www.youtube.com/watch?v=qlRoNXYK4-E&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=51

- 这个阿姨做的工作都是搞抽象的（非贬义），这次是形式化的 pre-condition/post-condition
- 不过听着挺累的就是

**C++ Weekly - Ep 350 - The Right Way to Write C++ Code in 2022** https://www.youtube.com/watch?v=q7Gv4J3FyYE

- 一个符合现代的C++ project 的起手式
- 该说不说，现在工具链这块还是有明显进步的

**C++ Weekly - Ep 349 - C++23's move_only_function** https://www.youtube.com/watch?v=OJtGOJI0JEw

- C++ 23 引入 `std::move_only_function<>`
- 这个 wrapper 自身是 non-copyable 的，并且它能保存带有 non-copyable capture 的 lambda

**C++ Weekly - Ep 348 - A Modern C++ Quick Start Tutorial - 90 Topics in 20 Minutes** https://www.youtube.com/watch?v=VpqwCDSfgz0

- 一些 modern features 的 demo 速览
- 不过内容不是很全，毕竟都十几年的 features 摞在一起还是太多了

**C++ Weekly - Ep 347 - This PlayStation Jailbreak NEVER SHOULD HAVE HAPPENED** https://www.youtube.com/watch?v=rWCvk4KZuV4

- 核心就是打开 `-Wall -Wextra -Wconversion -Werror`
- 越狱用的漏洞就是一个 signed → unsigned 的 malloc…

**CppCon 2022 | Lightning Talk: Embrace Leaky Abstractions in C++ - Phil Nash** https://www.youtube.com/watch?v=uh15LjpBIP0&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=117

- 更进一步，all abstractions are leaky
- 所以首先写 simple small 的代码，别一上来搞一个超大的抽象

**What is the “vector pessimization”?** https://quuxplusone.github.io/blog/2022/08/26/vector-pessimization/

- 核心就是 move ctor 能 noexcept 的一定要做成 noexcept 否则 vector resize 的时候会为了 strong exception safety 而不使用 move 导致降低性能
- 另外还提到以前 MSVC STL 实现的 std::list 采用的是 sentinel node，导致默认构造也会需要 heap allocation for sentinel node

**Using std::unique_ptr With C APIs** https://eklitzke.org/use-unique-ptr-with-c-apis

- 就是很 conventional 的用 unique_ptr 的 custom deleter 的方法，评价是不如 esl 的 handle_ptr https://github.com/kingsamchen/esl/blob/master/esl/unique_handle.h

**A moved-from optional** https://akrzemi1.wordpress.com/2022/09/06/a-moved-from-optional/

- moved-from object 并不一定会被 set to null/empty state，标准没说这个，很多实践中遇到的这个行为反而是 side-effect
- `optional<T>` 被移动之后不是 empty state，并且这个行为是出于优化考虑
- `optional<T>` 有针对 T=trivial 的优化

\#2

最近开始写 http-router 的 locate，因为最近事情太多，所以这块进度比较慢 🤡

---

好了这周就这样，下周见
