---
title: 编译期判断是否存在某个成员函数
categories: PROGRAMMING
date: 2017-01-30 00:32:42
tags: [c++, SFINAE, template]
---
之前在[某篇](https://kingsamchen.github.io/2016/06/14/template-type-constraints-and-type-traits/)文章描述了一个实际上不是那么好用的检查某个模板类型参数是否存在某个函数的方法。

这次会介绍一个相对有用的，在编译期检查某个类是否存在给定成员函数的做法，并且可以根据检查的结果执行不同的代码。

（简而言之就是如何拙劣的模拟一下 duck typing）

假设我们要检查某个类 `class Foo` 是否存在成员数 `bar(int)`

实现辅助设施

```c++
template<typename T, typename... Args>
struct has_member_bar {
    template<typename U>
    constexpr static auto check(const void*)->
        decltype(std::declval<U>().bar(std::declval<Args>()...), std::true_type());

    template<typename U>
    constexpr static std::false_type check(...);

    static constexpr bool value = decltype(check<T>(nullptr))::value;
}
```

检查的方法是尝试生成函数 `T::bar` 的返回类型，如果不存在相应函数，则 `check()` 的重载决议会匹配到第二个函数上。

这里引入 variadic template 的原因是我们希望能够检查时同时确定函数的 signature（注意 signature 是不包含返回类型的）。

使用方法：

```c++
constexpr bool has_bar = has_member_bar<Foo, int>::value;
```

如果想把这部分做成通用的设施，那只能引入宏，根据需要生成辅助结构的代码；因为一个辅助结构只能检查一个函数成员。

```c++
#define DECLARE_HAS_CLASS_MEMBER(NAME)                                                  \
template<typename T, typename... Args>                                                  \
struct has_member_##NAME {                                                              \
    template<typename U>                                                                \
    constexpr static auto check(const void*)->                                          \
        decltype(std::declval<U>().NAME(std::declval<Args>()...), std::true_type());    \
                                                                                        \
    template<typename U>                                                                \
    constexpr static std::false_type check(...);                                        \
                                                                                        \
    static constexpr bool value = decltype(check<T>(nullptr))::value;                   \
}

#define HAS_CLASS_MEMBER(CLASS, MEMBER, ...) \
    has_member_##MEMBER<CLASS, __VA_ARGS__>::value
```

使用前需要先用 `DECLARE_HAS_CLASS_MEMBER(class_name)` 来创建一个辅助结构，再利用 `HAS_CLASS_MEMBER(Foo, bar, int)` 来判断是否存在 `Foo::bar(int)`。

如果希望能利用上面的设施在编译期做到根据函数是否存在执行不同的操作，那么则可以借助 SFINAE 来实现：

```c++
DECLARE_HAS_CLASS_MEMBER(foo);
DECLARE_HAS_CLASS_MEMBER(bar);

struct Foo {
    void foo() {}
};

struct Bar {
    void bar() { std::cout << "Bar::bar()\n"; }
    void foo(const std::string&, int) { std::cout << "Bar::foo(const std::string&, int)\n"; }
};

template<typename T>
typename std::enable_if<HAS_CLASS_MEMBER(T, foo, const std::string&, int)>::type RunFoo(T& obj)
{
    obj.foo("hello world", 1);
}

template<typename T>
typename std::enable_if<!HAS_CLASS_MEMBER(T, foo, const std::string&, int)>::type RunFoo(T& obj)
{
    obj.bar();
}

int main()
{
    Bar bar;
    RunFoo(bar);
    return 0;
}
```

但是如果想将 SFINAE 这部分做成通用的 static-if，还是比较 tricky 的；我一开始利用 lambda + SFINAE 倒是模拟了一个，但是因为 lambda 直接被无条件编译，导致即使在编译期已经确定不用某个分支的 lambda，仍然会编译这个 lambda 的代码，使得 static-if 功能大减。

我猜想使用 generic lambda 应该可以绕过去，但是实现上可能会更加 tricky，并且使用上不会太直观，或者满地坑。

其实 `HAS_CLASS_MEMBER` 最好的作用是等到 C++ 17 普及了 if-constexpr，这样整个流程都变得清晰了。

另外有一点不得不说，其实很多你认为需要通过编译期判断是否存在某个成员函数才能解决的问题，压根就走不到需要自己实现 SFINAE 那一步，通过一些（可能不是那么优雅）简单的构造，就可以利用 type traits 解决，例如，我在实现 [KBase Singleton](https://github.com/kingsamchen/KBase/blob/master/src/kbase/singleton.h) 的方案。