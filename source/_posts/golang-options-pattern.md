---
title: Golang's Options Pattern
categories: PROGRAMMING
date: 2020-06-30 09:29:23
tags: [golang, options, design patterns]
---
可以将 Golang 的 _Options Pattern_ 视作（有副作用）函数式版的 _Builder Pattern_，其核心是：特定的 option-function 调用会生成对应的类型为 Option 的闭包，执行闭包会修改内部的 Options 结构。

Golang 的函数支持同一类型的不定参数，因此上面的闭包类型一致。

区别传统 Builder Pattern：

- builder pattern 通过成员函数调用直接修改 builder 对象的属性成员；或者通过成员函数直接修改实例对象的成员

- options pattern 是通过函数调用生成闭包，内部通过执行闭包来修改对应成员

```golang
package main

import (
    "fmt"
    "time"
)

// ClientOptions defines options of a client instance
type ClientOptions struct {
    Timeout time.Duration
    Retry   int
}

type ClientOption func(*ClientOptions)

func WithTimeout(dur time.Duration) ClientOption {
    return func(opts *ClientOptions) {
        opts.Timeout = dur
    }
}

func WithRetry(cnt int) ClientOption {
    return func(opts *ClientOptions) {
        opts.Retry = cnt
    }
}

type Client struct {
    opts *ClientOptions
}

func NewClient(opts ...ClientOption) *Client {
    // can use some default values for options
    cli := &Client{
        opts: &ClientOptions{},
    }

    // apply options
    for _, opt := range opts {
        opt(cli.opts)
    }

    return cli
}

func main() {
    cli := NewClient(WithTimeout(time.Second * 3))
    fmt.Println(cli.opts.Timeout)
}
```

## 基于 interface 的扩展

除了生成闭包，option 函数也可以生成实现了对应接口的结构：

```golang
type Options struct {}

type Option interface {
    apply(*Options)
}
```

参考 uber-golang-style 里的 [functional options](https://github.com/uber-go/guide/blob/master/style.md#functional-options)

## Rant

根据 @vizze 的说法，golang 创建闭包的开销比直接函数调用高不少。默认 golang 行尾自动添加分号的策略导致多个函数调用换行写法难看。

另，对于 C++ 来说也可以使用 golang 这种风格。不定模板参数解决不定参数问题，同时这里需要的是一个 callable concept，lambda 或者 `std::function<>` 都可以做到，前者应该可以做到 zero-cost abstraction，后者会有一些额外开销。
