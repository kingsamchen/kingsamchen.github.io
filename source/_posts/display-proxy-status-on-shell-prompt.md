---
title: Display Proxy Status on Shell Prompt
categories: PROGRAMMING
date: 2025-06-10 14:11:30
tags: [shell, powershell, zsh, byobu]
---
因为众所周知的原因，在 china mainland 的开发者必须要精通各种代理的用法。为了方便时常在 terminal 中拉取/构建一些开源项目，我在开发环境，不管是 Windows 还是 Linux/Ubuntu 里都配置了函数来开关 terminal 中全局的 http(s)_proxy：

```powershell
# Powershell $PROFILE on Windows

function proxy.on {
    $env:http_proxy="http://127.0.0.1:10080"
    $env:https_proxy="http://127.0.0.1:10080"
}

function proxy.off {
    $env:http_proxy=""
    $env:https_proxy=""
}
```

```shell
# .zshrc on Ubuntu

function proxy.on {
    export http_proxy="http://127.0.0.1:10080"
    export https_proxy="http://127.0.0.1:10080"
}

function proxy.off {
    unset http_proxy
    unset https_proxy
}
```

但是有时候开了全局 http(s) 代理之后会忘记自己是否当前处于代理之中，所以如果能在 shell prompt 中提示当前是否打开全局代理就更好了。

借助 chatgpt 实践了一下，发现其实都很容易解决。

### oh-my-zsh for zsh

因为我习惯用 oh-my-zsh 的 ys 主题，所以直接以它为例子

编辑 `~/.oh-my-zsh/themes/ys.zsh-theme` 加入

```shell
local proxy_status='$(proxy_status_prompt)'
proxy_status_prompt() {
        if [[ -n "${http_proxy}" ]] || [[ -n "${https_proxy}" ]]; then
                echo "proxy:on"
        fi
}
```

颜色什么的可以自己调整。

并且在最后面的 `PROMPT` 变量定义中加入

```diff
- [%*] $exit_code
+ [%*] ${proxy_status} $exit_code
```

最后别忘了 reload shell

### oh-my-posh

我在 powershell 的 oh-my-posh 里也习惯用 ys 这个主题，但是需要注意的是不要直接修改 oh-my-posh 的 original theme file，因为每次升级你的修改就会丢失。

官方推荐的做法是首先导出你使用的主题到个人目录：

```powershell
oh-my-posh config export --output ~/.ys.omp.json
```

然后编辑这个导出文件，在 `"type": "prompt"` 这个 block 里的 segments 按照你喜欢的顺序加入

```json
{
    "foreground": "darkGray",
    "style": "plain",
    "template": "{{ if or .Env.http_proxy .Env.https_proxy }} proxy:on{{ end }}",
    "type": "text"
},
```

颜色什么的可以自己调整

然后 `$PROFILE` 里使用我们自己编辑过的主题就行

```powershell
oh-my-posh init pwsh --config ~/.ys.omp.json | Invoke-Expression
```

同样别忘了 reload profile

---

这俩方案有一个同样的缺点：修改都是跟着主题走的。

意味着哪天想换一个主题需要对新主题做一个修改。

目前我不知道怎么解决这个，但是我个人习惯而言并不怎么换主题，所以对我来说不是什么 issue

### Bonus：改 byobu 的配置文件

其实一开始的时候我第一想法是通过改 byobu 的配置文件来展示 proxy status。

这里也顺带提一嘴怎么定制 byobu 的 status line 好了。

创建目录

```shell
mkdir -p $HOME/.byobu/bin
```

```shell
cat > $HOME/.byobu/bin/10_proxy_status <<EOF
#!/bin/sh
if [[ -n "${http_proxy}" ]] || [[ -n "${https_proxy}" ]]; then
        echo "proxy:on"
fi
EOF
```

注意开头一定要以 `n_` 命名，代表运行间隔时间（秒）

然后用 `chmod +x` 让 shell 有 execute 权限

**最重要的一步**：允许 自定义 status notification：

1. byobu 中按下 F9
2. 选择 Toggle status notification
3. 选中 custom
4. Apply

---

因为 byobu 做了不少集成所以调整起来比较方便。

如果使用的是原生 tmux 的话建议还是自己研究一下 🤣
