---
title: 自动开始安装 Inno Setup 打包的安装程序
categories: PROGRAMMING
date: 2017-01-14 01:08:40
tags: [inno setup, installer]
---
有时候我们希望用户执行安装程序后，跳过路径选择等一系列确认，自动开始安装，已尽可能减少等待时间。

最明显的例子就是，用户在已经安装程序的情况下，下载了新版的安装包，需要执行更新操作。因为安装路径、设置选项等信息早在用户首次安装时就已经确定，升级安装过程中完全可以跳过。

某科学的直播姬在加入自动更新功能的同时，就需要安装包具备上述能力。

OK，那么如何在 inno setup 中实现自动安装？

### Step 1

Inno Setup 提供了 `ShouldSkipPage(PageID: Integer)` 函数，这个函数会在安装的不同阶段被调用，并且参数是当前安装程序页的 id，函数返回 `True`，则表示跳过此页。

于是只要重写这个函数，在更新模式下，自动跳过除了 `wpInstalling` 的所有页。

但是如果只这么做，会发现安装程序还是无法自动开始安装，它会停在 `wpReady` ，即确认页。

这是 a feature by design，因为 inno setup 的作者觉得不让用户确认就自动开始安装是一个很 evil 的行为；所以即使在 `ShouldSkipPage()` 里选择跳过确认页，inno setup 也会自动忽略这个要求。

真他喵的体贴啊。

既然只能手动点击安装，那我们就通过发送按钮点击事件来模拟用户点击就好了。

### Step 2

在函数 `CurPageChanged(CurPageID: Integer)` 中检查当前页，如果已经到了确认页，就发送按钮事件。

```pascal
if CurPageID = wpReady then begin
    param := 0 or BN_CLICKED shl 16;
    PostMessage(WizardForm.NextButton.Handle, CN_COMMAND, param, 0);
end;
```

这里注意几个坑，按我当时被踩的先后顺序：

1. 如果你的安装程序定制了 UI，那么极有可能会隐藏原生的 next button 并且提供一个自己美化过的安装按钮；如果真的是这样，那么请通过设置 `WizardForm.NextButton.Height := 0` 来隐藏他，而不是直接 set invisible。因为后者会导致这个按钮直接不响应他的事件。
2. 发送消息采用 `PostMessage`。我一开始用的 `SendMessage` 并没有成功，具体原因未知...

### Rants

Inno Setup 这种试图通过提供一系列设置和受限的脚本的方式来生成安装包的行为，固然可以降低使用门槛，但是也增加了自定义的难度和复杂度。Joel Spolsky 那篇洞察一切的 *the law of leaky abstractions* 其实已经说明了一切。

其实如果不是接手项目的时候安装包早就已经固定了，我很可能自己就用 C++ 给撸了，如果真这样，也没有这么多七七八八的事情...

至于我为什么会亲自上阵做安装包...还不是因为没有人愿意做，并且之前的 iss 脚本质量实在一般，都快大坑小坑落玉盘了，总得有个人出来 clean the mess/ass，你说对伐（真怀念当年不想搞了可以把脏活直接丢给老大的日子...）。