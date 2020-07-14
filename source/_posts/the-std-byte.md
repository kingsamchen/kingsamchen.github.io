---
title: The std::byte For Byte Addressing
categories: PROGRAMMING
date: 2020-07-14 00:55:59
tags: [cpp]
---
时隔多年，C++ 17 终于迎来了专门用于 byte-oriented access 的数据类型 `std::byte`。

之所以引入一个新的类型是因为之前不管是 `char` 还是 `unsigned char`，都承载了三种角色

1. 字节寻址 byte addressing
2. 算术操作类型
3. 展示字符类型

这样很容易引发混乱和错误。

另外即使是 `uint8_t`，也至少承担了算术操作类型；因此标准委员会觉得需要一个**仅用于字节寻址**的 byte 类型。

官方口径叫：_to distinguish byte-oriented access to memory from accessing memory as a character or integral value_.

实现上你会发现 `std::byte` 其实是基于底层类型为 `unsigned char` 的 enum class：

```cpp
enum class byte : unsigned char {} ;
```

这似乎有意而为之。

第一是限制 `std::byte` 的算数操作能力；同时因为字节通常会涉及到位运算，因此额外提供了位运算的操作符重载。

这避免意外运算导致的错误：

```cpp
std::byte read_mistake(std::byte* b, int offset)
{
    // should be *(b + offset)
    return *b + offset;     // <- compile error
}
```

另外 enum class 的出身导致了 implicit conversion 都不被允许，到 integer 的转换必须使用 `to_integer()` 以及强制类型转换。

同时初始化如果不借助强制类型转换就必须使用 braced-initialization，即

```cpp
std::byte b {42};
```

另一个原因是采用 enum class 可以保证外部类型和底层类型的大小和内存对齐一致。

细节见: https://stackoverflow.com/questions/44508172/why-is-stdbyte-an-enum-class-instead-of-a-class
