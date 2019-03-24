---
title: socks4a 协议代理
categories: CODE-LIFE
date: 2019-03-24 20:07:03
tags: [socks4a, socks, proxy]
---

对于网络编程学习而言，尝试实现一个 proxy 是一个很好的途径。因为一个 proxy 对于 clients 来说是 server，对 remote servers 来说又是 client，一下就覆盖到了网络编程的两个大块。

同时，需要管理/协调两侧的连接对如何正确的处理网络连接的关闭也是一个挑战。

对比普通的 HTTP Proxy，socks4a proxy 作为 transport layer proxy 在协议自身上更加”干净“（不需要像 HTTP Proxy 那样解析厚重的各种 header 以及对 HTTPS 的特殊处理），也更贴近 TCP 网络编程的具体实践。

对比更新的 socks5，socks4a 仅支持 TCP，对 TLS 信道加密没有协议上的要求，更容易实现。

另，[socks4a](https://www.openssh.com/txt/socks4a.protocol) 协议是 [socks4](https://www.openssh.com/txt/socks4.protocol) 的一个补丁版，两个协议的 RFC 文本打印下来整体不超过两页 A4 纸，半个小时就能看完。

上周的时候我为 ezio 增加了一个 socks4a 的[简单实现](<https://github.com/kingsamchen/ezio/tree/master/examples/socks4a>)，过程中发现了 `TCPClient` 在 teardown 时存在[缺陷](<https://github.com/kingsamchen/ezio/commit/24c1dc4d6af91f03dd748d4a3713280e71eaceef>)，有概率导致连接的断开和 `TCPClient` 的析构出现 race。

同时还发现作为测试的 curl v7.46 （ubuntu 16.04 LTS apt 版本）在处理 HTTPS 请求时存在缺陷，会导致 client connection 过早断开，proxy 出现 RST 错误。

这说明拿 socks4a proxy 作为熟悉具体的语言/网络库的编程范式的例子还是非常不错的。

注：socks4a 协议有两个连接模式：常用的 `CONNECT` 和 `BIND`，后者是为了适应 FTP 的 [active mode](<https://stackoverflow.com/questions/1699145/what-is-the-difference-between-active-and-passive-ftp>) 而存在的，现在基本没有使用的必要，可以直接忽略。

