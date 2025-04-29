---
title: 一周杂记 in Week 4 Apr 2025
categories: CODE-LIFE
date: 2025-04-28 00:35:05
tags: [杂记]
---
本周（04/21 ~ 04/27）是四月份最后一周，下周四就是五月一号了。

## Life

\#1

因为这周阴雨天比较多，所以趁着周三天气好，赶紧把家里的车安排到对面的麦道夫给洗了。

不过很坑的是，这家店下架了之前的48块钱美团普洗团购券；但是我看快精洗和精洗券还是有很大程度的折扣，所以就走了精洗。

结果洗完了告诉我他们家已经独立了，美团上的门店券不能用了。。。最后花了198块钱

真的是 mmp，以后直接去稍远的一家店了，再也不来这家麦道夫了，真尼玛扯淡。

\#2

除了洗车，趁着周日刚好调休 WFH 约了小区团购的空调上门清洗。

讲道理，这次的空调清洁还是可以的，比2-3年前那次好得多，挡风板和内网都拆下来清洗并且安装的时候还都擦干净了。

整体算下来花了512，比之前的清洗性价比要高不少。

最后师傅走之前我还一人给了一瓶怡宝。

\#3

因为下周五一假期，所以这周日给搞成了单休。

虽然单休在家 WFH 但是还是真的好累啊

\#4

周三晚上跑去公司参加了电影俱乐部

- **优等生社团 Honor Society** 3.5/5 有点意思，虽然有些地方过于刻板了。另外nerd风评被害啊

## Work

\#1

**CppCon 2022 | Lightning Talk: Standard Standards for C++ - Ezra Chung** https://www.youtube.com/watch?v=vds3uT9dRCc&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=108

- 核心就是 there are not one but many C++ standards

**CppCon 2022 | Breaking Enigma With the Power of Modern C++ - Mathieu Ropert** https://www.youtube.com/watch?v=zx3wX-fAv_o&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=109

- 说是拿 C++ 写一个 enigma 破解机但是通篇也没说具体实现，然后前面各种介绍 enigma 原理的也看得想睡
- 最后50分钟开始讲的 C++ part 也没啥干货，就是历史部分当听评书还行…

**CppCon 2022 | Lightning Talk: find-move-candidates in Cpp - Chris Cotter** https://www.youtube.com/watch?v=F8wbpi2kTmY&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=111

- bloomberg 内部自己有基于 llvm 做的 clangmetatool，提供额外clang-tidy的检查

**CppNow 2024 | Designing a Slimmer Vector of C++ Variants - Christopher Fretz** https://www.youtube.com/watch?v=NWC_aA7iyKc&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=55

- 直接使用 `std::vector<std::varaint<>>` 的问题是某个子类型尺寸过大的话会引起整个variant尺寸都放大，严重浪费内存
- 作者自己的 vv::vector 直接做进了 variant 的支持，用类型表和把动态数组来解决；不过我没看懂如果某个对象类型换了他是怎么做切换的，不知道是我看漏了还是视频没提到
- 不过是我的话我会把大尺寸对象直接用指针或者类似的东西包一下把实际存储塞到堆上，不过可能要考虑一下 copy behavior

**CppNow 2024 | Modernizing Finite State Machines in C++ - Empower Simplicity & Boost Performance With std::variant** https://www.youtube.com/watch?v=1jbLzupBus4&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=52

- 这个 talk 有点意思，用 std::variant 来存每个状态，然后在这个之上实现 FSM；并且前面还给出了 enum 和 state pattern 的实现进行对比
- variant-based 的 FSM 还做了一些优化，不过我觉得到 transaction table 那部分就可以了，最后面那个拆的太细了在实际工程中可能会有坑
- sample repo 在 [https://github.com/adchawla/FSM](https://github.com/adchawla/FSM/blob/main/CMakeLists.txt)

**C++ Weekly - Ep 476 - C++23's Fold Algorithms** https://www.youtube.com/watch?v=s8UInkalVgs

- C++ 23 开始 ranges 提供了 fold_left/right 算法
- 这里的 fold left/right 和不定模板参数的 fold 方向是一致的

**C++ Weekly - Ep 358 - C23's #embed and C++23's #warning** https://www.youtube.com/watch?v=ibKnNRAq5UY

- `#embed` 这个可以内嵌外部资源文件了，甚至可以直接读 `/dev/random` 而且可以用 `limit(n)` 来限制长度
- `#warning` 这个相对 minor，各家编译器的 pragma 转正的 feature

**C++ Weekly - Ep 357 - typename VS class In Templates** https://www.youtube.com/watch?v=86Pa973BW4Y

- 其实没区别，甚至 Jason 里面提的那个 template template 的情况，C++17开始也不需要了

**C++ Weekly - Ep 356 - The Python Enabled Calculators of 2022** https://www.youtube.com/watch?v=82v0jYGh0p0

- 介绍（US）可以买到的支持 Python 的计算器

**C++ Weekly - Ep 355 - 3 Steps For Safer C++** https://www.youtube.com/watch?v=dSYFm65KcYo

- 有理，接地气
- Step 0：Tests + CI
- Step 1：Basic static analayiss
- Step 2：Use sanitizers
- Step 3：Enable fuzzing

**malloc() and free() are a bad API** https://www.foonathan.net/2022/08/malloc-interface/

- 作者认为 malloc/free 接口设计的很差，并且提出了几个差的地方，alignment 和 wasted space 在 low-level dev 中确实有点棘手
- 然后是他自己认为比较好的接口的spec；C++这方面有一些 proposal 在改进 operator  new / delete 因为也是有类似的效果，虽然 customization 的重载有一些改进，但是作者认为值得做更多的改进

**How can I choose a different C++ constructor at runtime?** https://devblogs.microsoft.com/oldnewthing/20250306-00/?p=110942

- 答案是利用 static build function 返回对象，并且利用 copy elision 效果和 in-place construction 一致
- 一个 bonus 是可以利用 immediately evaluated lambda 来做同样的事情
- 之所以 ternary operator 不行是因为这个 context 下 copy elision 无法作用

\#2

http-router 的 add_route 的非 catch-all 部分都写完了，剩下的 catch-all 下周抽点时间应该就能写完。

之后会先把 add_route 相关的 tests 覆盖一下，应该有各种 conflicts 相关的 cornor tests 需要覆盖

刚好下周五一假期，应该会有不少时间可以做这个。

---

这周就是这样，下周见。
