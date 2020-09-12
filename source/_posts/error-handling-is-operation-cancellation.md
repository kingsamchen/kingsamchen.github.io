---
title: Error Handling is Operation Cancellation
categories: PROGRAMMING
date: 2020-09-12 20:21:46
tags: [cpp, error handling, exception]
toc: true
---
最近看了[这篇文章](https://akrzemi1.wordpress.com/2019/04/25/handling-errors-is-canceling-operations/)有一种顿悟感，想着写点总结加深理解。

如果嫌原文太长可以直接看这篇总结；不过别人咀嚼过的不一定适合你，所以还是推荐一块把原文也看了😁。

## Operation Cancellation

假设一个函数 `foo()` 中的某个操作发生了错误，并且后续操作**直接或间接地依赖**当前操作的正确行为；那么，不管使用何种错误处理/汇报手段，这里都需要 (1) 中止后续操作并 (2) 向上汇报错误。

这里称这种行为为 *operation cancellation*。
<!-- more -->
例如：

```cpp
void foo()
{
    open_socket();
    if (failed)
        die();

    resolve_host();
    if (failed)
        die();

    connect();
    if (failed)
        die();

    send_data();
    if (failed)
        die();

    receive_data();
    if (failed)
        die();
}
```

- `resolve_host()` 依赖 `open_socket()` 正确执行
- `connect()` 依赖 `resolve_host()` 正确执行
- …

任何一处调用失败都会引发对后续操作的 cancellation。

## Cancellation Cascade

函数 `foo()` 内部发生错误并触发 operation cancellation 后错误会向上传递到调用 `foo()` 的函数 `bar()`，如果 `bar()` 中后续操作也依赖 `foo()` 的正确执行，那么这个错误同样也会引发 `bar()` 的 operation cancellation。

于是 cancellation 会沿着调用栈一路向上传导，直到某个 call site 的后续操作不依赖出错操作的正确执行。

这里称这种链式中止为 *cancellation cascade*.

```cpp
void bar()
{
    foo();          // <- it failed.
    if (failed)
        die();

    baz();
    if (failed)
        die();
}
```

C++ 的 stack unwinding 设计上完美契合 cancellation cascade 的思想，并且能让因果依赖关系变得更加直观和清晰。

## With Resource Management

通常情况下，如果无法成功分配资源（内存资源、文件资源 .etc）那么后续的操作均无法进行，需要立即触发 cancellation cascade。

而资源释放是否需要执行仅依赖分配操作是否成功；只要资源成功分配，即使后续操作立即失败并触发 cancellation cascade，也需要执行资源的释放操作。

C++ 通过 RAII idiom 达成这一目标；而 C#/Java 等则有对应的 try…catch…finally 或者其他 resource guard。

正因为资源的释放如此的特殊，绝大多数情况下，后续的其他操作并不依赖资源正确的释放：即使资源释放失败，也不需要触发 cancellation cascade；因为后续操作不依赖这些资源，使得这部分资源泄露在**此时**是可以被容忍的。

### 0x0. Destructors that Only For Releasing Resources

C++ 标准没有限制 destructor 只能做什么，所以理论上你可以在析构函数中做任何事情。

但是如果你遵守

> 析构函数仅用于释放资源

这一规则那么你的 C++ 编码生活会清真很多。

前面说到资源释放失败是可以容忍的，不需要中止后续的操作。这映射到析构函数的设计要求上便是：析构函数绝不应该允许有异常逃逸。

自 C++ 11 开始，析构函数默认是 `noexcept` 的，一旦有异常从析构中上抛，会立即触发程序的 `std::terminate()`。

如果这个规则能得到保证，同时出错的对象保证自身处于 valid state 那么 stack unwinding 过程中就能保证沿途的对象被安全顺利的析构销毁。

## Observation & Conclusion

1. 不管采用何种错误处理手段，错误处理的核心是：中止执行那些依赖失败了的操作的后续操作。
2. 尽可能保证析构函数在语义上只用于释放资源
3. 如果操作失败时不需要取消后续操作（触发 cancellation cascade）不要对外抛出异常，例如释放资源失败
4. catch 异常代表终止 cancellation cascade
5. 不要 catch 异常，除非你确定后续操作不依赖 try block 中的操作成功
6. 对象的修改操作失败时尽可能保证对象仍处于 valid state