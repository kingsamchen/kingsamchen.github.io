---
title: Monthly Read Posts in Nov 2016
categories: PROGRAMMING
date: 2016-12-04 21:53:00
tags: [monthly read posts, stack frame layout, x86-64 ABI, https, DRY]
---
[Stack Frame Layout on X86-64](http://eli.thegreenplace.net/2011/09/06/stack-frame-layout-on-x86-64)

**Main observations**

For AMD64 X64 ABI compiliant platforms (such as Linux), the major changes include:
- Introduces a red-zone storage area on the stack
- Officially makes frame pointer explicitly optional

For Windows X64 ABI(though depictated in supplementary):
- No red-zone; instead, a pre-allocated home-space is enforced for every function call
- Cleanup of old calling convention legacy; no more worry about cdecl/fastcall/stdcall etc.

[Understanding Security in IoT: SSL/TLS](http://www.golgi.io/security-ssl-tls/)
[我也想来谈谈HTTPS](http://insights.thoughtworkers.org/talk-about-https/)

**Why HTTPS**

Why HTTP is not secure?

Because it transports information in plain text and with no anti-eavesdroping protection.

**How does it work**

After TCP connection is established, the client and server will futher start TLS/SSL handshake:
1. Client-Hello
2. Server-Hello
3. Authentication and Key Exchange
4. Server-Finish
5. Client-Finish

**Extra Bonus**

How to use openssl for https.

帧哥在他的[技术博客分站](http://blog.syscallx.com)上有三篇和 TLS/SSL 协议实现相关的 posts，可以放到下个月的 reading list 里

[ON DRY AND THE COST OF WRONGFUL ABSTRACTIONS](http://thereignn.ghost.io/on-dry-and-the-cost-of-wrongful-abstractions/)

**Insight**

Duplicating code sometimes can be a good thing (when the cost imposed by abstractions is too high); namely, context matters.

**Reasons**

Programmers spend far more time reading code (to understand logic behind) than writing code.

Abstractions cut both ways: every abstraction comes at a cost.

Every abstraction imposes an extra layer of knowledge, which places cognitive load on brain, and in turn, increases the time spent on understanding that piece of code.

In some extreme cases(everything is encapsulated in abstractions), the abstraction lost its value, and became a wrongful one.

**Conclusion**

Always abstract if the cost of abstraction does not surpass the cost of duplicating code.

But, it's just hard to get it right in real practice.

**Extra Comments**

之前看过一篇类似的 post，标题大概是 *dependency versus redundancy*，印象中里面叙述的观点比这边更加的具体。