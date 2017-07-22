---
title: Send Specific Cookie in URLFetcher
categories: PROGRAMMING
date: 2017-07-23 00:14:34
tags: [cookie, chromium, urlfetcher]
---
使用 `URLFetcher` 作为网络请求的基础组件有一个优势：如果 server 的 response header 指明了 `set-cookie`，那么 `URLFetcher` 在发出下一个请求时会自动往 request header 中设置 cookie。

这为使用某些需要在请求时附带 cookie 的 API 提供了便利；但是同时也在某一些场合下导致了一些问题。

如果你需要为某个 API 请求的 request header 中携带自定义的 cookie 数据，例如 `cookie: buvid=xxxx`，那么，即使你在发送请求前通过 `AddExtraRequestHeader()` 添加了 cookie 头，你也会惊奇地发现，出去的请求并没有携带你设置的 cookie，而是只有 `URLFetcher` 自己设置的，服务端提供的 cookie 值。

不要问为什么这个时候要用 cookie 传递这个信息，而不是某个独有的 request header，亦或者 query-string；你永远不知道和你对接的人是一个怎样的智障；毕竟，没有谁会真的去读 RFC。

自定义 cookie 丢失的原因大概如下：

1. 你首先设置了 cookie，然后发出请求
2. `URLFetcher` 在内部实际进行网络请求前，会设置服务端的 cookie 值（因为之前的请求存在 `set-cookie`）
3. 因为 RFC 规定，一个 request header 中， cookie header line 有且仅有一个，也就是说

```
cookie: buvid=xxx
cookie: sig=xxx
```

是不合法的；虽然可以通过

```
cookie: buvid=xxx; sig=xxx
```

来传递多个value，但是这要求 `URLFetcher` 在添加 cookie 时要能够 aware 到这点；很可惜，根据情况来看这点不成立

4. 于是 `URLFetcher` 粗暴的替换了你设置的 cookie 值

扫了一遍代码之后，我想到一个 workaround，实践证明确实可以：

发请求前设置 `URLFetcher` 的 load flag，加上 `LOAD_DO_NOT_SEND_COOKIE`，阻止 `URLFetcher` 自己设置 cookie 的行为，完全由自己控制请求的 cookie。

话说用 `URLFetcher` 被坑了好多次（算上 chromium 其他的 lib，次数可以翻翻），还好每次都能够凭借“优秀工程师的第六感”蒙中问题的 root cause，进而化险为夷。我突然感觉自己理解了什么叫 *文章本天成，妙笔偶得之*。