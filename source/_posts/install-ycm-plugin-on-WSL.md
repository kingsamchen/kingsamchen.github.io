---
title: 在 WSL 的 VIM 里安装 YCM
categories: PROGRAMMING
date: 2016-10-03 13:52:18
tags: [wsl, bash on windows, ycm]
---
注 1：此处的 WSL 指的是 *Windows Subsytem for Linux*，不是“猥琐流”

注 2：如果是在中国并且没有专用的全局科学上网线路（比如在自己家里），建议找一个类似 SS 的梯子，让 WSL 里的网络操作走代理，提高速度，避免因为某些连接被和谐导致悲剧发生。

如果有 SS，那么进入 WSL 后执行命令

```
export http_proxy=http://127.0.0.1:1080
export https_proxy=http://127.0.0.1:1080
```

对当前用户设置代理。

如果需要使用 apt-get 操作，那么需要首先 `su` 进入 root 账户，在 root 下设置代理。

此处设置的环境变量在某个 terminal 退出后自动销毁，下次进入 terminal 需要重复设置。如果你的代理非常稳定非常快，那么可以考虑改写 profile，避免每次手输。

_**Special thanks to @xqq here**_

### 更新自带的 VIM

因为 WSL 里的 ubuntu 是 14.04 LTS，因此里面附带的 vim 并不满足**最新**的 YCM 的最低版本要求，如果不升级会出现 ycm 工作异常。

```
sudo add-apt-repository ppa:pkg-vim/vim-daily
sudo apt-get update && sudo apt-get upgrade
```

### 安装构建基础依赖

主要包括：CMake 和 python-dev packages

```
sudo apt-get install build-essential cmake
sudo apt-get install python-dev python3-dev
```

### 安装 VIM 插件管理器 Vundle

```
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

插件设置参考 [这里](https://github.com/VundleVim/Vundle.vim)

### 安装 YCM

虽然我们安装了 vundle，但是我们不会直接通过 vundle 装 ycm，因为太慢了。

当然如果你在国外或者有科学上网专线，那就无所谓了...不过谁让我们是穷屌丝呢。

首先进入目录 `~/.vim/bundle`，然后把 YCM 的 repo 拉下来，记得要用 `--recursive`，因为现在的 ycm 依赖的第三方模块太多了...

```
git clone --recursive https://github.com/Valloric/YouCompleteMe.git
```

这个操作对你的梯子是一个极大的考验，如果中途 clone 挂了，就进入 ycm 的目录，接着用 submodule update 去继续拖代码即可。

想当初我的 SS 在这里断了 5-6 次，每一次 connection lost 都让我问候了一遍党和国家...

代码拉完之后还没结束，这个时候打开 `.vimrc`，把 ycm 加到 vundle 的设置列表里，然后打开 vim 走一遍 `PluginInstall`。你应该会看到 ycm 被飞快的安装完了（其实也就校验了一下😁）

### 编译 ycm

源代码拖完了就该轮到编译了

```
cd ~/.vim/bundle/YouCompleteMe
./install.py --clang-completer
```

脚本首先会去下载 clang-3.9，然后走一遍 cmake 的编译，等一会儿就好。

### Debut

来一张最终效果图

![](img/wsl_vim_ycm.jpg)