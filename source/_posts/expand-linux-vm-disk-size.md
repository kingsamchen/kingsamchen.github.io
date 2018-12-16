---
title: 虚拟机 Linux Mint 磁盘扩容
categories: CODE-LIFE
date: 2018-12-16 17:39:19
tags: [linux, mint, 磁盘, 扩容]
---
之前在虚拟机里创建 mint 时磁盘就给了 20G 的 SSD，最近发现可用容量捉襟见肘，必须要扩容一波。

摸索了一下发现给虚拟机里的 linux 磁盘扩容并不是那么简单直白，至少有几个坑，所以稍微做了一些记录，以备日后不时之需。

### Step 0：通过虚拟机给系统增加磁盘容量

这一步比较简单，VMWare 的话直接在虚拟机属性里可以进行 expand；VirtualBox 应该类似。

### Step 1: 装配增加的磁盘容量

这一步比较麻烦的原因是，如果你的 linux 和我一样一开的时候使用默认安装，那么一般主分区后面会跟着一个 swap 分区，导致我们新增加的磁盘容量不和主分区连续。

所以我们要做的是把 swap 分区挪到最后。

首先安装 gparted，这玩意儿带 GUI，用起来也很直观。

Mint 下直接利用

```shell
sudo apt install gparted
```

装完后记的用管理员权限运行。

然后，首先将 linux-swap 分区停掉，操作是：右键，然后选择 swapoff

注：下面的操作在 gparted 中会是一个模拟操作，所有操作完之后再进行 apply。尽量别中途 apply！

接着删除这个 linux-swap 分区，然后选择 linux-swap 的父分区，进行 Move / Resize。

通过调整示意图中的容量条，将这整个分区挪到最后，保证我们新分配的容量分区和主分区连续。

再接着选择主分区，一样是利用 Move / Resize，进行容量扩增。

接着别忘了重新创建回我们的 linux-swap。

最后选择 apply changes 即可

### Step 2：重新启用 swap

首先记录现在的 linux-swap 的 UUID。

然后用管理员权限打开 `/etc/fstab`，找到记录 swap 的项，更新 UUID。

最后重启系统。

重启后检查 swap 分区是否已经真的 active 了。这一步很重要，不然你会发现扩容后系统启动非常慢（因为 `/etc/fstab` 里的记录有问题）

检查可以通过系统自带的磁盘工具，看看 linux-swap 是否是 active 状态就行。
