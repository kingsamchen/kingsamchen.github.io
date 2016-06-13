---
title: Template Type Constraints And Type Traits
date: 2016-06-14 00:32:39
categories: PROGRAMMING
tags: [c++, template]
---
模板参数类型约束在 C++ 中一直以来大概得算不上不下的一个处境。

因为非运行时（编译期）的优势，对于参数类型约束的需求不像 C# 那样紧迫；但是同时又因为实例化完成的时期过早，编译器对于模板代码的处理并不能上升到一个精确的语义层次，导致的后果就是模板相关的代码的出错信息一直在井喷，而且相当一部分的错误信息完全没有卵用。

最直接的例子就是拿 `std::sort` 去对一个 `std::list` 对象进行排序。

早期的编译器扔出来的错误提示第一眼完全看不出再讲什么（例如找不到 `operator<`的定义）；虽然经验丰富的老司机们一下就能看出问题在于 `std::sort` 要求的迭代器是 *random access iterator*, 而 `std::list` 提供的迭代器是 *bidirectional iterator*，但是原因八成是因为老司机们撸过的轮子比你用过的库还多。

这个问题对于 library developer 尤为尖锐。

按照原本的历史进程，解决这个大麻烦的荣耀应属 C++17 中的 [Concepts](http://en.cppreference.com/w/cpp/language/constraints)；然而不知道标准委员会的委员们哪根神经回路短路了，居然把这个 proposal 给 veto/reject 了，于是之前说好的大救星就这么突然被打脸了。

## Type Constraints

在很久很久以前，甚至比提出 concepts 这个想法还要早，曾经有一批人开始尝试自己加一些类型约束设施，已达到类似的要求。

在 Herb Sutter 著名的 *More Exceptional C++* 一书中，提供了这么一个例子：

假设我有一个类模板

```c++
template<typename T>
class Foo { ... };
```

要求 `Foo` 的实例化类型参数 `T` 必须包含一个成员函数 `Clone()`，并且这个函数的原型要**严格满足** `T* Clone() const`。

做法是提供一个类型约束类 `HasClone`

```c++
template<typename T>
class HasClone {
public:
    HasClone()
    {
        void (*pfn)() = &ValidateConstraints;
    }

    ~HasClone() = default;

    DISALLOW_COPY(HasClone);

    DISALLOW_MOVE(HasClone);

    static void ValidateConstraints()
    {
        T* (T::*fn)() const = &T::Clone;
        UNREFED_VAR(fn);
    }
};
```

并利用父类构造函数先于子类执行的特性

```c++
template<typename T>
class Foo : HasClone<T> {
    ...
};
```

对模板参数 `T` 进行约束。

虽然在形式上这个构造法非常漂亮，然而在实践中，根本称不上好用。

某些编译器，例如 VS 2015，在显式调用 `T::Clone()` 失败时会直接跳过 `HasClone` 的构造，直接提示目标调用点相关的错误。

这意味着精心设计的类型约束类直接变成了 subordinate solution。

## Type Traits

另一方面：既然不方便改善类型约束，那么可以考虑在编译期利用类型信息针对不同的类选择不同的行为。

于是这个操作通常需要两部分协助： type traits 和 template specialization。

举个很常见的误用 OOP 的例子：有两个类型 A 和 B，如果 B 是 A 的子类，那么进行操作1，否则进行操作2。

有些人会利用 runtime type information 的方式实现，显著特征是要做 down-cast，例如 `dynamic_cast` 或者 Java 中的 `instanceof`，然而他们不知道这是 runtime polymorphism 的典型场景。

不过在 C++ 中，借助 type traits，可以实现 compile time polymorphism。

这里我们需要一个能够编译期判断继承关系的设施，假设它是一个名为 `IsDerivedDrom` 的类。

同样来自 *More Exceptional C++*，这个实现来自脑洞炸开的 Andrei Alexandrescu：

```c++
template<typename D, typename B>
class IsDerivedFrom {
private:
    struct No {};
    struct Yes { No data[2]; };

    static No Check(...);
    static Yes Check(B*);

public:
    enum {
        value = sizeof(Check(static_cast<D*>(0))) == sizeof(Yes)
    }
};
```

这个实现有多精巧呢？

1. 继承关系通过非常自然的 implicit upcast。如果 `D` 是 `B` 的子类，那么 `D*` 能够安全顺滑的转换为 `B*`
2. 利用函数重载来实现编译期绑定，并且利用了不定参数形式在 overload resolution 中的 low priority
3. sizeof 和 enum 的编译期求值行为
4. 没有任何运行时开销

从头到脚的 compile-compiliant 的处理方式，闪耀着老司机们的智慧光芒。

当然到了 C++11，可以把这个形式做更进一步的简化，让直观上更容易理解

```c++
template<typename D, typename B>
class IsDerivedFrom {
private:
    static std::false_type Check(...);
    static std::true_type Check(B*);

public:
    static constexpr const bool value = decltype(Check(static_cast<D*>(nullptr)))::value ;
};
```

至于剩下的怎么做 specialization 这里就不提了，自己翻书去吧。

一个好消息是 C++ 11 开始，STL 内置了一大票和这个类似的 type traits 设施。

在 concepts 短期无望的情况下，利用 type traits 和 specialization/overload resolution 可以做出一些更加灵活的设计，并且依托内置设施，甚至大程度可以避开自己手工跑 SFINAE，减轻生活压力。

大概就这样。

PS：不过其实你最后可以发现，两者前后并没有什么直接的因果关系。主要原因大概还是为了写个 post 而丧心病狂的强行拉亲戚。