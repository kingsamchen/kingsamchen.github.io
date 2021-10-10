---
title: himsw - Hey I aM Still Working
categories: PROGRAMMING
date: 2021-10-10 23:25:19
tags: [windows]
---
因为公司的安全政策要求，公司发的笔记本会在5分钟没有操作后自动锁住当前账户，并且这是由预装的安全组件强制保证的。

（不要想着可以把安全组件卸载或者禁用了，会被 IT 查水表的）

有时候加着仓或者用手机看个新闻就超时锁屏了，总有种摸鱼被发现的感觉，不太好。

更蛋疼的是，WFH 的时候只能用公司发的笔记本；而每次锁屏后都会导致 VPN 掉线需要重连，并且有概率会无法重连需要重置网卡....

没办法只能自己写了个小工具，在3分钟没有操作后进入模拟工作状态，定时发送键盘按键消息；同时监听鼠标和键盘消息，如果出现人为操作，就退出模拟状态。

经过一段时间的体验，基本实现了初衷，摸鱼再久也不会被发现了 😁

---

项目代码完全开源，可以在[这里](https://github.com/kingsamchen/Eureka/tree/master/hey-i-am-still-working)查看

核心就三点：

1. 用 `GetLastInputInfo()` 获取程序上次获得输入的时间；用来判断 idle duration
2. 进入 simulation state 之后定时通过 `SendInput()` 发送键盘事件；为了避免干扰，选择了 ScrollLock 这个按键
3. 利用键盘钩子和鼠标钩子监听全局的键盘/鼠标事件；有人为活动后，自动退出 simulation state

因为用到了系统的 API 所以目前只支持 Windows，估计也不好移植到其他平台。

一开始 GUI 是用 nana 做的，后来发现如果要支持 tray icon 的消息处理还要自己对窗口做一遍 sub classing...

索性弃用 nana 用 native win32 做了个 dialog，起码各种 message handling 会灵活一点。

因为是自家用，所以也不提供功能选项定制啥的，毕竟用不到啊。

最后再吐槽一下：我是真不喜欢做 UI

<!-- more -->
