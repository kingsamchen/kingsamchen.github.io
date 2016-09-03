---
title: C++ is fine, it's you that suck
categories: PROGRAMMING
date: 2016-09-03 17:10:39
tags: [rant, c++]
---
这篇主要内容是吐槽，干货不多。

曾经我以为只要是我设计的代码结构里，基本不会碰到需要 multiple inheritance 的情况，更不用说需要 virtual inheritance，结果没想到这周就被啪啪啪打脸了。

起因在于我要为底层的 obs properties 体系设计一个 wrapper，为上层提供类似的等价物。

properties 描述了一个 obs source 所具备的特性集合，不同的 obs source 拥有不同的 properties；而 properties 实质上又是一个由众多不同类型的 property 构成的集合，因此不同类型的 property 才是操作的基本单位。

一个 source 的 properties 可能实际上包含：

- 描述数值类型的 int-property or float-property
- 描述文字信息的 text-property
- 描述一系列子属性的 list-property
- etc.

但是蛋疼的地方在于，在 obs core 这一层，逻辑上不同类型的 property 却具有相同类型的数据类型，都由 `obs_property_t` 来表示；

于是，除了通吃的诸如 `get_name()`, `get_description()` 操作之外，obs 还提供了返回具体类型的函数接口，以及分别针对某个类型的一组操作函数接口。

考虑到几个原则

- proxy layer 本身是一个屏蔽底层细节的模块，只能在边界暴露必要的接口
- properties 要反映出是 property 的序列容器（能够便利定位到某个 property）
- property 无论是共有的操作还是特定的操作都要暴露给外界，并且要屏蔽底层实现 property 的细节

最适合我的实现反而是拥有如下两条 hierarchical path 的多继承设计

```
                        Property [pure]
                        /        \
                       /          \
       SpecificProperty[pure]     PropertyImpl [实现基础操作，兼容 properties 的存储]
                       \          /
                        \        /
                    SpecificPropertyImpl
```

左侧两个基类都是 pure abstract class，负责对外暴露接口，右侧的 impl class 是在 proxy layer 中的实际类型。

由于考虑到 *properties contains a series of property* 的事实，`PropertyImpl` 实际上对外表现出值类型语义（嗯哼，我才不会说我硬生生的在另外的 `properties` wrapper 上给加上了迭代器支持...）。

上面继承出现了菱形继承，于是 virtual inheritance 就进来了，不然 `Property` 的析构函数就有两份，并且还会执行两次。

考虑上层在某些时候需要从 `Property` 获得更为具体的 `SpecificProperty`，以执行一些特定的属性操作，这个时候就需要利用 `dynamic_cast` 做一些 down-cast。

然而.....这是不可能的，因为整个程序是基于 chromium 的框架开发，而 chromium 强制把 RTTI 关闭了...

于是一个晚上加上一个白天，最后终于想出一个 workaround 给绕了过去...而在构想最终的 workaround 期间，我甚至有冲动要自己艹 memory layout 来计算偏移...

关闭 RTTI 是一个非常具有代表性的行为，和多重继承，虚继承类似，很多人认为这些东西是非常 evil 的；因为他们不光带来了性能开销，并且实际作用都可以藉由各种 workaround 实现。于是先把你的设计批判一番，再接着滔滔不绝的给你讲解各种可能可以用得上的 idioms...

然而，工程设计是非常 non-trivial 的，现实世界是非常 cruel 的，你永远不知道你面临的会是什么问题，尤其是遇到你的 code base 中存在来源于其他不受控制的代码的时候。

或许是对这种人已经感到了厌倦，甚至是无语，Bjarne Stroustrup 特意在他的 FAQ 里提到了这么两点：

- language-supported inheritance is typically superior to workarounds for ease of programming, for detecting logical problems, for maintainability, and often for performance [FAQ](https://isocpp.org/wiki/faq/multiple-inheritance#mi-needed)
- It *really* bothers me when people think they know what’s best for *your* problem even though they’ve never seen *your* problem!! How can anybody possibly know that multiple inheritance won’t help *you* accomplish *your* goals without knowing *your* goals?!?!?!?!!! [FAQ](https://isocpp.org/wiki/faq/multiple-inheritance#mi-not-evil)


C++ 之所以发展成今天这样，成为一个 multi-paradigm 的语言，本就来源于 BS 认为世界太过 undeterministic，依靠单一的范式很难精准有效的解决大部分问题。

回到前面 RTTI 的问题，十来年前，各大主流编译器 （MSVC, GCC 等）是默认关闭 RTTI 的，理由是 for better performance；而现在，RTTI 的是默认打开的，这至少意味着主流编译器认为 RTTI 所带来的 side-effect penalty 已经是 negligible。

打开 RTTI 的副作用是什么呢？所有 **polymorphic class** 的 vtable 多了一个 slot 专门存放类型信息。

*If you don't want to use a feature, just don't use it*

相较之下，禁用异常就真的是非常不理智的行为了。

更重要的，不要因为存在某个 feature 就不明真相的乱用，掉坑里了又愤愤不平的搞个大新闻，四处宣扬这个 feature 简直是 shit，并且 C++ 是 a pile of shit。

**_Remember, C++ is fine, it is you that suck_**

### Bonus

最后贴两个 presentation，分别是 Bjarne Stroustrup 和 Herb Sutter 在 CppCon 2015 上的分享。

- [Bjarne Stroustrup: Writing Good C++ 14](https://www.youtube.com/watch?v=1OEu9C51K2A)
- [Herb Sutter: Writing Good C++ 14 By Default](https://www.youtube.com/watch?v=hEx5DNLWGgA)

作为开篇分享，这两个 talks 应该说是为 write moder c++ programs 奠定了一个基调，里面对 resource ownership, type safety, data-safety 都做了非常精彩的叙述，并且还联手提炼了一个 C++ Core Guidelines 出来。

当然里面也有各种私货，例如 BS 吐槽 xx coding style，BS 推销 VS 的静态分析功能...