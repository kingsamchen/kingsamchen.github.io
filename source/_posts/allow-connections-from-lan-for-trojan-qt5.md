---
title: Allow Connections From LAN for Trojan-qt5
categories: CODE-LIFE
date: 2020-04-19 12:33:18
tags: [梯子, shadowsocks, trojan, socat]
---
因为众所周知的原因，机场的服务商把协议从 SS 切换到了 Trojan。但是之前的 SS windows 客户端只支持 SS 协议，所以切换后只能换 Trojan-qt5 作为客户端。

但是这个客户端做的实在不怎么样，作者似乎是在原来的 ss-qt5 的基础上做的修改，集成了 libtrojan；以至于之前 SS-Windows 用的好好的 *Allow Connections From LAN* 功能到这里没法用了。

更改 socks5 监听地址为 `INETADDR_ANY` 确实能让局域网内的其他主机链接 socks5 代理，但是 HTTP 代理就神奇的不能用了...

试验了一番结果发现要使 HTTP 代理能够正常工作，socks5 代理监听地址必须是 `localhost`... 🤪

因为我的 Linux 开发环境是搭建在虚拟机里并通过 X11 转发到宿主机的，所以不能使用来自 LAN 的连接会严重影响开发效率。

## 解决方案

0. 自己写一个 tcp socket 运行在宿主机上，并监听 `INETADDR_ANY`，Mint 把转发器作为代理
1. 用 netcat 实现
2. 找其他替代品

先考虑 (1)，然而发现 netcat 可以实现单次连接转发，但是后续的同一个连接的通信会直接 hang 住，可能哪里姿势不对

再考虑 (2)，发现可以用 socat。

WSL 里通过 apt 安装，然后

```bash
socat tcp-l:10086,fork,reuseaddr tcp:127.0.0.1:10081
```

Mint 把宿主机的 10086 端口作为代理端口

嗯，发现工作非常顺畅...

这样就不用考虑 (3) 了，不然哪怕用 ASIO 也差不多要百来行代码。
