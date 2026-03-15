---
title: 一周杂记 in Week 2 Mar 2026
categories: CODE-LIFE
date: 2026-03-15 22:22:54
tags: [杂记]
---
本周（03/09 ~ 03/15）是3月份的第二周，天气渐渐暖起来了。

## Life

\#1

这周 HRV 和 RHR 又全面烂了；周三没休息好还去正常配速跑了30分钟，心率直接飙到175+。。。拉完了

下半周基本处于恢复期 🤦‍

\#2

周四团建，中午吃的御牛道，东西确实还不错。也就是团建预算所以库库的点

吃完之后在火星工厂和suyang打了一下午的 it takes two，而且我俩配合不错，一下午直接打完第三关。其他同事都说我俩进度真快 🤣

不过比较抽象的是老崔吃完中饭之后带着g1 g2 g4带头回去加班了，真牛逼。老崔心里估计想早点把我们这些老人都干掉，好让崔家军都按照他的要求工作

\#3

周六去chao家参观，顺带打了一局德普，小赚了102。

不过晚饭倒是一般的，小声说🤫，不过老婆意见比较大，本来就是想来蹭饭的，结果饭这么难吃（其实我倒觉得没那么差）

\#4

本周观影

- **If I Had Legs, I Would Kick You** 3/5 过分“女权”了 虽然探讨的是社会预期的母职要求对女性的影响/束缚 但是呈现出来的完全是就是女主自己那句话：她不是一个适合当妈的人。 而且难以想象女主居然是是一个心理医生，我觉得女主的丈夫和患病的孩子才更可怜

Iker 要离职了，last day 4.2，哎 😔

## Work

\#1

**CppNow 2025 | Alex Stepanov, Generic Programming, and the C++ STL - Jon Kalb** https://www.youtube.com/watch?v=yUa6Uxq25tQ&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=51

- 非常值得一看的 talk，讲的 STL 的前世今生，以及为什么一些东西是这么设计的
- 另外提到了STL很多实现是在1990s的背景下做的，现在设备发生了很大的变化，例如 malloc 变得极其昂贵，cache 越来越重要，所以STL势必要做一些改变，比如 flat_map/unordered_flat_map .etc 以及 stick to the std::vector if you can.

**CppNow 2025 | Balancing the Books: Access Right Tracking for C++ - Lisa Lippincott** https://www.youtube.com/watch?v=wQQP_si_VR8&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=50

- 非常理论而且抽象的一个talk，听了一半发现核心应该是对应 C++ lifetime profile 的一些设计
- 不过我好奇那个设计真的可靠吗？评论里 Sean Baxtor 就在吐槽说她讲的一些点有问题

**CppCon 2023 | Libraries: A First Step Toward Standard C++ Dependency Management - Bret Brown & Bill Hoffman** https://www.youtube.com/watch?v=IwuBZpLUq8Q&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=30

- 算是正式提出 CPS，目标是能联合主要的 package manager，统一 package spec，这样 conan 产生的 vcpkg 也可以用
- 感觉算是个好开头

**CppCon 2023 | Getting Started with C++ - Michael Price** https://www.youtube.com/watch?v=NReDubvNjRg&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=31

- 内容比预想的好太多了，从IDE到compiler toolchain到build system 到 package manager
- 确实非常适合新手

**Job Hunting and Optimizing Compilers with Jamie Pendergast - CppCast Ep404 - C++ Weekly Ep 521** https://www.youtube.com/watch?v=vzar4IDKTys

- 这一期主要是 cppcast 了，采访了一个小哥，讲了一下他的求职经历
- 小哥背景很杂，做过web也做过游戏，找工作时也遇到了 entry-level paradox；同时还遭遇了诈骗招聘
- 提到了小哥现在在做的一些事情，例如用C++写一个 optimizing compiler 和 minecraft server

**C++ Weekly - Ep 522 - Don't Remove Code. =delete it!** https://www.youtube.com/watch?v=gwwxD_l9T28

- 其实标题说的是不要删除 overloads，因为会有 implicit conversion 会导致意外
- 更好的做法是把函数标记为 `=delete;`

**C++ Weekly - Ep 179 - Power C - A Native C Compiler for the Commodore 64** https://www.youtube.com/watch?v=yCEG30yQS5w

- 展示 commodore 64 上的 C 编译器！？

**C++ Weekly - Ep 178 - The Important Parts of C++14 In 9 Minutes** https://www.youtube.com/watch?v=mXxNvaEdNHI&t=12s

- C++ 14 的核心features；有点老了

**C++ Weekly - Ep 177 - `std::bind_front` Implemented With Lambdas** https://www.youtube.com/watch?v=fLeHy7s1WIo

- 把 bound_params 塞到 capture 里 foward 传入；结果 closure 的参数 forward 传入原函数的后半部分

**C++ Weekly - Ep 176 - Important Parts of C++11 in 12 Minutes** https://www.youtube.com/watch?v=D5n6xMUKU3A

- 更老了，跳着看都行

\#2

这周终于把 Asio 的 cancellation 的文章写完了，也快累死了。。。

写完感觉轻松好多，而且写的过程也是对原有理解的进一步加深，还顺带练习了英文写作。

\#3

这周在公司“偷摸”终于把 conan 出包的流程跑通了，真的是一波三折。

说是偷摸是因为这是给 chao 的 scheduler 打黑工，没法摆到台面上给老崔讲的。不过没办法，这个部门看来看去只有 chao 的 scheduler 最有希望活下来。

后面就是逐个依赖的上 conan，然后完成第一步，把 scheduler repo 的所有三方依赖都迁移到 conan 上

\#4

因为 cmake-format 和 gersemi 这两个开源的 cmake format 项目都不太能满足我的 style 需求，所以周五的时候用 Cursor 开着 claude opus 生成了一个我的 style 定制版的 cmake formater

目前初步用下来应该还需要 fine tune，等下周调完了再看看情况。

opus 干这种活确实挺猛的

---

好了这周就这样，下周见
