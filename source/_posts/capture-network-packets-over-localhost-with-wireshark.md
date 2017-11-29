---
title: 用 Wirshark 捕捉分析经过 localhost 的网络包
categories: PROGRAMMING
date: 2017-11-29 23:47:50
tags: [wireshark, localhost]
---
最近写 WinAntHttp 的时候需要用 Wireshark 捕捉并分析发往 mock server 的 http 请求。

然而因为 mock server 是直接跑在本机 localhost 上的，而在 Windows 上，发往 localhost 的网络包都不会经过网卡，所以 wireshark 基本无法捕捉。

研究了一会儿之后发现两个解决方案：

第一个是安装 [npcap](https://github.com/nmap/npcap/releases)，这个是 wincap 的一个 alternative，但是支持本地请求。

不过因为这个需要替换掉原来的 wincap，所以我并没有采用。

第二个是使用 [RawCap](http://www.netresec.com/?page=RawCap)，这货能抓到本地网络包，并且输出的格式是 wireshark-compatible

```
# administrator privilege is required!
RawCap.exe 127.0.0.1 network_cap.pcap
```

考虑到使用频率和侵入性等因素，最终选择使用 RawCap

抓到包之后 wireshark 不能马上直接看到 http 请求，因为普通的 http 请求是和 server 的 80 端口通信，mock server 的 Flask 默认使用 5000 作为通信端口。

通过菜单 `Analyze - Decode As`，将 TCP 5000 端口按照 HTTP 协议解析，这样 wireshark 就可以正常的从 tcp packets 中拆出正确的 http message 了。
