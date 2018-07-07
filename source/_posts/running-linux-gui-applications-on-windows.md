---
title: 在 Windows 上运行 Linux GUI 程序
categories: CODE-LIFE
date: 2018-07-06 22:43:29
tags: [ssh, x11 forwarding]
---
自从 Windows 10 提供 WSL 之后，在 Windows 上运行 Linux CLI 程序并不是一个复杂的事情；然而俗话说饱暖思淫欲，既然可以做到在 Windows 上跑 Linux CLI 了，下一步自然想的是在 Windows 上跑 Linux GUI 程序。

之所以要跑 GUI 是因为，无论是 vscode 还是 CLion，写 C++ 的体验都比在 terminal 里开一个 vim 要好太多了，无论你是花了多少时间配置了 `.vimrc`，在 CLion 面前都是战五渣。更何况我买了 Jetbrains 的 All product license，不用用难道留着过年吗。

这里先解答两个常见疑问：

Q：为什么不直接在虚拟机里使用？
A：因为 Linux 对 4K 屏原生支持太糟糕。哪怕 Mint 提供了 double scaling，解决了一些外观上的问题，我在 4K 的环境下一开 vscode 程序就崩溃，而且运行操作明显掉帧。
另外一个问题是，单纯的虚拟机操作和外界宿主太隔离，等于我得配置两套完全一样的环境（比如浏览器，常见的应用，甚至 SS 等），而且和宿主的交互非常不够便利。

Q：为什么不买 macbook？
A：？？你是认真的么？OS X 那么垃圾的系统。再说我要写 C++ server-end 的代码，要是可以用 OS X 我为什么不直接在 Windows 上跑?

另外有一个剧透：我尝试过运行 WSL 里的 Linux GUI 程序，例如 CLion，但是目前 WSL 的文件系统性能过于糟糕，CLion 一个劲的冒错误提示，所以，下面的环境假定是虚拟机里的 Linux 或者一台单独的 Linux 设备。
<!-- more -->
## Linux 端

首先确定安装好 Linux，建议不要用 server 版本，否则你还要自己装 X server 以及各种乱七八糟的东西。

我选的是 Linux Mint 18.2。

接着安装好 SSH Server：

```bash
sudo apt install openssh-server
```

装好后运行 `ps -e | grep ssh` 确保 sshd 在跑。

然后开启 X11 Forwarding。我们的目标是将 Windows 宿主机作为 X-Server，渲染图形界面。

用 root 权限打开文件 `/etc/ssh/ssh_config`，保证如下两个选项：

```
ForwardX11 yes
ForwardX11Trusted yes
```

最后是一个可选步骤：Linux 默认不启动图形界面。

如果你的 Linux 跑在虚拟机里的话，默认不开图形化可以大大节省内存。

因为 Mint 18.2 基于 Ubuntu 16.04 LTS，原生支持了 systemd，所以只需要运行

```bash
sudo systemctl set-default multi-user.target
```

即可。

如果需要恢复，则执行

```bash
sudo systemctl set-default graphical.target
```

另外，在系统运行过程中执行 `startx` 即可手动加载图形环境。

## Windows 宿主端

宿主端需要
1. ssh 终端
2. x-server

最后我选择 MobaXterm，这玩意儿自己集成了 x-server。

注：MobaXterm 由免费版本，并且源代码按照 GPLv3 开放，如果你想要去除限制，可以自己编译一份。

下面列一下几个我自己在使用过程中遇到的问题以及对应的解决方案。

**高 DPI 支持**

将 `%userprofile%\Documents\MobaXterm\slash\bin\XWin_MobaX.exe` 程序的 DPI 设置改为 Application

如下图：

![](/img/mobaxterm_hdpi_override.png)

**MobaXterm 终端 ZSH Powerline 显示异常**

这个原因是字体的问题。

在 https://github.com/powerline/fonts/tree/master/DejaVuSansMono 找到TTF文件，下载安装；然后修改 MobaXterm 的 ssh 终端字体。

**MobaXterm 终端 ZSH 里无法使用 Home/End 按键**

在 ~/.zshrc 加上以下两行

```bash
bindkey "\033[1~" beginning-of-line
bindkey "\033[4~" end-of-line
```

然后利用 `source ~/.zshrc` 刷新一下

**运行过 GUI 程序后退出 SSH 时卡死**

这个问题非常恼火，我研究了好一阵子之后才找到解决方案。

问题的原因是 dbus-launch 卡在 GUI 程序的标准输入输出管道上。

可以在每次退出前杀掉 dbus-launch 进程，当然还有更好的方案。

在 shell profile 里（如果用的 zsh，那么就是 `.zprofile`），加上

```bash
# set dbus for remote SSH connections
if [ -n "$SSH_CLIENT" -a -n "$DISPLAY" ]; then
    machine_id=$(LANGUAGE=C hostnamectl|grep 'Machine ID:'| sed 's/^.*: //')
    x_display=$(echo $DISPLAY|sed 's/^.*:\([0-9]\+\)\(\.[0-9]\+\)*$/\1/')
    dbus_session_file="$HOME/.dbus/session-bus/${machine_id}-${x_display}"
    if [ -r "$dbus_session_file" ]; then
            export $(grep '^DBUS.*=' "$dbus_session_file")
            # check if PID still running, if not launch dbus
            ps $DBUS_SESSION_BUS_PID | tail -1 | grep dbus-daemon >& /dev/null
            [ "$?" != "0" ] && export $(dbus-launch) >& /dev/null
    else
            export $(dbus-launch) >& /dev/null
    fi
fi
```

同样别忘了 reload 一下。

## 尾声

因为 Linux 跑在同一台设备上，网络传输都是在 Loopback 网卡上跑，因此流畅度可以有保证。

同时，只要程序自身支持，运行的 GUI 程序是可以和 Windows 共享剪贴板的。

最后来一张效果照

![](/img/win_as_xserver_final_effect.png)

不仔细看还以为是一个 Windows 程序呢。