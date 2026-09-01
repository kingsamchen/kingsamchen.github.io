---
title: 一周杂记 in Week 4 Aug 2026
categories: CODE-LIFE
date: 2026-09-01 00:00:47
tags:
---
本周（8.24 ~ 8.30）是8月份最后一周，下周就进入9月份了。

## Life

\#1

又续了12节拳击课，不过因为是1对1，所以其实和私教课差不多，算完优惠一节240块钱

周日上午上了拳击课，因为教练落了带绷带，所以这节核心还是调整技术动作；半小时热身，打了1个半小时，打爽了

不过后面的课就是周六下午了

\#2

这周 CEO 来杭州了， 周四下去西湖文体 all hands，感觉大部分还是车轱辘话。

另外听说我们组的美国大经理 Strong 下课了，还是休假期间下课的，因为上面又派了一个 senior manager 过来了；不过也有说上面这条消息可能是个乌龙，看来还需要观察一下。

scheduler 这边产品汇报算是出乎意料的好，低（人力/机器）成本和客观的营收老板都比较满意

## Work

\#1

**CppCon 2023 | A Fast, Compliant JSON Pull Parser for Writing Robust Applications - Jonathan Müller**  https://www.youtube.com/watch?v=_GrHKyUYyRc&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=58

- 这个 talk 实在有点难了，让 G 老师给我翻译了一下大概是
- Presenter 认为 DOM parsing 和 SAX parsing 都不如 pull parsing，因为 pull parsing 是 consumer driven，可以做到 “不要让 JSON parser 告诉你“这里有什么”，而应该由你的代码告诉 parser“我期望这里有什么”
- 但是从一个使用者角度说，不管你的 parsing 到底用的什么model，只要性能过关，大家更在意的是一个好用的 serde layer

**CppCon 2023 | Expressive Compile-time Parsers in C++ - Alon Wolf** https://www.youtube.com/watch?v=F5v_q62S3Vg&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=59

- 有点综述性质，介绍目前 C++ 里 compile-time parser 的一些做法还有常用库
- 作者自己写了一个 通用型的 YACP

**CppCon 2023 | Lightning Talk: Write Valid C++ and Python in One File - Roth Michaels** https://www.youtube.com/watch?v=GXwYjI9cJd0&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=64

- 非常好玩，但是感觉一般没必要学习
- 因为 `#` 在 python 里会被识别为 comment，所以利用这个 trick，一个文件经过处理后既可以在 cpp 里用，又可以在 python 里用

**C++ Weekly - Ep 547 - Using constexpr to Catch Memory Errors** https://www.youtube.com/watch?v=-LAXqqqX274

- 展示用 consteval block 来抓手搓 unique_ptr 内存问题
- 其实也可以抓 UB，但是使用场景不一定宽

**C++ Weekly - Ep 89 - Overusing Lambdas** https://www.youtube.com/watch?v=OmKMNQFx_8Y

- generic lambda 在 generic heavy context 上可能会导致 code bloat 以及构建时间明显变长

**C++ Weekly - Ep 87 - std::optional** https://www.youtube.com/watch?v=PiaZkNp_fIM

- 介绍 std::optional<>

**C++ Weekly - Ep 88 - Don't Forget About puts** https://www.youtube.com/watch?v=VZTVmKOXLVU

- 省流：够用的时候 std::puts 就够好

**C++ Weekly - Ep 86 - Valgrind** https://www.youtube.com/watch?v=3l0BQs2ThTo

- 介绍 valgrind
- 不过如果编译器支持，还是用 sanitizer 吧；valgrind 慢了接近100x

\#2

终于把 fawkes 集成到了 scheduler；coding style / 文件 naming 全都入乡随俗了

不过类名/函数名，不改了，这个改起来要死，而且和标准库/boost保持一致没啥不好的

后面就要开始着手 httpd 的迁移工作了，这个感觉也是个大坑，而且按照标准完全迁移完要好久。

\#3

趁着引入 fawkes 把 tidy 和 format 也做了一次大扫除/大对齐

tidy 增加后现在一次 full clang tidy checks 会出来接近5000个错误，于是我跟 oncall 说这周先我来处理，因为有些 check 会需要评估一下

这个做的累死我了…字面意义的一个周末都在修 tidy

---

好了这周就这样，下周见
