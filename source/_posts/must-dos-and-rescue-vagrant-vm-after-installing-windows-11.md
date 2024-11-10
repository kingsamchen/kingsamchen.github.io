---
title: 码农安装好 Windows 11 后要做的1001件事，以及如何拯救就电脑上的 vagrant vm
categories: PROGRAMMING
date: 2024-11-10 16:34:06
tags: [windows, vagrant, cpp, telegram, font, scoop, dev drive]
---

系统装的是 Windows 11 Pro 24H2，没有选用 LTSC 的原因是感觉必要性不大而且看某些资料的描述 LTSC 可能并不适合开发者。

另，激活用的是14年从刀老师那边要来的 product license，研究之后发现居然是个企业 MAK 码，有上千次的 activation quota 🫡

### Step 0: 安装驱动

现在硬件越来越牛逼了，系统自带驱动基本都只能做到勉强可用的状态，甚至有时候不装无线网卡驱动连 WiFi 都用不了，所以不管是台式机还是笔记本，装完系统之后第一件事还是把硬件对应的各种官方驱动都给装了。

我用的 ASUS ROG X870E HERO 主板，大头驱动直接用官方的 ASUS Driver Hub 装即可；ASUS 直接往 BIOS 里注入了代码，能在系统安装之后自动弹出 Driver Hub 让你安装驱动，怎么说呢，有点牛逼...

另外现在配件各种 RGB 灯光如果想要关闭的话，需要安装 ASUS Armoury Crate，在里面调整灯光

显卡用的 MSI 超龙 4080S，但是直接用 Nvdia 官方的驱动就行；装完显卡驱动之后双显示器就可以用了 🤣

散热器用的 Valkyrie V360 这个不需要驱动，但是那个显示屏如果想让呈现效果的话需要装一个 Myth.Cool

### Step 0.5: 语言包和输入法

装的如果是中文系统的话这步可以跳过，如果是英文版系统的话，还需要配置以下内容。

进入 **Language & Region**，选择 **Add a language**，然后把中文的 language pack 和 Basic Typing 安装好。

不装这两个中文显示会奇怪，而且内置的中文输入法是没法用。

不过国内用户的话这一步安装会非常考验人品，经常网络连接有问题，全局代理也没用，我猜可能还得调整一下 DNS 啥的...不想折腾的话多试几次，或者干脆装中文版系统得了 🤣

日期时间的显示格式根据爱好调整，但是 locale 是必须要设置的，否则中文字体一样会显示奇怪

进入 **Region -> Administrative** 对话框，下面有个 **Change system locale**

然后把 system locale 选择 Chinese；并且把下面使用 UTF-8 也勾上

![](img/chinese_locale_utf8.png)

这个配置完之后只要屏幕素质和PPI不太差，分辨率也足够，开 200% DPI Scale 之后用微软雅黑作为显示字体也是非常能打的

### Step 1: 简单系统配置

这些事情宜早不宜迟

####  打开 developer mode

这个应该不用解释吧

#### 启用 sudo

Windows 11 专门做的，以前还在用 Windows 10 的时候还要装一个 gsudo，现在就完全不需要了

#### 关闭快速启动

按照 https://www.zhihu.com/question/38604081/answer/2752869354 这里说的做即可

关掉是因为码农的使用方式配合这个经常会有一些奇怪的问题...

而且现在作为使用 96G 大内存的码农来说，平时没事直接待机就好了，我都台式机了还在乎这点能耗嘛...

#### 创建 Dev Drive

注：创建 Dev Drive 可能会要格式化现有磁盘，所以这步要早点做，尤其是如果你没有多个硬盘的情况下

这是 Windows 11 某个版本开始引入的，目标是提升开发者经常用的构建调试工作中的磁盘IO性能。

Dev Drive 本身使用是 ReFS 文件系统，并且 Windows Defender 会对此有优化

跟着 https://learn.microsoft.com/en-us/defender-endpoint/microsoft-defender-endpoint-antivirus-performance-mode 操作就行

因为我单独有块 4TB 的 990 Pro 作为开发盘，所以我直接把这个盘用 commandline 格式化成了 dev drive，并且因为容量足够，所以不用担心扩展问题，因而没有选择 VHD 方案。

注：系统盘是不能创建为 Dev Drive 的

弄完之后能看到不仅文件系统改成了 ReFS，Defender 也启用了 asynchronous scanning

![](img/disk_properties.jpg)
![](img/dev_drive_view.jpg)
![](img/win_defender_dev_drive_scanning.png)

### Step 1: 梯子

如果你不在中国大陆，那这步就完全可以跳过；否则的话这步几乎是每个码农的前置步骤。

我用的还是 clas,至于具体的 gui 客户端就暂时隐去。

现在好多流行的客户端开发者考虑到自身安全性都选择了 archive 了项目，摊手

### Step 2: Windows Terminal + Powershell Core

Windows 11 自带了 Windows Terminal，但是不一定是最新版本，所以可以先升级到最新版 https://github.com/microsoft/terminal

然后安装更适合开发者体质的 Powershell Core https://github.com/PowerShell/PowerShell/releases

### Step 3: Scoop

我选择 scoop 作为 Windows 上的包管理，有自己偏好的比如 winget 或者 chocolate 可以选择自己习惯的包管理

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
irm get.scoop.sh -Proxy 'http://127.0.0.1:PORT' | iex
```

然后配置 scoop；另外我们需要安装 aria2 并让 aria2 成为 scoop 默认下载器

```powershell
# User Env
setx SCOOP C:\Programs\scoop

# System Env
# Need admin privilege
setx /M SCOOP_GLOBAL C:\Programs\scoop-global\
setx /M SCOOP_CACHE  C:\Caches\Scoop\

# No http:// !
scoop config proxy 127.0.0.1:PORT

scoop install aria2
scoop config aria2-enabled true
scoop config aria2-warning-enabled false
```

### Step 4: 基础必要应用

有了 scoop 这些就容易装了；不过 scoop 比较依赖 git 和 7zip，所以这两个要先装

```powershell
scoop install git 7zip
```

有一些应用在 extra bucket 中，所以也需要加上 extra 源

```powershell
scoop bucket add extras
```

然后就一键自动安装：

```powershell
scoop install `
    oh-my-posh posh-git PSReadLine z Terminal-Icons `
    ariang-native bat cmake curl dark difftastic fork `
    gitui grep potplayer less llvm vim ninja nodejs python pkg-config touch which
```

因为是一个 CPP 码农，所以 llvm/CMake/Ninja 是必须的；装 nodejs 是因为博客是 hexo 做的，所以需要...

Python 就很显然了，哪个码农不得写一些 Python scripts 嘛

### Step 5: 写配置

#### git

```shell
[user]
    name = <yourname>
    email = <your-email>

[http]
    proxy = http://127.0.0.1:PORT
[https]
    proxy = http://127.0.0.1:PORT

[alias]
    lg = log --graph --pretty=format:'%C(yellow)%d%Creset %C(cyan)%h%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=short --all
    prune-local = "!prune_local() { git branch -vv | grep ': gone]' | awk '{ print $1}' | xargs git branch -D; }; prune_local"

[core]
    autocrlf = input
    editor = vim

[credential]
    helper = manager

[i18n]
    filesEncoding = utf-8

[merge]
    conflictstyle = diff3

[push]
    default = current

[rerere]
    enabled = true
```

#### vim

```shell
set nocompatible
set modelines=0
set backspace=indent,eol,start
set nobackup
set noswapfile
set autoread
set clipboard=unnamed
set mouse=a
set mousehide
set completeopt=longest,menu
syntax on
filetype on
colorscheme evening
set encoding=utf-8
set number
set smartindent
set autoindent
set smarttab
set tabstop=4
set shiftwidth=4
set expandtab
set nowrap
set ambiwidth=double
set splitright
set splitbelow
set termguicolors       " enable 256color
set cursorline
set showmatch
set sm!                 " highlight matched paren
set hlsearch
set ruler
set novisualbell
set showcmd
set laststatus=2        " always show statusline
set showtabline=2       " always show tabline
set vb t_vb=
```

#### clang-format-diff

项目的 CI 依赖这个

```powershell
$llvm_share="$env:SCOOP\apps\llvm\current\share\clang"
(Get-Item -Path "$llvm_share/clang-format-diff.py" > $null) && ([Environment]::SetEnvironmentVariable('Path', $llvm_share + ";" + [Environment]::GetEnvironmentVariable('Path', 'User'), 'User'))
```

#### 开发相关 Cache 放到 Dev Drive

```bash
# cpm.cmake
setx CPM_SOURCE_CACHE D:\Caches\cpm

# vcpkg binary cache
setx VCPKG_DEFAULT_BINARY_CACHE D:\Caches\vcpkg

# npm
setx npm_config_cache D:\Caches\npm

#pip
setx PIP_CACHE_DIR D:\Caches\pip
```

#### Powershell Profile

直接在 powershell 里运行 `code $PROFILE` 就可以编辑当前用户的 profile 了

```powershell
Import-Module posh-git
oh-my-posh init pwsh --config $env:SCOOP\apps\oh-my-posh\current\themes/ys.omp.json | Invoke-Expression
Set-PSReadLineOption -PredictionSource History

Import-Module "$env:VCPKG_ROOT\scripts\posh-vcpkg"

$env:LANG='zh_CN.UTF8'

# Alias
Set-Alias ll ls
Set-Alias pbcopy 'Set-Clipboard'
Set-Alias pbpaste 'Get-Clipboard'


function Update-Env-Path {
    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") +
                ";" +
                [System.Environment]::GetEnvironmentVariable("Path","User")
}

function proxy.on {
    $env:http_proxy="http://127.0.0.1:PORT"
    $env:https_proxy="http://127.0.0.1:PORT"
}

function proxy.off {
    $env:http_proxy="http://127.0.0.1:PORT"
    $env:https_proxy="http://127.0.0.1:PORT"
}
```

### Step 6: 装字体

码农嘛，有些额外的字体还是要装的

Windows 上字体有得选尽量选 TTF 格式

- [Cascadia-Code/Mono](https://github.com/microsoft/cascadia-code/releases) 这个虽然系统自带了，但是也可以顺带升级一下；我目前 console 主力字体，可以用带 PL 的版本
- [monaspace/Neon](https://github.com/githubnext/monaspace) 我目前写代码主力字体
  另外这个字体在 vscode 中用的话需要搭配如下配置
  ```json
  "editor.fontFamily": "'Monaspace Neon Var',
  "editor.fontLigatures": "'calt', 'liga', 'ss01', 'ss02', 'ss03', 'ss04', 'ss05', 'ss06', 'ss07', 'ss08', 'ss09'",
  "editor.fontSize": 15,    // change size at your will
  ```
- [Sarasa-Gothic](https://github.com/be5invis/Sarasa-Gothic) 作为有时候雅黑不好使的 fallback
- [noto-cjk](https://github.com/notofonts/noto-cjk) 原因同上

### Step 7: 安装 Vagrant

先装 VM，VMWare 已经可以个人免费试用了，而且这玩意儿肉眼可见 Windows 上性能比 Virtual Box 好，所以就先装这个了。

然后安装 vagrant 本体

```shell
scoop install vagrant
```

然后装 vmware 的 plugin

1. 先装 vagrant vmware utility
2. 然后
   ```shell
   vagrant plugin install vagrant-vmware-desktop
   ```

参考 https://developer.hashicorp.com/vagrant/docs/providers/vmware/installation

### Step 8: 配置 Telegram 字体渲染

这个单独拎出来说是因为最新版的 Telegram 支持自定义字体和切换渲染引擎了

这意味着 Windows 可以绕开 Qt 之前经典的字体选择错误导致的字体渲染让人难以接受的问题

`Chat Settings -> Font Family` 这里可以选择字体，我直接选择了微软雅黑

然后

`Advanced -> Experimental Settings -> FreeType Font Engine` 把这个勾上就行

Windows 11 4K 分辨率 200% DPI Scale 下的效果

![](img/telegram_new_font_engine.png)

唯一不足的就是小字体的话看起来总感觉有点肾虚的样子 🤔

---

至于其他 Visual Studio 啊 VSCode 啊，浏览器啊，基本都没啥好提的，按照自己需求装了就是。

因为我是 Office 365 付费用户，所以我有很多配置都是存放在 OneDrive 的，新电脑同步完直接把配置一覆盖就完事了...

### 抢救旧电脑复制来 Vagrant VM

因为这次电脑装的比较仓促，我还没来得及研究如何转移 vagrant vm 老电脑的 970 EVO 就给我拆下来装到新电脑上了。

那就只能硬着头皮上了，因为 vagrant vm 里还有还没 push 的代码变更，所以最好能抢救回来。

1. 装完 vagrant 之后先把对应的 ubuntu 2204 的 vbox 拉下来，这个很重要
2. 进入 `.vagrant\machines\default\vmware_desktop\` 目录下把有几个存有路径的文件的路径给更新一下
3. 因为新旧系统的用户名也变了（我干嘛非要手贱呢...）所以导致 vagrant vm 关联的 vbox 的路径也变了，我发现 vmdk 这个 binary 文件也记录老路径，所以我不敢直接改。
  想了个办法，用 vmware 导入旧 vm，然后会提示基础镜像找不到（vagrant vm其实就是 vbox 基础镜像上跑的），手动找到新路径即可
  然后就发现 vm 能启动起来了
4. 启动一会儿会提示 vm 被锁了，可能有其他进程在使用。
  想了一下这个可能是因为当时太仓促了，我在旧机器上是直接没有关闭 vm 就休眠了系统然后把盘拆下来了...
  解决方案是删掉 `*.vmem.lck` 和 `*.vmdk.lck` 这两个目录里的文件即可
5. 进入系统后还需要注意，如果搞过什么 guest -> host 的网络映射，要把 IP 也更新一下，因为现在新系统 VMWare 分配到的网卡IP也变了

这样一顿操作下来这个 vagrant vm 就算抢救回来了，用了几天也没啥问题 😊
