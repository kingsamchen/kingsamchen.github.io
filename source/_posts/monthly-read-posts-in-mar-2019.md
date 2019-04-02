---
title: Monthly Read Posts in Mar 2019
categories: PROGRAMMING
date: 2019-04-02 23:18:21
tags: [browser caching, cors, docker, monorepo, spurious wakeup]
---
## Networking

[The State of Browser Caching, Revisited](https://www.mnot.net/blog/2017/03/16/browser-caching)

各家浏览器对 HTTP caching 的支持概况

---

[Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

[ajax跨域请求及相关](http://fengxu.ink/2018/02/15/ajax%E8%B7%A8%E5%9F%9F%E8%AF%B7%E6%B1%82%E5%8F%8A%E7%9B%B8%E5%85%B3/)

和 CORS 有关的两篇 posts。第一篇是 MDN 的官方教程。

需要注意的是，CORS在服务端的表现**不是限制**某些 URL 只能被某些 domain 的请求访问，而是允许这些 URL 可以在满足要求的条件下（origin 是某个匹配 domain），允许接收跨域请求。

跨域禁止的同源策略是由 browser 实施的，当收到服务端许可的情况下才会继续。

## SRE

[Docker 核心技术与实现原理](https://draveness.me/docker)

科普型，内容深度并不深，适合作为了解

## Software Engineering

[What Are The Best Software Engineering Principles?](https://dev.to/luminousmen/what-are-the-best-software-engineering-principles--3p8n)

列举了一些软件工程的基本原则。

---

[Monorepos: Please don’t!](https://medium.com/@mattklein123/monorepos-please-dont-e9a279be011b)

这篇文章算是很标准的檄文：首先点出几个 monorepo 的理论优点然后逐一分析，得出这些优点并没没有看上去那么好；接着列举缺点。

最后总结得出核心是工程师文化。

---

[Monorepo: please do!](https://medium.com/@adamhjk/monorepo-please-do-3657e08a4b70)

这篇文章是上面那篇的 argue，但是相比起来写的就太一般了。

全片的核心论点就一个： monorepo 解决的是大团队里各个子团队的沟通提问题。

---

[Things You Should Never Do, Part I](https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/)

KEY: Do not rewrite your code.

## Concurrency

[Spurious wake-ups in Win32 condition variables](https://blogs.msdn.microsoft.com/oldnewthing/20180201-00/?p=97946)

简要解释了 Windows 上 condition variables 会出现 spurious wakeups 的两个原因

---

[Locks Aren't Slow; Lock Contention Is](https://preshing.com/20111118/locks-arent-slow-lock-contention-is/)

频繁的锁竞争是使用锁慢的根本原因，剩下的就老论点：减少临界区大小，减少锁竞争发生概率 .etc

文中用泊松分布模拟了实际使用 case 中锁竞争概率和性能的关系，50%的竞争下，两个线程也能由1.5x的提速