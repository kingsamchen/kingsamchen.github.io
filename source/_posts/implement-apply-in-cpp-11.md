---
title: 在 C++ 11 中实现 apply
categories: PROGRAMMING
date: 2017-03-04 17:08:07
tags: [c++, std::apply, 造轮子]
---
## Why apply matters

假设我们要实现一个和具体业务无关的 HTTP(S) 网络 (Restful)API 请求设施 `RequestConnection`，我们至少要提供两个实现：

- 客户代码的请求结束时的回调 callback/handler
- 针对具体业务的 response parser；因为 HTTP 是文本协议，无论 response data 基于何种格式（例如 JSON），我们都要针对具体的业务逻辑场景进行解析

假设我们希望请求的 callback 足够友好，直接将 client 需要的数据通过函数原型暴露出来，例如

```c++
void OnCheckUpdateComplete(bool succeeded, int last_build_no);
```

那么我们的 reponse parser 也要相应的能够返回符合 callback signature 的数据，在 C++ 中，我们可以用 `std::tuple` 来保存 parsed result：

```c++
std::tuple<bool, int> ParseCheckUpdateResponse(const std::string& data);
```

但是很不巧的，`RequestConnection` 是业务无关的，而业务相关的 callback/response-parser 则是通过依赖注入的方式（例如通过模板参数）进入 `RequestConnection`；这意味着我们需要一个通用的方法能够在编译期完成如下事情：

```c++
// Presume magic function Apply
std::string response_data;
Apply(ResponseHandler, ResponseParser(response_data));
```

神奇函数 `Apply()` 能够调用一个函数，并且将一个 `std::tuple` 里的元素按照顺序转换为对应的实参。

简言之，这就是 C++ 17 要引入的 [std::apply](http://en.cppreference.com/w/cpp/utility/apply) 所做的事情。

但是如果我们的 codebase 是 C++ 11的，那又如何呢？

## How to implement apply in C++ 11

在 C++ 11 里实现 `apply()`，我们要做两步：

1. 实现 index-sequence （C++ 14 开始标准库附带了这个实现）
2. 实现 apply

```c++
#include <tuple>
#include <type_traits>

template<size_t... Ints>
struct index_sequence {
    using type = index_sequence;
    using value_type = size_t;

    static std::size_t size()
    {
        return sizeof...(Ints);
    }
};

template<class Sequence1, class Sequence2>
struct merge_and_renumber;

template<size_t... I1, size_t... I2>
struct merge_and_renumber<index_sequence<I1...>, index_sequence<I2...>>
    : index_sequence<I1..., (sizeof...(I1) + I2)...>
{};

template<size_t N>
struct make_index_sequence
    : merge_and_renumber<typename make_index_sequence<N / 2>::type,
                         typename make_index_sequence<N - N / 2>::type>
{};

template<>
struct make_index_sequence<0> : index_sequence<>
{};

template<>
struct make_index_sequence<1> : index_sequence<0>
{};

template<typename Func, typename Tuple, std::size_t... index>
auto apply_helper(Func&& func, Tuple&& tuple, index_sequence<index...>) ->
    decltype(func(std::get<index>(std::forward<Tuple>(tuple))...))
{
    return func(std::get<index>(std::forward<Tuple>(tuple))...);
}

template<typename Func, typename Tuple>
auto apply(Func&& func, Tuple&& tuple) ->
    decltype(apply_helper(std::forward<Func>(func),
                          std::forward<Tuple>(tuple),
                          make_index_sequence<std::tuple_size<typename std::decay<Tuple>::type>::value>{}))
{
    return apply_helper(std::forward<Func>(func),
                        std::forward<Tuple>(tuple),
                        make_index_sequence<std::tuple_size<typename std::decay<Tuple>::type>::value>{});
}
```

以上代码就是我在某直播姬的网络框架里所用的实现；根据实际是用来看，quite awesome