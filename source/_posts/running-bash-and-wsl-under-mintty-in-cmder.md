---
title: 在 cmder 里以 mintty 为终端的方式运行 bash 和 wsl
categories: CODE-LIFE
date: 2018-11-24 23:26:04
tags: [cmder, mintty, bash, wsl]
---
在 cmder 里如果不指定运行的 terminal 则会默认使用 windows cmd，而这在使用 git-bash 或者 wsl 时会经常遇到问题：
1. 性能底下导致交互出现显著延迟（这个发生在使用 tig 等重型 bash 命令或者使用 wsl中）
2. 容易因为环境属性问题导致乱码，比如 cmder 里直接运行 git-bash 的 tig 会发现提交历史的分割线都乱码了

神奇的是 cmder 支持将某个终端内嵌到自己的宿主界面里，这样就可以在 cmder 一个程序（单指窗口，非进程）里跑多个终端环境了

对于使用 mintty 跑 git-bash，假设已经安装了 git for windows，则只需要设置这个人物属性为

```
%ProgramFiles%\Git\usr\bin\mintty.exe /bin/bash -l -new_console:d:%userProfile%
```

即可。

而对于 WSL，先安装一下 wsl-terminal，然后在 cmder 里新建一个 task，启动内容为：

```
"C:\Programs\wsl-terminal\bin\mintty.exe" /bin/wslbridge -t /usr/bin/ssh-agent /bin/zsh -l -new_console:d
```

即可。

