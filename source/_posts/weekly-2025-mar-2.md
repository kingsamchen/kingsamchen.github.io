---
title: 一周杂记 in Week 2 Mar 2025
categories: CODE-LIFE
date: 2025-03-18 01:53:04
tags: [杂记]
---
本周（03/10 ~ 03/16）是三月份第二周，这周事情也比较多

## Life

\#1

这周二乐刻的跑步机网络又出问题了，真的服了。没办法跑到城西银泰的乐刻跑步。

一开始纳闷这家乐刻选址牛逼啊，直接在银泰写字楼，进去后才发现，woc，就这么一点点空间，而且晚上7点多，空气里弥漫着鞋臭味，狐臭味汗臭味混合的味道...

而且他家的跑步机感觉不是水平，也有一个倾角，所以跑久了也觉得不舒服

下次不会再来这家了。

周六虽然下着小雨，试探性地发现家边上的那家乐刻精品馆的网络修好了，所以周六就在附近跑了。

因为中间少了一次，所以这次直接跑了十多公里，试图弥补一下中间的 gap 🤡

\#2

我妈周三来杭州，约了周四的口腔医院看牙齿。

周四看完之后说周五老家有人去世了需要回去，所以买了周五的票就回去了。

不过说是再过两周还要再来一次杭州做个牙套啥的

\#3

周日开车陪媳妇儿和丈母娘考察了市妇保钱江院区和萧山医院的月子中心。

选这两家是因为这两家的月子中心是医院自营的，除了阿姨是外聘之外医生护士都是本院团队。

考察完之后我自己对两家环境和提供的服务都是满意的，但是最终决定权得媳妇儿来定...

反正希望她别最后哪家都不去折腾我们几个人 🤷‍♂️


## Work

\#1

**Memory consumption, dataset size and performance: how does it all relate?** https://johnysswlab.com/memory-consumption-dataset-size-and-performance-how-does-it-all-relate/

- 对于 memory bound 的程序来说减少内存占用可以提升性能
- 然后是一些减少内存占用的 tips/tricks

**CppNow 2024 | What Does It Take to Implement the C++ Standard Library? (C++Now Edition) Christopher Di Bella** https://www.youtube.com/watch?v=bXlm3taD6lw&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=42

- 基本是听故事，标准库开发者分享自己的心路历程
- 另外学到一个，用 libstdc++ 的话需要额外定义 `-D_GLIBCXX_SANITIZE_VECTOR`

**C++ Weekly - Ep 470 - requires Clause vs requires Expression?** https://www.youtube.com/watch?v=k1I4ZoB50_I

- 这一期干货很多，感觉一下就 get 到核心点了
- `requires expression` 核心就是判断一段代码能否正常编译，然后返回一个 boolean；因为他是返回一个 boolean 所以实际上可以党做运行时代码用

    ```cpp
    int main() {
        return requires(int i) { ++i; };
    }
    ```

    因为本身是合法代码所以自然返回 true；但是如果把 `int i` 换成 `cosnt int i` 虽然 requires expression 能判断出是 invalid 但是我们自己也没法编译通过了；解决方案/正确的姿势是把 requires expression 放到 template context 里去

- 然后就有

    ```cpp
    template<typename T>
    constexpr bool incr_able() {
        return requires(T i) { ++i; };
    }

    static_assert(incr_able<int>());       // ok
    static_assert(incr_able<const int>()); // fail
    ```

- `requires clause` 就是需要一个 compile-time boolean，不管你这个 boolean 是怎么来的，你甚至可以直接扔一个 `true/false` literal 上去

**C++ Weekly - Ep 383 - C++ Cross Training** https://www.youtube.com/watch?v=9RxPRr-fk7Q

- 这一期讲的是 cross training 有助于理解和提升整体编程能力
- 可以听听，顺带练练听力

**C++ Weekly - Ep 382 - The Static Initialization Order Fiasco and C++20's constinit** https://www.youtube.com/watch?v=rEwijXgC_Kg

- “constinit doesn’t change the meaning of your code, it validates your assumptions”
- 用这个来保证标记的 static/thread storage duration（包含全局/静态/tls）的变量能够在 compile-time 完成初始化
- 防止 static initialization order fiasco 的做法我觉得是直接关闭了 runtime initialization，所以适用场景还真的得看

**C++ Weekly - Ep 381 - C++23's basic_string::resize_and_overwrite** https://www.youtube.com/watch?v=Ymm0yN_QUQA

- 现在的 resize 要么用默认值填充要么用给定的 char 填充，效率有点问题，并且不需要填充的时候 overhead 有去不掉，所以才引入这个新函数
- episode 里面展示的是自定义 overwrite 的操作，如果只想当做一个 buffer 做 uninit-resize，可以这么用

    ```cpp
        std::string str = "hello";

        // 调整大小但不修改内容
        str.resize_and_overwrite(10, [](char* buf, std::size_t buf_size) {
            return buf_size; // 返回缓冲区大小，表示使用全部空间
        });

        std::cout << str << std::endl; // 输出: hello（后面可能有未初始化的字符）
    ```


**C++ Weekly - Ep 380 - What Are std::ref and std::cref and When Should You Use Them?** https://www.youtube.com/watch?v=YxSg_Gzm-VQ

- std::ref/cref 的主要适用场景，目前还是对 `std::bind()` 这种默认采用 pass-by-value 的调用进行 pass-by-ref 折衷的处理
- 不过 std::reference_wrapper 的使用场景还是不少的，比如搭配 optional/variant 这种一起用
- reference_wrapper 的好处是可以坚持使用 value semantics 同时带有 ref 语义；原生的引用有时候的效果是反直觉的（之前有周记里有提到）

\#2

这周又又又一次踩了 apache httpd 的坑。

我们好几个 service 都是编译成 so 在local vm里是由一个 httpd 加载的，然后 service-a 和 service-b 都编译链接了 service-b 的 http-rpc structs/client（header only）

然后我往 service-b 的 http-rpc 某个 resp 里加了一个字段 F，但是测试时候发现请求这个 api 返回的 response 里面死活没有 F

最后排查了很久才发现因为没有把 service-a 也编译链接，导致序列化用的函数跳到了 service-a 里的那个版本...

实际上这个是违反 odr-violation 的，但是动态库这个本来就是法外狂徒

也就是因为摊上了一个挫逼老登 head，2020年起的项目特么整个技术栈还用的是2000年的，真的服了

\#3

这周学会了 fmtlib 如何打印指针 - - 🤣

fmtlib 打印指针需要用 `fmt::ptr()` 才行

```cpp
int i = 5;
auto pi = &i;
fmt::println("{}", fmt::ptr(pi));
```

不用会直接编译失败

这应该是从类型系统上做的 enhancement

---

好了这周就这样，下周见
