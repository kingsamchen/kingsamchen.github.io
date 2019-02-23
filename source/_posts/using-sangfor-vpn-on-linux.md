---
title: 在 Linux 上使用深信服 VPN
categories: CODE-LIFE
date: 2019-02-23 20:26:29
tags: [linux, vpn, 深信服]
---
因为公司使用 Windows 和 MacOS 的人最多，因此导致两个后果：
1. 连接公司内网需要使用的深信服 VPN 基本只支持 Windows 和 MacOS
2. 后端 golang 大仓代码基本只能在 MacOS 或者 Linux 上提交和调试

求一下交集可以发现，在贵司使用 Linux 作为开发平台的同学应该很难过。很不幸的是，我刚好属于那种在 Linux (虚拟机里跑 Mint) 上写后端服务的人。

公司买的深信服的 VPN 服务，那个客户端虽然官方宣称支持 ubuntu，但是事实是压根不能用，连接成功后一旦有数据包就自动断开。

官方论坛有人反映过类似的问题，得到的回答千篇一律都是推荐使用 Windows 和 MacOS。

然而让我使用屎一样的 MacOS 是不可能的；让我在自己的主力台式机的 Windows 上装深信服的客户端也是不可能的。
<!-- more -->
于是经过一番折腾之后，我找到了一个 workaround：搞一个 vm 跑 Windows，用来运行深信服 VPN；然后 mint vm 将内网地址的网络包转发到 windows vm 上。

### Let the Hacking Begin

环境如下：
- 物理宿主机 Windows 10
- 虚拟机 vm Windows 8 （使用 win8 是因为这个版本相对资源占用少而且支持 hidpi）
- 虚拟机 vm mint

操作步骤如下：
1. vm win8 安装好 sangfor vpn 客户端之后会多一个 sangfor 网卡；vm 自己一开始有一个 NAT 网卡；我们现在再手动加一块 bridge 网卡
    需要手动添加 bridge 网卡的目的是，连通 vm win8 和外网，保证 vpn 可以工作
2. 设置 sangfor vpn 网卡，将网络共享给 NAT 网卡
    注意这一步设置完之后会有两个后果
    a. NAT 网卡因为使用了 sangfor vpn，因此再 sangfor vpn 没有网络联通的情况下 NAT 网卡是无法连接外网的。所以我们前面才手动添加了一块 bridge 网卡
    b. 因为 Windows 自身的策略问题，NAT 网卡的 IP 地址会自动变成 192.168.137.1。
3. 在 vm mint 里首先执行
    ```shell
    sudo ip a add 192.168.137.2/24 dev ens33
    ```
    将 vm mint 的 NAT 网卡也加入到 192.168.137.1 的子网里，保证能和 vm win8 的 NAT 网卡通信。
    这里 `ens33` 是我的 vm mint NAT 网卡对应的 interface name，你根据自己需要更改。
    然后修改路由表：
    ```shell
    # 打一个没必要的马赛克 -..-
    sudo ip r add 172.xx.20.0/24 via 192.168.137.1
    sudo ip r add 172.xx.21.0/24 via 192.168.137.1
    ```
    这样目的地是公司内网的数据包就转发到 192.168.137.1 里了
4. 因为前面 vm win8 的 NAT 网卡共享了 sangfor vpn 网卡的网络，所以可以正确无误的找到下一跳目标。

注：linux 上设置的改动重启后会自动丢失，需要时候需要重新跑一遍。

结果图

![](/img/dashboard-on-mint.jpg)
