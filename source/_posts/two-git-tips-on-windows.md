---
title: Windows 上使用 Git Tips 两则
categories: CODE-LIFE
date: 2019-10-19 17:12:14
tags: [git, powershell]
---
## Windows Terminal 中 git log 显示 UTF-8 编码的中文

这个准确的说其实是 powershell 自己的问题。

打开当前用户对应的 powershell 的 profile 文件，如果没有就创建一个，目录一般位于 `C:\Users\<user>\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1`

添加：

```powershell
$env:LANG=zh_CN.UTF8
```

然后重启 powershell 即可。

## Filename too long 导致 checkout 失败

原因是 git 默认使用旧版的 Windows API，对于路径的支持最大为 `MAX_PATH`（多么熟悉的宏）。

解决方法是直接开启 git 对 windows 的长文件名支持，执行

```
git config --global core.longpaths true
```
