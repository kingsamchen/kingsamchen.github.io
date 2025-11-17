---
title: 一周杂记 in Week 2 Nov 2025
categories: CODE-LIFE
date: 2025-11-17 20:11:35
tags: [杂记]
---
本周（11/10 ~ 11/16）是11月份第二周，这周各种突发的事情太多了。

## Life

\#1

算是上周末开始吧，娃突然喝奶量显著降低，连续2-3天一天只喝600ml上下的奶。

换了各种方案发现效果甚微，后来想了一下可能和天气降温之后奶温迅速降低有关。

于是老婆山姆买了一盒蛋糕，用蛋糕的保温膜做了一个保温袋；同时下单淘宝买那种专用奶瓶保温袋。

有了保温袋之后奶量有显著的恢复，而且经过几轮测试我们发现自己DIY的保温袋比淘宝买的还好用一些，相同时间温度始终能多一度...

于是这周娃奶量又慢慢恢复了。

\#2

岳父的单位之前说要搞来杭疗养拼团，但是第一次拼团失败了。

这周他们单位终于有足够的人愿意来杭州疗养，所以这周岳父母直接跟着单位跑来杭州。

疗养只是手段，主要还是想白嫖公家的钱来杭州看秋秋。

预计在杭州带9天，所以刚好两个周末加一周工作日。

\#3

上周几天喂猫的时候发觉已经好几天没看到小蛋了，但是之前小蛋也有跑出去玩几天才回来的经历，所以没怎么当回事。

后来之前一直救助猫的阿姨发微信给我说，小蛋可能走了，因为我们小区那个救助猫的小姐姐说前几天她听到有只胖胖的狸花猫跑到街对面，被不知道什么人用石头砸死了

那个姐姐后来想去把猫的尸体埋掉的时候发现尸体已经被清理了，所以她也不知道是不是小蛋。但是结合小蛋最近确实不见了，以及别人对猫的描述，很有可能受害的就是小蛋

😔，我从没想过这么一直可爱亲人的小猫还没活过两岁就被人残忍杀害了 😭

周末两天看到皮蛋的时候，有时候都感觉一阵心酸。

## Work

\#1

**CppNow 2025 | Welcome to v1.0 of the meta::[[verse]]! - Inbal Levi** https://www.youtube.com/watch?v=BKIuIh3Jo_s&list=PL_AKIMJc4roW7umwjjd9Td-rtoqkiyqFl&index=32

- CPP26 copmile-time reflection 前瞻

**Inside boost::unordered_flat_map** [https://bannalia.blogspot.com/2022/11/inside-boostunorderedflatmap.html](https://bannalia.blogspot.com/2022/11/inside-boostunorderedflatmap.html?m=1)

- 号称目前地表最快的 C++ hash container
- hash-based containers 中 open addressing 非常适合现代的 CPU 架构，非常 cache friendly
- benchmark ranking 中排名好的基本都用 open addressing，区别在于他们的 *collision management techniques*.
- 两大流派：non-relocating 和 relocating，区别在于 寻找空 bucket 时会不会移动已有元素，二者各有天然优缺点
- boost 实现受 abseil 的启发，也有 metadata array 来利用 SIMD 指令做 comparison 加速，快速初筛不符合的候选者；同时还利用 bit interleaving 在不支持 SIMD 平台上模拟类似的快速比较（实现细节真复杂）
- boost 实现会在元素删除时根据策略减少 max load，保证在频繁的 insert/delete 操作之后能触发 rehash，这是为了减少 probe length

**为什么条件锁会产生虚假唤醒现象（spurious wakeup）？** https://www.zhihu.com/question/271521213/answer/1918629786030416391

- 稍微瞄了一下，发现不是新东西。。

\#2

这是一个拖了蛮久的事情，老早就打算给 fawkes 优化一下 pre-commit hook，这样到了新环境下可以比较简单顺利的把 commit hook 给打开。

另外就是老早就想解决一下当开发环境是在 Linux 上时如何把 run tidy diff 也集成到 hook 里。

这个 PR https://github.com/kingsamchen/fawkes/pull/9 差不多算是把这几个点都弄好了。

这个 PR 用了两个 tricks

- cpplint / run-clang-format-diff / run-clang-tidy-diff 三个脚本直接以源码模式集成到项目里；这样外部环境只需要安装 clang tools 即可
- 因为 fawkes 开启了 pch 而 gch clang-tidy 会认为是错误，同时也没有提供 warning option 可以 disable，所以我想了一个 work around，跑 tidy diff 的时候驱动一个 py 脚本把 compile_commands.json 复制一份到 /tmp/tidy-diff 目录下这份删掉 `-i /path/to/pch` 相关的部分

另外因为现在是只对 diff 跑 tidy，所以本地提交的时候快了很多

\#3

因为本来打算开始做 https 支持，于是看了一下 beast/flex demo 和 beauty 的 https 实现

beauty 就是比较传统的：如果提供了 certs 就认为是要走 https traffic，服务就会走 https 的代码分支

beast/flex 比较牛逼，它可以在一个监听端口同时 serve http/https 两种流量；每次建连接的时候嗅探一下是否能完成 SSL 握手，能的话就走 https session。而且同 beauty 几个核心点都要针对处理不同，flex 利用 CRTP 扩展两种 session 处理

不过US tech lead 说我们 pod 里跑的都是 http traffic，https 过了网关之后都被 offload 了，并且和其他服务间的通信有利用 wireguard 做 tls 所以服务自身不需要 serve https trafic，而且证书弄起来多麻烦啊。。。（也是）

这样一来，fawkes 支持 https 可以放后面一些

\#4

开始给 fawkes 加 cors middleware，先重新温习了一遍 cors 的基础原理，然后找到了 gin 的 cors middleware 扩展，看了一下比较 production ready 一些，打算对着借鉴（抄）

---

这周就这样吧，下周见
