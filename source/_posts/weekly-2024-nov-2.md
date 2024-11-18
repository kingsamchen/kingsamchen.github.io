---
title: 一周杂记 in Week 2 Nov 2024
categories: CODE-LIFE
date: 2024-11-18 21:38:32
tags: [杂记]
---
本周 (11/11 ~ 11/17) 是11月的第二周。

## Life

\#1

这周又到孕检的时候了，周二上午快到中午的时候开车去媳妇儿医院先接媳妇儿下班；中午吃完饭休息的差不多之后先开车到了市妇保做了最基本的检查，然后到了隔壁的社区医院，做了孕妇建档。

这建档是真的挺花时间的，感觉各种信息登记啥的搞下来弄了快两个小时。

因为是下午去的，所以没法做血液生化检查，于是周六一大早又到了社区，抽血做生化。

期间媳妇儿说这几天腿走路会有疼痛感，但是她自己感觉不是血栓或者水肿，疑似缺钙，所以生化检查刚好查一下血钙。

到了周日晚上检查结果全部出炉，也确认了腿部疼痛是因为缺钙 🤣

然后我就给媳妇儿提了每天至少喝两杯鲜牛奶的要求，毕竟家里牛奶管够啊，比额外吃钙片要简单多了；善存不能吃太多，否则伤肝。

另外为了解决媳妇儿偶尔会有低血糖的情况，我买了一箱李子园甜牛奶和一箱有糖的可口可乐，as requested by her 😊

\#2

之前说小区边上有好心人给两只流浪小狸花搭了窝还定期投喂。

这两只小狸花很亲人，而且看年纪也有4-5个月了，我想了一下觉得应该抽个时间带他们去绝育。

刚好这周他们住的位置边上的栅栏要重新刷漆，导致周围都是很浓重的油漆味，而且工事的东西都直接堆在他们窝边上了，所以我想干脆直接这周就带他们去医院做个检查，把绝育做了，就当是避难度假了。

两只猫身体状况都不错，这里给中华田园猫点个赞，手术很快就安排了。而且因为是公猫，所以风险相对小一些，恢复得也快。

后面几天去看他们的时候都已经恢复到基本正常了，又开始变得能吃起来，除了肠胃可能还有点点脆弱，会有稀软便。

两只猫预计下周二就可以恢复完全了，所以最近要给他们找领养了。如果实在找不到靠谱的领养人，可能就放他们回之前的那块位置。毕竟照顾他们的好心人还是很多的。

\#3

周一约了 chao 和 perfect 一起去西溪博纳看了《哈利波特与凤凰社》的重映，影院效果是真的不错啊。

除此外

- **新铁血战士 Predators** 3/5 完全不如州长那部有趣，大多数角色都没啥弧线，反而日本杀手和变态医生算是为数不多的亮点。另外一个亮点大概是引入了铁血战士有多个氏族的概念
- **枯叶 Kuolleet lehdet** 3.5/5 北欧版的爱情神话吗，整体有点俗套，但是有些片段笑梗还挺多的，不知道是不是北欧的人都是这种一脸正经的搞笑

## Work

\#1

**C++ Templates 2nd | 20. Overloading on Type Properties**

- 用 function overload，e.g. tag dispatching, SFINAE，和 class specialization 来实现一些 TMP 策略
- if-constexpr 在某些场合效果拔群

**CppCon 2022 | What's New in Conan 2.0 C/C++ Package Manager - Diego Rodriguez-Losada** https://www.youtube.com/watch?v=NM-xp3tob2Q&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=60

- 介绍的 conan 2 的新改进
- 但是感觉演讲的有点问题，我全程都没get到核心点…

**CppCon 2022 | Graph Algorithms and Data Structures in C++20 - Phil Ratzloff & Andrew Lumsdaine** https://www.youtube.com/watch?v=jCnBFjkVuN0&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=58

- 介绍 std::graph 的提案；现成实现可以参考 boost 的那个
- 不过工作上目前基本没用到 graph 数据类型，所以当英语听力了

**CppCon 2022 | Lightning Talk: C++20 - A New Way of Meta-Programming? - Kris Jusiak** https://www.youtube.com/watch?v=zRYlQGMdISI&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=61

- quick overview 一些 C++ 20 的新特性有助于简化 meta-programming
    - concepts 来实现 introspection
    - Immediately-Invoked Lambda Expression 配合 lambda template argument 实现 manual unrolling
    - fixed string 编译期的字符串
    - if constexpr + concepts 可以简单地实现 to_tuple 把 struct members 打包成 tuple
    - constexpr vector

**C++ Weekly - Ep 454 - std::apply vs std::invoke (and how they work!)** https://www.youtube.com/watch?v=DxFpQa1PyaA

- `std::invoke` 是 generic 的函数调用+指定参数；`std::apply` 则是参数要以 tuple pack 形式提供
- 还顺带讲了一下二者的内部实现

**C++ Weekly - Ep 439 - mutable (And Why To Avoid It)** https://www.youtube.com/watch?v=CagZYOdxYcA

- mutable 破坏了 const member function 的 immutable 假设，会让人误以为这个函数是 thread-safe 但是实际上不是
- 经验上，mutable 应该只用于修饰 mutex 这种成员

**C++ Weekly - Ep 438 - C++23's ranged-for Fixes** https://www.youtube.com/watch?v=G6FTtZCtFXU

```cpp
for (const auto& v : foo().bar()) {
  // use v will cause lifetime issue
}
```

- 这种用法都是有问题的，因为 foo() 的临时变量后面就自动析构了，算是一类比较经典的 issue pattern 了
- C++ 23 通过专门延长 range-for-loop 中临时变量的生命周期来解决这个问题

**Loop Optimizations: how does the compiler do it?** https://johnysswlab.com/loop-optimizations-how-does-the-compiler-do-it/

- 讲了非常多当前 modern compiler 对于 loop 的一些优化方案
- 另外，现在已经不是很推荐 manual loop unrolling 了

**Loop Optimizations: taking matters into your hands** https://johnysswlab.com/loop-optimizations-taking-matters-into-your-hands/

- 两个影响编译器优化最大的拦路虎：函数调用 和 指针别名
- 函数调用如果不能被 inline，导致编译器会丢失很多上下文信息，那么会对待优化的代码趋于保守；尤其函数调用还涉及到全局变量使用的时候
- pointer aliasing 的问题类似，会让编译器在不确定的情况下趋于保守
- 然后还提了一些其他的 loop optimization 上的小 tips

**Coercing deep const-ness** https://brevzin.github.io/c++/2021/09/10/deep-const/

- 对于 `template<typename T> void foo(std::span<const T>)` 应该如何支持 vector<int> 这种类型
- 作者自己有个提案，似乎被加入进了 C++ 23；不过在有解决方案前作者给了一个 workaround
    
    ```cpp
    template <range R>
    void takes_any_range2(R&& _r) {
        auto r = views::as_const(_r);
    
        // r is definitely a constant range
        // never use _r again
    }
    ```
    

**Tutorial: the CRTP Interface Technique** https://www.foonathan.net/2021/10/crtp-interface/

- 介绍 CRTP；这篇算是比较核心的指出 CRTP 的目的是 share impl boilerplate
- 另外要注意，base class 实现的时候，只有在 member function 中 Derived template argument 是 complete 的，即
    
    ```cpp
    template <typename Derived>
    class forward_iterator_interface
    {
    public:
        // Error: use of incomplete type `Derived`.
        using reference = const typename Derived::value_type&;
    
        // Error: use of incomplete type `Derived`.
        typename Derived::pointer operator->() const
        {
            auto& derived = static_cast<const Derived&>(*this);
            // OK, inside the body `Derived` is complete.
            typename Derived::pointer ptr = &*derived;
            return ptr;
        }
    };
    ```
    
    如果需要在成员函数外访问 `Derived` 的类型成员，则需要通过额外的模板参数的方式注入
    
\#2

这周事情比较多，有点松懈，没怎么写代码。下周加油 🫡

---

好了这周就这样，下周见
