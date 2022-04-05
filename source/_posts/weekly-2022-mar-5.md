---
title: 一周杂记 in Week 5 Mar 2022
categories: CODE-LIFE
date: 2022-03-29 15:52:21
tags: [杂记]
---
## Life

\#1

本周是清明假前的最后一周，要上6天班。周二的时候把去年遗留的一天年假用了，结果到了周三 3/30 的时候行政通知说因为今年疫情原因特殊，上一年度的年假的有效期可以一直延长到年底...

不是，你咋不早一点说呢??

\#2

因为最近疫情越来越“严重”了，所以清明期间哪也去不了，估计就在家躺着。

上海现在的样子直接说明了，哪怕你之前吹嘘自己是自由文明城市，红色铁拳来临时，一个都跑不掉。

清零政策就是一个笑话，走着看吧，看经济和政策哪个先死。

\#3

这周花了3-4天把 _The Madalorian_ 的衍生剧 _The Book of Boba Fette_ 看完了。剧集保持了曼达洛人一贯的质感和水准，但是不知道是不是迪士尼自己没有底，所以最后还是让 Mando 出场了两集，不过如果说是为 The Madalorian S3 提前宣传的话也说得过去。

星战的衍生剧和衍生剧的衍生剧比正传好看太多，真是一件有意思的事情

\#4

电影方面抽空看了 Venom 2，实在是一言难尽，90分钟的电影至少有1/3实在掺水，全程亮点还不如最后一分钟的彩蛋...

\#5

然后还继续打了几把求生之路，嗯...

## Work

\#1

这周还在折腾 CPR。

DevOps 处于安全团队的要求把环境的 libcurl 升级到了最新的 v7.8 以支持 HTTP/2，但是有个 server api 的日志出现了大量错误。于是 US 那边的同事怀疑是 CPR 版本太老旧导致的，所以建了一个 JIRA ticket 去升级 CPR。于是这个 task 就毫无疑问地落到了我头上。

我看了一下 CPR 对 HTTP/2 支持的 MR，发现本质上只是提供了设置 libcurl HTTP version 字段的接口而已，没有什么实质性的变更，所以我对升级 CPR 能解决问题的假设存疑。

而且 libcurl 官方文档说 HTTP/2 Multiplexing 需要使用 multi interfaces，而 CPR 都是在 easy interfaces 上用的，所以显然是无法支持的。

而事情的发展和我预料的一样，升级 CPR 并且显式指定 HTTP/2 之后问题依旧。

最后和另外一个同事研究了一把，发现这个问题在 libcurl 这一层就出问题了，直接使用 curl 并且走 HTTP/2 协议通讯也会出现一样的问题。

tcpdump 抓了包之后用 wireshark 简单的看一下发现是我们的 sever response 有个地方会导致 client side 的 HTTP/2 协议出现异常，直接触发 RST termination。

最后的做法是暂时先将 HTTP 请求的版本固定在 HTTP/1.1，后面专门来查这个问题。毕竟目前团队中没有人对 HTTP/2 协议算得上了解。

\#2

另外还抽时间做了某个 es service integration test。

es 对外提供了 HTTP/1.1 接口，很多操作可以直接通过 REST API 完成。

有意思的是，ES 的 query 接口大概是我第一次在现实中看到 HTTP GET 携带 body 的场景

\#3

Lumper 目前进度卡在创建子进程运行指定程序这一步。

因为我还没想好 base/subprocess 要怎么设计才能比较优雅的支持 standard i/o 的 重定向策略。

初步实现下至少要能支持：

1. 重定向到 null device
2. 重定向到 pipe；即父进程可以通过 subprocess::stdx_pipe 进行读写
3. 重定向到指定 fd；比如为了实现经典的 `2>&1`

boost/process/child 的实现虽然提供了足够的灵活度，但是太复杂了，对 lumper 来说不是太有必要。

瞄了一下 folly 的 subprocess （噫，人家这名字也是受到 python subprocess module 启发呢），感觉是可以不错的参考范本。

另外一个坑是，因为创建子进程需要指定 namespace 隔离，所以不能直接用 `fork()`，只能用 `clone()`

而且看了几个相关实现，发现大家都是用的 raw syscall clone，而非 glibc 包装的 `clone()`，似乎是因为后者这个场景下不太好用。

---

这周就这样，下周见
