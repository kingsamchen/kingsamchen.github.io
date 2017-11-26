---
title: 使用项目自带的 python 编译 bililive-win
categories: PROGRAMMING
date: 2017-11-26 21:00:53
tags: [chromium, python]
---
众所周知，Google 喜欢拿 python 做各种工具链，比如构建系统；然而 G 家用的又是 python 2.x，并且目测在未来一段时间内都不会做升级，因此相关的工具链环境也被锁死在了 python 2.x。

某直播姬因为用的 chromium 的框架，部分资源文件的构建就依赖 python 2.x。

在系统安装 python 2.x 并且加入环境变量的情况下这不算个问题；然而如果想把 python 3.x 作为主 python 环境，那么构建一定会失败，哪怕项目的 third-party 下自己附带了一个 python 2.6....

研究了一下发现可以强制使用项目自带的 python 2.6：

找到目标项目的 vcxproj 文件，用编辑器（例如 VS Code）打开，搜索 python，确认是调用，将 python 改为指向 third-party 下的 exe。

例如，将 `call python "..\tools\grit\grit.py` 替换为 `call $(SolutionDir)..\third_party\python_26\python.exe`

对于涉及到 cygwin 的调用，就先将 third-party/python2_6 加入环境变量

将 `call "$(ProjectDir)..\third_party\cygwin\setup_env.bat" &amp;&amp; set CYGWIN=nontsec&amp;&amp;` 替换为 `set PATH=..\..\third_party\python_26;%PATH% &amp;&amp; call "$(ProjectDir)..\third_party\cygwin\setup_env.bat" &amp;&amp; set CYGWIN=nontsec&amp;&amp;`

保存后生效
