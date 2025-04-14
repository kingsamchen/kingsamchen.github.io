---
title: 一周杂记 in Week 2 Apr 2025
categories: CODE-LIFE
date: 2025-04-13 23:46:36
tags: [杂记]
---
本周（04/07 ~ 04/13）是四月份第二周，这周除了周六天气反常之外其他时候都是多数晴天。

## Life

\#1

清明的时候在小区边上的猫猫活动点发现了一只头上有灰条的田园猫，看起来至少一岁了，是个成年猫。

比较亲人，能接受触摸，看起来像是被遗弃的，原本打算这周周末带去医院绝育。

但是没想到周四晚上照常给喵子们放粮的时候意外发现这只猫躺在小蛋的窝里，但是状态特别差，口鼻都有分泌物而且似乎在渗血，还能闻到一股化脓的味道。

于是立马回家拿了航空箱，费劲巴拉把小家伙从猫窝里掏出来，装进航空箱带到了医院。

结果检查下来发现杯状病毒，支原体甚至猫瘟病毒都是阳性的，我当场人就傻了...

当晚入院就开了一个隔离间，并且相关的抗毒抗炎针剂都用上了，只能祈祷小猫自己能挺过去；我还得回去把它碰过的猫窝给处理掉，避免病毒传染给其他小猫。

但是到了周六中午，突然收到医院通知说小猫情况突然恶化，一边哀嚎把头套和输液管都给蹬掉了，而且他们发现后立马抢救，但是最终还是没能抢救回来，小猫回喵星了。

去年救治过的 Lucky 也是在医院第三天就突然恶化回喵星了，被遗弃或者流浪猫得猫瘟实在是太难办了，只能无奈摊手

\#2

周日新的猫窝到了之后放阳台下晒了半天，散散气味。

顺带又买了凉席，最近天热了，猫窝不是很透气，所以垫个凉席会好一些。

不过周日下午去布置的时候没有发现小蛋，只看到了福宝又跑出来放风，以及一只似乎是新来的橘猫。

不过准备离开的时候倒是看到了皮蛋

\#3

周六在盒马买了两块M6-7澳洲进口和牛，不过买的是板腱肉，用平底锅试了一下，感觉整体还行，口感确实比之前买的牛排都要好；不过这个部位的肉确实有点硬

有一说一，这99块钱一块的牛排确实比之前的闻起来都要香。

下回有机会的话试一下这个级别的菲力或者肉眼？🤔

\#4

这周开始看 DC 的企鹅人，观感不错，柯林法瑞尔真的舍得牺牲形象啊

S1 总共8集，估计下周周末前就能看完

## Work

\#1

**CppCon 2022 | Lightning Talk: C++ on Fly - C++ on Jupyter Notebook - Nipun Jindal** https://www.youtube.com/watch?v=MtKdza3RJNM&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=105

- 用 jupyter 配合 cling

**CppCon 2022 | Compilation Speedup Using C++ Modules: A Case Study - Chuanqi Xu - CppCon 2022** https://www.youtube.com/watch?v=0f5N1JKo4D4&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=103

- 阿里云的分享，采用 modules 之后平均会有2-4x的编译速度提升，具体取决于优化级别和头文件必须要使用的程度有关
- 另外他们发现在高优化级别下，尤其是开了LTO，优化需要的时间很容易抵消 modules 带来的增长，这个算是一个 tradeoff 了
- 想一下其实在本地开发中用 O0 关闭 LTO 搭配 modules 应该是很好的选择

**CppCon 2022 | Lightning Talk: Const Mayhem in C++ - Ofek Shilon** https://www.youtube.com/watch?v=cuQKiXZUmyA&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=107

- 前半段都是铺垫，核心是说存在下面这个问题
- 注：代码是可以编译通过的，而且其实行为是UB

```cpp
struct C {
    C();
    int i_;
    int* p_ = &i_;

    void constMethod() const { ++(*p_); }
};

C::C() = default;

int main() {
    const C c;
    c.constMethod();
}
```

**CppNow 2024 | Concept Maps using C++23 Library Tech - Indirection to APIs for a Concept - Steve Downey** https://www.youtube.com/watch?v=H825py1Jgfk&list=PL_AKIMJc4roWVN3DOxmYZVLNaH0pFHts1&index=47

- 太理论抽象了，完全没听懂想说的是啥…

**C++ Weekly - Ep 366 - C++ vs Compiled Python (Codon)** https://www.youtube.com/watch?v=vXahGgWfzcA

- 介绍的 https://github.com/exaloop/codon 这个，可以把 python 代码 AOT 成 native code
- 不过看介绍不是 100% 兼容 cpython，应该是限定场合使用

**C++ Weekly - Ep 365 - Modulo (%): More Complicated Than You Think** https://www.youtube.com/watch?v=xVNYurap-lk

- 取模操作本身具有复杂性，并且涉及到负数的话符号性不同语言也有不同的标准；C++ 默认都是 truncate 模式，也有针对浮点数特别的 rounded 模式
- 具体见 Wiki：https://en.wikipedia.org/wiki/Modulo

**C++ Weekly - Ep 364 - Python-Inspired Function Cache for C++** https://www.youtube.com/watch?v=lHnYSkZ7Cis

- 实现一个 cache util function 来模拟 python 中的 cached decorator
- 其实使用上差距很大的，而且实际上我个人也不太建议这种 implicit cache 的用法，评论区也有人指出不同函数但是签名一致也会被认为一样，而且还有其他类似 thread-safe 的问题
- 不过这个 talk 有个好处是可以学习一下基本的类型处理技巧

**C++ Weekly - Ep 363 - A (Complete?) Guide To C++ Unions** https://www.youtube.com/watch?v=Lu1WsdQOi0E

- 这期比较顶，union 基本点都提到了，并且用 constexpr 包裹上下文来检查一些行为是否触发了UB有点意思
- 因为 union 没有 default member 所以默认下也没有 ctor/dtor 也不会构造内部 non-trivial 对象，所以一个场景是可以用来做 lazy construction（配合 construct_at or placement new）
- 不过就如视频里说的，active member 的切换和 dtor 相关的逻辑很难写对，所以基本上你可能只需要 std::optional 或者 std::variant 而已

**C++ Weekly - Ep 362 - C++ vs Python vs Python (jit) vs Python With C++!** https://www.youtube.com/watch?v=lhqP50YVT-I

- 感觉算是水了一期的视频，benchmark 了一下 cpython, pypy 和 cppyy 的性能差距

**Assessing a read-after free for security implications: The string comparison** https://devblogs.microsoft.com/oldnewthing/20220603-00/?p=106710

- 作者想说的是，有些情况的 use-after-free 并不是 vulnerability

**unordered_multiset’s API affects its big-O** https://quuxplusone.github.io/blog/2022/06/23/unordered-multiset-equal-range/

- 这篇文章有点意思，指出了标准库很多容器因为 API spec 的原因，conforming impl 的性能其实会有坑的
- 举的例子就是 unordered_multiset 的 equal_range 这个 API 导致 insert 会变得很慢，从 average O(1) 直接变成了 O(n)
- 作者自己做了一个clang/libc++ 的 patch 把这个 API 去掉了并且调整了其他接口的实现，在不改变底层数据结构的情况下 insert 有明显的提升，并且提升和复杂度从 O(n) → O(1) 一致
- 其他接口也有常数级别的提升

\#2

这周依然有抽了一些时间继续过 http-router 的代码。

主要的收获就是经过反复的 debugging（有 debugger 的好处），一些之前觉得莫名其妙的逻辑大概有点明白了。

不过不知道这是不是应该吐槽注释没写好 or Golang 的表现力确实欠佳 🤔

---

好了这周就这样，下周见
