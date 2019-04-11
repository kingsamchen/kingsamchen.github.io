---
title: 统一 ezio Buffer 的算术类型读写接口
categories: PROGRAMMING
date: 2019-04-07 20:40:11
tags: [cpp, template, SFINAE, ezio]
---
ezio Buffer 一开始的时候只为 read, write 和 peek 提供了从 `int8_t` 到 `int64_t` 的函数重载，如果需要处理 unsigned integers，那么就需要自己额外做 `static_cast`。

ezio 的主要客户[藏心](https://github.com/oceancx)同学早前抱怨过这个问题，并且同时建议我加上对 `float/double` 的浮点数支持。

对于这个建议我一开始是抵触的：

- 自己 cast 又不是不能用，额外加重载支持三个操作工作量都要翻一番呢
- 浮点数的 binary serialization 本来就是很难跨平台的，不是每个环境都（虽然大部分）要求使用 IEEE 754 spec。如果真的需要直接把浮点数存到网络包里，自己直接操作 underlying binary layout 不就好了...

于是藏心同学一开始开的 issue 我一直没理他，于是最后他自己关掉了...

等到我自己动手写一个 socks4a proxy 的时候我发现，自己 cast 真的是...太蛋疼了...而且代码看上去还非常丑，大面积的 `static_cast` 制造了相当一部分内容噪音。那会儿我大概有点理解藏心同学的内心感受。

于是我思考良久，打算改造 Buffer 的这部分接口，以支持绝大多数 integer types，顺带也增加入 floating piont types 的支持，这样 read, write, peek 就基本支持了绝大多数 arithmetic types。

通过直接增加重载是我极力避免的，因为除了接口签名外，大部分实现几乎是一样的，不外乎：

- 如果是单字节，直接写/读操作
- 如果是多字节，首先字节序转换，然后做写/读操作
- 如果是浮点数，首先按照对应字节大小的整数类型**解释**内存，然后参考普通整数的处理

于是自然而然的想到直接将函数做成 function templates 来增强语义。
<!-- more -->
下面以 Write 操作为例，阐述一下整体设计。

### Impl

首先我们需要针对模板参数类型做限定，只支持整数和浮点数，这个可以直接用

```c++
static_assert(std::is_arithmetic<T>::value, "T must be integral or floating point");
```

保证。

因为我们可以在编译期获用 `sizeof(T)` 得具体模板参数的内存大小，所以我们提供两个 `WriteImpl()` 函数，分别处理单字节和多字节的情况。

函数的编译期选择可以简单的利用 tag dispatch 实现：

```c++
struct single_byte {};
struct multi_byte {};

template<typename T>
void Write(T value)
{
    static_assert(std::is_arithmetic<T>::value, "T must be integral or floating point");
    using byte_trait =
        std::conditional_t<sizeof(T) == 1, internal::single_byte, internal::multi_byte>;
    WriteImpl(value, byte_trait{});
}

// private

template<typename T>
void WriteImpl(T value, internal::single_byte)
{
    static_assert(sizeof(T) == 1, "Require sizeof(T) == 1");
    Write(&value, sizeof(value));
}

template<typename I>
void WriteImpl(I value, internal::multi_byte)
{
    static_assert(sizeof(I) > 1, "Require sizeof(I) > 1");
    auto be = HostToNetwork(value);
    Write(&be, sizeof(be));
}
```

接下来来考虑如何处理浮点数。

因为我们已经按照单/多字节区分了函数 tag，如果再按照浮点数/整数区分一次，tag 层次会变深；而且浮点数目前肯定是多字节长度的。

一个容易想到的方法是使用 SFINAE 来将多字节的情况拆分成整数和浮点数：

```c++
template<typename T>
struct floating_point_store_type;

template<>
struct floating_point_store_type<float> {
    using type = uint32_t;
};

template<>
struct floating_point_store_type<double> {
    using type = uint64_t;
};

template<typename I, std::enable_if_t<std::is_integral<I>::value, int> = 0>
void WriteImpl(I value, internal::multi_byte)
{
    static_assert(sizeof(I) > 1, "Require sizeof(I) > 1");
    auto be = HostToNetwork(value);
    Write(&be, sizeof(be));
}

template<typename F, std::enable_if_t<std::is_floating_point<F>::value, int> = 0>
void WriteImpl(F value, internal::multi_byte)
{
    static_assert(sizeof(F) > 1, "Require sizeof(F) > 1");
    static_assert(std::numeric_limits<F>::is_iec559, "IEEE 754 spec is enforced");
    using store_type = typename internal::floating_point_store_type<F>::type;
    WriteImpl(*reinterpret_cast<store_type*>(&value), internal::multi_byte{});
}
```

因为 `double` 和 `float` 要使用不同的存储类型去解释，所以上面还需要额外做一个 type mapping。

另：我们在浮点数的处理里强制要求浮点数类型的实现是符合 IEEE 754 spec 的。

这样一顿操作之后我们就已经给 Buffer 加上了通用的 Write 函数族。并且未来如果新增了类似 `char8_t` `int128_t` 什么的类型，接口实现几乎不用做任何改动。

### Final Result

用类似的方法为 `PeekAs()` 和 `ReadAs()` 增加支持后，我们可以做到：

```c++
TEST_CASE("Read trivial integral and/or floating point values", "[Buffer]")
{
    Buffer buf;

    SECTION("read trivial integrals") {
        buf.Write(uint8_t(0xFF));
        buf.Write('a');
        buf.Write(short(-1));
        buf.Write(uint32_t(0xDEADBEEF));
        buf.Write(size_t(1024));

        REQUIRE(buf.ReadAs<uint8_t>() == uint8_t(0xFF));
        REQUIRE(buf.ReadAs<char>() == 'a');
        REQUIRE(buf.ReadAs<short>() == -1);
        REQUIRE(buf.ReadAs<uint32_t>() == 0xDEADBEEF);
        REQUIRE(buf.ReadAs<size_t>() == 1024);
    }

    SECTION("read floating points") {
        // exact hexadecimal representation is: 0x407e0000
        float f = 3.96875F;
        buf.Write(f);
        double d = 3.1415926;
        buf.Write(d);
        auto f_ep = buf.ReadAs<float>() - 3.96875F;
        REQUIRE(f_ep == 0);
        auto d_ep = std::abs(buf.ReadAs<double>() - d);
        REQUIRE(d_ep == 0);
    }
}
```

可读性也变高了。





