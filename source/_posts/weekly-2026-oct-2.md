---
title: 一周杂记 in Week 2 Oct 2026
categories: CODE-LIFE
date: 2026-10-12 00:06:44
tags: [杂记]
---
本周（10.5 ~ 10.11）是十月份第二周，十一假期结束了，又要开始进入牛马生活

## Life

\#1

5号傍晚下着小雨，我们到了老家高铁站候车。

感觉回程的商务座买亏了，因为老家高铁站的商务候车室居然还是和浙商银行啥的拼的，连好点的零食茶水都没...

不过考虑到商务座人少，我们那节大车厢也只有11个座位，并且还没坐满；可以减少秋宝病毒暴露，也还算可以吧。

到了西站之后找车也顺利，西站停车真便宜，就花了80块钱不到...

晚上 8:30 不到就顺利到家了

稍微收拾一下就给秋宝哄睡了

真是辛苦的一天啊 😅

\#2

本来约了6号的拳击课，因为晚上有电影，所以想和包子说早一点开始得了

结果包子提前找我说他晚上有事，能不能提前到3点开始；那肯定可以啊

结果6号我 14:55 到健身房操房，我靠，怎么有这么多人？看了一下信息，我靠，怎么临时加了一节3点开始的团课...

包子也很无奈，商量了一下今天这节算了，改期再约好了。。。

\#3

8，9两天照常上班，因为有几个同事还在休假，所以感觉上班的氛围不是很重

10号居家，下午3:30开始请假，因为4点终于可以上拳击了

感觉勾拳还是需要持续的训练，现在左右连续勾拳都不能保持髋部稳定。。。

\#4

6号晚上约了 chao / iker / jed 一起看生化危机爆发日，顺带带了媳妇儿。结果媳妇儿给吓到了，结束的时候下楼梯差点连摔两个阶梯。。。

- **生化危机：爆发夜 Resident Evil 4/5** 导演太会拍了 这种镜头视角就好像突然穿越到生化危机宇宙变成一个路人玩家 沉浸感拉满

## Work

\#1

**CppCon 2023 | More C++ Ranges Please - Roi Barkan**  https://www.youtube.com/watch?v=Ugb8-ig7Pnw&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=67

- 讲的不是如何使用 ranges/views，而是有了 ranges/viwes 这种超强 composability 的东西之后，标准库的算法应该重新审视并做一些扩充（algorithmic vocabulary）

**CppCon 2023 | Evolution of a Median Algorithm in C++ - Pete Isensee** https://www.youtube.com/watch?v=izxuLq_HZHA&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=70

- 超棒的一个 talk，从最基础的 median 出发，一步步实现的更像 STL algorithms 那种通用设计，并且还要考虑到使用者的建议方便
- nth_element 这个大家应该都很熟悉了，midpoint() 是 C++ 20 新引入的
- talk 里当个数是偶数时，先调用一编 nth_element，然后对左半部分跑一次 max_element
- 最后 generic 和返回类型处理有点意思

**CppCon 2023 | Robotics at Compile Time: Optimizing Robotics Algorithms With C++'s Compile-Time Features**  https://www.youtube.com/watch?v=Y6AUsB3RUhA&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=69

- c++ 对于 compile-time 的支持越来越完善了，机器人领域用 compile-time 能把很多以前需要在 runtime 跑的都前置到 compile-time
- 一些确定的 configuration check 甚至 fixed collison detection 都放到 compile-time

**CppCon 2023 | ISO C++ Standards Committee Panel Discussion - Hosted by Herb Sutter** https://www.youtube.com/watch?v=sf_3Vfh6yRA&list=PLHTh1InhhwT7gQEuYznhhvAYTel0qzl72&index=70

- 委员会座谈会，各个参会的讲了自己对 23 最满意的 feature 并且对26最期望的 feature；Bjarne 说 `import std` 的时候全场鼓掌
- 观众提问的时候委员会说，他们不会因为某个特性特别有用/重要就会把他引入标准，然后提的例子是 static reflection…囧rz
- 写提案最重要的是什么，是 defining the problem；RAII 依然是最重要的基础；然后 tooling 一定要随着语言进化
- 整体还是挺有意思的

**C++ Weekly - Ep 552 - C++26's is_within_lifetime** https://www.youtube.com/watch?v=EaYvQz8oQkM

- C++ 26 引入了 `std::is_within_lifetime()` 可以在编译期检测 union 指定的 member 是否处于 active 状态

    ```cpp
    union values {
      int x;
      int y;
    };

    consteval auto use_values() {
      values v;
      v.x = 42;
      v.y = 10;

      if (std::is_within_lifetime(&v.x)) {
        return v.x;
      } else {
        return v.y;
      }
    }
    ```


**C++ Weekly - Ep 73 - std::string_view** https://www.youtube.com/watch?v=fj_CF8xK760

- 介绍 std::string_view
- 2026 年了，应该没人不知道这个了吧

\#2

给 fawkes 加了 response 的 nosniff header https://github.com/kingsamchen/fawkes/pull/41

修了一个 route tree locate 的 bug，这 bug 太低级了但是当时那坨 unittests 硬是没发现… https://github.com/kingsamchen/fawkes/pull/42

做了 HEAD requests 的支持，按照实践，HEAD requests 如果没有专门的 handler 应该 fallback 到 GET route；对于 Content-Length header 处理可讲究了

既然都做了这个，顺带把 204/304 的 response 也给修了一下 https://github.com/kingsamchen/fawkes/pull/43

\#3

研究了一下 Windows/Linux 上跑 ChatGPT 走 http/ws 代理的方式

```powershell
$Proxy = "IP:PORT"

# Kill an existing ChatGPT/Codex process tree first,
# otherwise an already-running instance won't inherit the new environment.
Get-Process ChatGPT, codex -ErrorAction SilentlyContinue |
    Stop-Process -Force

$pkg = Get-AppxPackage -Name OpenAI.Codex

if (-not $pkg) {
    throw "OpenAI.Codex package not found"
}

$exe = Join-Path $pkg.InstallLocation "app\ChatGPT.exe"

if (-not (Test-Path $exe)) {
    throw "ChatGPT.exe not found: $exe"
}

$ProxyEnv = @{
    HTTP_PROXY  = $Proxy
    HTTPS_PROXY = $Proxy
    ALL_PROXY   = $Proxy

    # Extra compatibility for clients that explicitly inspect these.
    WS_PROXY    = $Proxy
    WSS_PROXY   = $Proxy

    NO_PROXY    = "localhost,127.0.0.1,::1"
}

Start-Process `
    -FilePath $exe `
    -WorkingDirectory (Split-Path $exe) `
    -Environment $ProxyEnv `
    -ArgumentList @(
        "--proxy-server=$Proxy",
        "--disable-quic"
    )
```

```bash
#!/usr/bin/env bash

PROXY="http://IP:PORT"

export HTTP_PROXY="$PROXY"
export HTTPS_PROXY="$PROXY"
export http_proxy="$PROXY"
export https_proxy="$PROXY"

export ALL_PROXY="socks5h://P:PORT"
export all_proxy="$ALL_PROXY"

export WS_PROXY="$PROXY"
export WSS_PROXY="$PROXY"
export ws_proxy="$PROXY"
export wss_proxy="$PROXY"

export NO_PROXY="localhost,127.0.0.1,::1"
export no_proxy="$NO_PROXY"

exec /usr/bin/chatgpt \
    --proxy-server="$PROXY" \
    --disable-quic \
    "$@"
```

\#4

这周在公司踩了一个 glibc 版本更新推送的坑

我们服务需要 glibc-langpack-xx 支持 l10n，但是因为公司合规问题以及不想装 glibc-langpacks-all（太占空间）导致我们用了三个源包：

1. 构建镜像 ubi9 的官方源
2. 运行镜像 ubi9-micro 的官方源
3. almalinux9 的官方源（提供细分的 glibc-langpack-xx）

而 glibc-langpack 必须要和 glibc 版本对应

因为 glibc 出了 CVE，所以 ubi9 官方源率先推送了新版本的 glibc；但是 ubi9-micro 和 almalinux9 都还没推到，所以 docker image 构建失败了...

一开始我想那直接以 ubi9-micro 的 glibc 为准得了，结果这个 MR 合进去没多久，docker image 构建又挂了。

这回是 ubi9-micro 也收到了推送，但是 almalinux9 还没更新。。。我凸(艹皿艹 )

最后和AI研究了一下，干脆直接装 glibc-langpacks-all 得了，因为这个可以从 ubi9/ubi9-micro 直接装...

改了之后终于正常了

代价就是每个镜像多了大概 ~60 MB

---

这周就这样，下周见~
