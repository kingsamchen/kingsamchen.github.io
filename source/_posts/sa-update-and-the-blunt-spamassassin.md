---
title: sa-update 和直率的 spamassassin
categories: PROGRAMMING
date: 2021-07-11 16:38:00
tags: [amavisd, spamassassin]
---

## 背景

amavisd 是 MTA（例如 postfix）流行的 content-filtering 模块，可以提供垃圾邮件试别过滤、病毒邮件检测隔离等功能。

spamassassin 则是 amavisd 使用的垃圾邮件模块。

在目前主流的 Linux 发行版上，这两个系统都会以 systemd 管理的服务运行。

在系统集成过程中遇到一个神奇的问题：amavisd 和 spamassassin 服务会定期挂掉，`journalctl -fu` 的日志都提到了

> - config: no rules were found! Do you need to run sa-update​?

的错误内容

## 处理

解决步骤：

1. 检查 `/var/lib/spamassassin/` 目录下是否有一个对应版本的目录，例如 `3.00402`
2. 如果有的话，删除这个目录
3. 然后先后重启 spamassassin 和 amavisd 服务
<!-- more -->
一般到这服务就可以起来了。

## 为啥咧

因为 spamassassin 是 amavisd 的一个模块，所以 spamassassin 无法启动也会牵连 amavisd

而 spamassassin 无法启动的原因是因为到了零点定时器运行了 `sa-update` 试图下载垃圾邮件的自定义规则文件；这个下载的文件会被存放在 `/var/lib/spamassassin/<version>` 目录下。

但是 spamassassin 不管用户规则是否下载成功，都会创建目录（估计是先创建目录然后下载文件）；而在开发环境上恰巧 sa-update 因为 DNS 无法解析 remote host 始终导致下载失败，留下空目录

而 spamassassin

1. 服务启动时会检查规则目录，发现目录存在就会试图加载规则
2. 但是因为前面 sa-update 下载规则失败，所以有没有规则文件
3. spamassassin 加载规则失败，便报错退出，这也是开头日志种提到的错误来源

前面删除目录后，可以避免 spamassassin 认为存在用户规则，继而避免加载出错。

注：上面的逻辑是根据现象推测的，我没有去翻代码核实；毕竟上了年纪的 perl 代码我也看不懂...

不得不说这代码写的真是个铁憨憨 🤪

## 一劳永逸？

前面提到 sa-update 会被定时器触发，所以还得处理一下定时器。

相关的服务是 `sa-update.timer`，可以用

```shell
sudo systemctl disable sa-update.timer
sudo systemctl stop sa-update.timer
```

直接关掉定时器并且避免定时器随系统启动

另外，spamassassin 的服务描述里显式地依赖了这个定时器，所以默认情况下，即使我们停止了定时器，只要一启动/重启 spamassassin，sa-update.timer 又会被运行。

```shell
sudo systemctl edit spamassassin --full
```

会生成这个服务的本地配置，在配置里去掉 `Wants=sa-update.timer` 然后

```shell
sudo systemctl daemon-reload
sudo systemctl restart spamassassin
```

就可以令服务配置生效。

注：一定要用这个方式来修改 systemd 托管的服务，不要直接修改原服务配置文件

## 吐槽

这篇文章原来的标题叫：Spamassassin is a pile of SHIT 但是转念一想觉得大家维护一个开源项目也不容易...

而且套用 Kelvin 的一句话：“在犯傻的时候大家傻逼的程度都差不多”，所以也没有必要这么苛责贡献者。

另外，在我的开发环境下 `sa-update -vv` 可以看到更新失败是因为无法解析 remote host，但是不知道为什么请求的 remote host 始终会带一个诡异的前缀。后续如果要从根源上解决这个问题，估计这会是一个入手点。

但是我实在不愿意再继续深究下去了，我觉得我最近做的事情越来越像一个 DevOps 了 🙄

最后，仅以此文纪念我当时被耗费的数个小时
