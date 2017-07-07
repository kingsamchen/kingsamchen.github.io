---
title: Bypass Proxy in URLFetcher
categories: PROGRAMMING
date: 2017-07-07 09:07:31
tags: [chromium, proxy, urlfetcher]
---
`net::URLFetcher` 默认情况下会使用系统代理，对于针对应用于浏览器而设计的网络组件来说，这是合情合理的；并且这也方便了测试对于网络接口的调试，因为只需要打开 Fiddler 或者 Charles，就可以看到应用发出去的 HTTP 请求。

但是有时候我们又希望默认情况下不开启代理支持，比如：在我用 `net::URLFetcher` 重写某直播姬的网络通信组件后，出现了不少傻逼用户因为不知道自己系统上为什么会各种乱七八糟的本地代理而导致无法登陆。

这个时候产品的策略就应该改为：默认不使用代理，如果有特殊需要，通过命令行参数启动。

因为产品项目用的 chromium 的代码比较老（估计是 2x 的），这个版本的 `net::URLFetcher` 只要是使用 `URLRequestContextBuilder` 创建的 `URLRequestContext`，那么就一定会使用系统代理，这是写死在代码里的...

而且 `URLRequestContextBuilder` 也没有提供额外的途径让我们修改；而一旦创建完 `URLRequestContext`，此时再设置新的 `ProxyService` 是没有用的...

我觉得这是一个设计上的失误，不知道新版本的代码有没有针对这块做了什么改动。

万幸，`URLFetcher` 对象可以通过 `SetLoadFlags()`，使用标志 `LOAD_BYPASS_PROXY` 来强迫当前 fetcher 对象绕过代理。

于是设计就变成：如果没有外部设置，fetcher 在发出请求前设置 `LOAD_BYPASS_PROXY`。

### Afterthoughts

在研究代码过程中我发现 `URLRequestContext` 理论上是可以使用自己定义的子类的；builder 创建的是内部实现的一个 `BasicURLRequestContext`。chromium 在这里的设计颇有点**机制策略分离**的意味。

不过因为这个问题发现在新版本即将提测前，因此就将这个想法列为后续的 TODO 了。
