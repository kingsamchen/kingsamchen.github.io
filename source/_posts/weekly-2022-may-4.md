---
title: 一周杂记 in Week 4 May 2022
categories: CODE-LIFE
date: 2022-05-29 23:43:18
tags: [杂记]
---
## Life

\#1

这周五全组都出去团建了，跑到余杭的渔佬大搞自助烧烤和一些小活动。

整体上感觉一般，烧烤的食物的质量感觉是真的一般，而且烤架感觉也很敷衍，整体体验还没 2017 年在阿哔时去梅花州体验好。

至于玩的感觉除了射箭之外就更一般了。

还好我们自己带的零食供应还行。

\#2

因为周五团建，所以周六下午才和老婆一起去长龙过夜。

周六晚饭是老婆亲自下厨做炒粉干，除了不太适应长龙的厨房厨具导致糊了不少的粉干之外，粉干的质量没有太大的影响。

不过因为没有搞宽带和 WiFi 再加上那边的手机信号实在太差了，躺在床上基本也没法刷手机。

周日吃了中饭就回来了。

周末阴雨天，湿度大，搞得人很难受，大脑昏昏沉沉的

\#3

Better Call Saul 最终季的上半季完结了。

2160P 的半季资源已经准备妥当了，下周打算开始看先。

不过还有看了一半的 tinker tailor and spy...

## Work

\#1

这周学习了一下 byobu 多 session 的用法

```shell
`byobu list-sessions`

`byobu new-session -s <name>`

`byobu-select-session`
```

以后有必要可以多开 session 干活且可以互不干扰了。

\#2

还学习了 git 如何只 clone 某个分支

```shell
git clone --single-branch --depth=1 -b master REPO_URL
```

\#3

给 Lumper cli 加 UT 的时候需要在 tests 里访问 cli 的某个私有成员函数。

后来想到一个思路是引入一个 friend class `cli_test_stub`，并且这个类是 `cli` 的 子类。

这样一来可以直接通过 `using some_private_fn` 来把 `cli` 某个私有成员函数在子类里变成 public，从而在 test 里直接访问。

更妙的是，甚至可以不用额外给 `cli_test_stub` 增加前置声明，只需要提供一个 friend declaration 即可。

参考 https://stackoverflow.com/questions/4492062/why-does-a-c-friend-class-need-a-forward-declaration-only-in-other-namespaces

\#4

考虑到 Lumper 已经是一个比较正经的练手项目了，于是周六的时候抽了点时间把项目从 Eureka 里独立了出来。

为了保留 commit 历史，我选择的做法是先从 Eureka 里把相关的 commits 导出为 patch 文件，然后编辑文件去掉 `/Lumper` 目录前缀并且规范了一下 commit message。

然后在新的 Lumper 仓库里在把这些 patch 文件打入。

见 https://github.com/kingsamchen/Lumper

---

这周就这样，下周见
