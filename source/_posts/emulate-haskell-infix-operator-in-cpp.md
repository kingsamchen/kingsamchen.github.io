---
title: Emulate Haskell Infix Operator in CPP
categories: PROGRAMMING
date: 2017-10-29 20:03:06
tags: [cpp, haskell, infix operator]
---
Usage at a glance

```c++
#include <iostream>
#include <vector>

#include "infix_op.h"

int main()
{
    auto find = Infix([](const auto& container, const auto& value) {
        return std::find(container.begin(), container.end(), value);
    });

    auto seq = std::vector<int> { 1, 3, 5, 7, 9 };

    bool found = (seq | find | 7) != seq.cend();
    std::cout << found << std::endl;

    return 0;
}
```

Implementation:

```c++
#pragma once

#include <xutility>

template<typename BinaryFn>
struct InfixOp {
    explicit InfixOp(BinaryFn&& fn)
        : fn_(std::forward<BinaryFn>(fn))
    {}

    BinaryFn fn_;
};

template<typename BinaryFn>
InfixOp<BinaryFn> Infix(BinaryFn&& fn)
{
    return InfixOp<BinaryFn>(std::forward<BinaryFn>(fn));
}

template<typename T, typename BinaryFn>
struct InfixExpr {
    InfixExpr(T&& lhs, BinaryFn&& op)
        : lhs_(std::forward<T>(lhs)), op_(std::forward<BinaryFn>(op))
    {}

    template<typename U>
    decltype(auto) operator|(U&& rhs)
    {
        return op_(lhs_, std::forward<U>(rhs));
    }

    T lhs_;
    BinaryFn op_;
};

template<typename T, typename BinaryFn>
InfixExpr<T, BinaryFn> operator|(T&& lhs, InfixOp<BinaryFn> op)
{
    return InfixExpr<T, BinaryFn>(std::forward<T>(lhs), std::move(op.fn_));
}
```

Have fun.
