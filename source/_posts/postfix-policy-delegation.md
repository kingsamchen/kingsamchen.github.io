---
title: Postfix Policy Delegation
categories: PROGRAMMING
date: 2021-09-23 23:01:24
tags: [mta, postfix]
---
比较新的版本的 postfix 除了 milter 之外还可以用 [policy delegation](http://www.postfix.org/SMTPD_POLICY_README.html#client_config) 做 content filtering；后者的优势在于简单容易实现，但是只能读取有限的 email attributes。

policy delegation 实现上也有三种：

1. 基于 tcp 的 policy server
2. 基于 unix domain socket 的 policy server
3. 基于 master daemon spawn 的 policy server

第三种实现上最简单，并且虽然文档上说它也是基于 unix domain socket 实现通信，但是这部分通信实际上对 policy server 是透明的。

第三种情况下，只需要提供一个简单的 binary，能够读写 stdin/stdout 即可完成通信：

- 从 stdin 读出 request attributes
- 将判断结果往 stdout 写

文档上虽然没有明确写出这点，但是通过附带的 example 用例可以证实。

不过使用这种方式要注意一个坑：stdin/stdout 的读写必须不能使用 `fread()/fwrite()` `std::cin/std::cout` 这种带有应用层缓存的 IO 设施。

虽然这里是对 stdin/stdout 读写，但是实际上底下通讯还是会经过 unix domain socket，使用带应用层缓存的库函数读写时会导致阻塞。

相应的，这里要么使用 `read(2)/write(2)` 这种 syscall，要么手动屏蔽标准库的应用层缓存。

对于没有经验的人来说，这里是一个很容易掉的坑

<!-- more -->