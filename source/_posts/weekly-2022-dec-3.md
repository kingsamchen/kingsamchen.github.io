---
title: 一周杂记 in Week 3 Dec 2022
categories: CODE-LIFE
date: 2022-12-19 15:40:46
tags: [杂记]
---
本周是12月第三周，也是屁事贼多的一周。

## Life

\#1

周五（当天 WFH）早上热醒一次，8点起来后感觉不舒服，量了体温37.9摄氏度低烧，外加全身酸痛。发现另外有两个同事也有类似的症状，一合计，估计我们新冠阳性了。

本来想上午继续工作下午再看情况是否休息，结果 syncup 还没结束的时候就感觉头疼得越来越厉害，酸痛感也越来越强。

请了假之后吃了粒泰诺就扛不住睡觉去了。

周五这天大部分时间都在睡觉，着实难受。到了周六只变成了低烧，全身酸痛也没有了。

一看群，周围同事也纷纷开始发烧，已经止不住的爆发了...周六公司的抗原寄到了，测了一下确实阳性...

不过经过前两天高峰期，基本就进入恢复期了。虽然媳妇儿也开始发烧了...

\#2

最近这经济是真的差，不管是 US 还是 CN。

这理财收益一个劲的跨跨掉，有些都已经又一次转负了。

US 经济预计23年下半年触底，希望是真的，毕竟公司的股价跌这样 RSU 已经缩水不少了...

至于 CN 嘛...有总加速师/总烂尾师在，谁知道呢。

\#3

看了世界杯决赛，真特么一个叫一波三折精彩绝伦荡气回肠。

恭喜梅西在最后一届世界杯成功圆梦。

\#4

这周就看了 Fantastic Beasts: the secrets of dumbledores

感觉一般吧，就是很多重点不清，强行续的意思。而且米叔演格林德沃确实没有那种疯魔的感觉。

## Work

\#1

本周学习进度

- Designs, Lessons and Advice from Building Large Distributed System
  Google 在分布式系统架构方面的分享
- A Beginner's Guide To Scaling To 11 Million+ Users On Amazon's AWS
  基于 AWS 从1个用户如何 scale 到千万用户（基本属于 AWS 的广告）
  不过也有一些架构设计的原则也挺适用
- CppCon 2020 | The Science of Unit Tests - Dave Steffen
  UT 方面的科学建议；这个 talk 很有趣，host 很有意思
- CppCon 2020 | Build Everything From Source: A Case Study in Fear - Dave Steffen (Light talk)
  这是一个 light talk。作者提到他们遇到切换工具链时某个 3rd party lib 编译参数有问题，最后全链路源码编译解决
- CppCon 2020 | Structure and Interpretation of Computer Programs: SICP - Conor Hoekstra
  SICP 安利会，a.k.a LISP 为 C++ 未来发展指明方向
- CppCon 2020 | Reasoning with function signatures - Gabriel Aubut-Lussier (Light talk)
  这个 light talk 我完全没搞懂在讲啥...
- 大规模分布式存储系统 | Chapter 13 云存储
  整本也完结了。这书确实也学到了不少东西
- Software Engineering at Google | 18. Build Systems and Build Philosophy
  看了十来页吧，章节起了个头

\#2

因为公司项目有用 cpplint 做一些基本检查，然后在 make parallel 模式下错误信息会被掩藏，所以花了点时间研究了一下怎么高亮错误部分。

最后学习了一点基础的 AWK 知识之后给了一个还靠谱的方案。

```shell
#!/usr/bin/env bash

# awk regex doesn't support \d
# we also need to return lint result code transparently.
colorize_lint='
    BEGIN {
      rc = 0
    }
    /\w+\.(cc|h):[0-9]+:/ { printf "\033[31m --> "; rc = 1 }
    // { print $0 "\033[0m" }
    END {
      exit rc
    }'

function run_cpplint() {
  root=$1
  filter=$2
  # cpplint by default output to stderr
  cpplint --quiet ${root} ${filter} *.h *.cc 2>&1 | awk "$colorize_lint"
}

action="$1"
case "${action}" in
  --cpplint)
  run_cpplint "${@:2}"
  ;;
  *)    # unknown option
  echo "unknown action ${action}"
  ;;
esac
```

\#3

本周周末因为一直处于恢复期，脑子也不大愿意动，所以没怎么写代码 XD

---

好了这周就这样，下周见
