---
title: 一周杂记 in Week 3 Apr 2025
categories: CODE-LIFE
date: 2025-04-21 01:01:19
tags: [杂记]
---
本周（04/14 ~ 04/20）是四月份第三周，距离五一假期越来越近了。

## Life

\#1

周三丈母娘来杭州，傍晚到了东站下车，我提前开车到媳妇儿医院等媳妇儿下班，然后一起接上丈母娘在附近的老头儿油爆虾吃了顿晚饭。

讲道理全杭州的老头儿我觉得还是媳妇儿医院对面这家最好吃。

这次点了葱油鸡，意外的好吃，好奇居然之前都没点过。

吃完之后就开车送丈母娘回了媳妇儿家，顺带一起上楼坐了一会儿。

返程的时候为了不堵车绕了远路，整体时间差不多，但是开起来爽一些 🤣

\#2

周六中午吃过饭后带媳妇儿做了产检，这次因为不用抽血做生化，所以下午去就行。

下午人不多，整体流程很快，不过因为要做胎心监护，所以整体算下来也花了差不多50多分钟。

检查结果依然是一切正常，让人放心了不少。

之后去了西溪银泰的迪卡侬和优衣库买两个人的衣裤。

迪卡侬有个休闲短裤意外的觉得舒服，而且感觉不是特别剧烈的运动的话应该也不会太拖后腿，直接种草了。

顺带买了迪卡侬家出的补水饮品，到家后发现其实是委托今麦郎代工的 🤔

媳妇儿在迪卡侬没有买到合适的裤子，不过倒是在优衣库选到了

\#3

这周看完了企鹅人S1

- **企鹅人 The Penguin** 4/5 比起来编剧是这个剧最大的短板，感觉好几处比较生硬的机械降神式的处理。科林法瑞尔演的是真的好，一个唯利是图的畜生真给演活了，最后为了不让自己有软肋勒死 victor那段真的神

## Work

\#1

**CppCon 2022 | Undefined Behavior in the STL - Sandor Dargo** https://www.youtube.com/watch?v=fp45k9gsnUo&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=104

- 一开始以为是 STL 中那些会导致UB的设计问题，后来发现是STL中一些不正确操作导致的UB
- 但是整体就没有那么让人觉得惊喜

**CppCon 2022 | C++ Performance Portability - A Decade of Lessons Learned - Christian Trott** https://www.youtube.com/watch?v=jNGGKFkt4lA&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=106

- 讲的 GPU/CUDA 和 kokos 的东西，不懂，基本没听懂

**CppNow 2024 | Closing Keynote: C++ Development Tools: Past, Present and Future - Marshall Clow** https://www.youtube.com/watch?v=-Yy5T_P50iU&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=54

- 回顾了一下这么多年来周边工具的发展，以及展望了一下未来的发展
- AI 的应用应该会慢慢渗透到越来越多的地方

**C++ Weekly - Ep 475 - Lambdas On The Heap?** https://www.youtube.com/watch?v=W4G2xJX9Gnw

- Lambda 本质上也是一个 function object 所以语法上也可以把他创建在 heap 上

    ```cpp
        auto p = new decltype([]{
            return 42;
        });
        (*p)();
    ```

- 但是如果这个 lambda 有 capture 那么上面代码会因为没有 default constructor 失败，Jason 演示了一种 trick 可以 workaround 但是其实意义不大
- 整个做法来说其实都意义不大，如果真的因为 lambda capture 的东西会比较大，直接 explicitly use function object 就行了

**C++ Weekly - Ep 474 - What Can LEA Do For You?** https://www.youtube.com/watch?v=Ac7cdsIbjE0

- 讲的 `LEA` 指令的常用技巧
- `LEA REG [EXP]` 可以在 EXP 里直接做四则运算，然后把结果存到 REG 里

**C++ Weekly - Ep 361 - Is A Better `main` Possible?** https://www.youtube.com/watch?v=zCzD9uSDI8c

- 尝试用 `std::span<const std::string_view>` 来取代 main 入口函数的那坨参数
- 我觉得意义不大吧，一般真要 parsing args 都是直接上 lib 的

**C++ Weekly - Ep 360 - Scripting C++ Inside Python With cppyy** https://www.youtube.com/watch?v=TL83P77vZ1k

- 介绍的 cppyy 这个可以直接 python 里 include cpp file 并且还有运行时交互

**C++ Weekly - Ep 359 - std::array's Implementation Secret (That You Can't Use!)** https://www.youtube.com/watch?v=uLbv2u536G0&t=5s

- 示范了一下如何实现（平替）std::array

**Looking at assembly code with gdb** https://lemire.me/blog/2022/06/28/looking-at-assembly-code-with-gdb/

- 以为是看反汇编的教程结果发现不是
- 文章核心是利用 gdb 的反汇编来看代码是否有改进的地方，有两个技巧可以学习
    - `set print asm-demangle` 在输出反汇编的时候对函数做 demangle
    - `info functions validate_utf8` 搜索所有匹配/包含的函数名字和地址等信息

**void versus [[noreturn]]** https://quuxplusone.github.io/blog/2022/06/29/that-undiscovered-country/

- `[[noreturn]]` 标记的函数压根不会返回，可能因为 abort / throw 什么的直接就退出了；这个标记可以让 `int f()` 函数内部调用一个 `[[noreturn]] g()` 并且没有任何返回时不要 complain
- 另外 attribute 不参与 type system，作者认为这是一个潜在的坑

**Formatting Custom types with std::format from C++20** https://www.cppstories.com/2022/custom-stdformat-cpp20/

- 看完终于明白 fmt 这套自定义格式化咋用了…为了做到 full control of parsing & formatting 这个门槛确实有点高啊…
- 如果接受一些性能损失，看起来直接继承自 `formatter<string_view>` 然后 `format()` 内部用一个 std::string 把格式化做好最后调用 `formatter<string_view>::format()` 看起来最省事 🤔

**indirect_value: A Free-Store-Allocated Value Type For C++** https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2020/p1950r1.html

- C++ 26 的这个 https://en.cppreference.com/w/cpp/memory/indirect 的部分初始提案
- 区别在于这个 proposal 是直接冲着 non-dynamic allocation 去的，而标准的这个做法看起来是想引入 allocator 来解决
- 最终用法上都差不多，想象一下现成的 pimpl 类型

\#2

这周把 http-router 的 add router 部分代码都看完了并且大致弄明白了，insert child 除了 catch-all 的之外也都看完了。

后续可以先把 router with paramters 的部分先“复刻”一下，毕竟这部分用的比较多。

Golang 的代码虽然表达力不行有时候略显罗嗦，但是有些 trick 确实挺神奇的，比如执行 method 期间更换 receiver/this 来避免递归调用子对象的同个方法...

---

好了这周就这样，下周见
