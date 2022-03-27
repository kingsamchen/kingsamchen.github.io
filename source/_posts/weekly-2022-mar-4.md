---
title: 一周杂记 in Week 4 Mar 2022
categories: CODE-LIFE
date: 2022-03-27 21:24:19
tags: [杂记]
---
## Life

- 本周看了三部电影：_Cruella_, _Sully_ and _The Batman_
  Cruella 是周中看的，讲道理我挺喜欢这种叙事风格的影片，而且故事也比想象中的有意思。但是最后半段不知道为啥感觉水准突然下降了。
  Sully 是周六家庭影院和老婆一起看的，看完才发现是东木老爷子的电影。老爷子拍主旋律是真的稳啊，连我老婆这种故事好看最重要的人都看的津津有味。
  和同学约了一周时间才定的27号周日中午看的 The Batman。和诺兰的版本有点类似，可以抛开超级英雄的主题看，故事还不错，各种色彩构图都做得很棒，但是节奏实在有点偏慢了，而且感觉有的地方是没有必要的慢。
  同样是三个小时的 Dune 的节奏把握的就很好。另外这次 The Batman 想探讨的东西其实之前类似的美剧看了也不少了，比如我很喜欢的 Daredevil 三季，尤其是第三季。所以整体上这部 The Batman 还是没有到我的预期，可能我的预期太高了吧...
- 去年还剩下一天年假，并且得在3月结束前用掉，所以没法用来请清明前周六的调休日了。不得不说公司有些规定是真的恶心啊
  本来打算3/28周一请假的，但是因为有个大的 MR 周五没有 merge，所以心里总是放心不下，想周一让他合进去了再择日休假...
- 墙内疫情，多地开花。并且上海愈发严重，周日晚上终于宣布要封城了，真是有意思呢。
- 这周求生之路耍的挺开心的.... 🤣

## Work

- 上面说的大 MR 就是把目前项目用的 CPR 打成 RPM 依赖包，并且调整目前所有的 Makefile 让它们正确的依赖到这个 cpr 上。
  整体上到不复杂，也学习了如何构建 runtime / devel RPM 依赖包，但是因为涉及的范围实在太广了，几乎所有的 Makefile 都改了一遍...orz
- 这周把 Lumper 的 CLI 部分写完了，sub-command 的模拟做的非常到位。
  代码可以看这里 https://github.com/kingsamchen/Eureka/blob/master/Lumper/lumper/cli.cpp 。另外赞一下 p-ranav/argparse 这个库，真的是非常优秀非常优雅的 cli lib，在之上做二次封装也非常容易。
  现在 work on run command in child process 的部分，打算抽一个 `base/subprocess` 出来。并且因为需要用到 namespace 相关的东西，所以不能直接用 `fork()` 而要用 `clone()`。而 `clone()` 的文档真的是又长又没有体系，所以还在学习如何使用中。
  冷知识：Golang 的 `os/exec.Command` 在 Linux 上用的就是 `clone` 而非 `fork`，所以它才支持启动进程时设置 system namespace attributes
- 看完了 _Database Internals chapter 1_
  这章整体上属于 overview，偏综述类；暂时没有什么太细节的东西。

---

这周就这样，下周见