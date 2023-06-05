---
title: 一周杂记 in Week 1 Jun 2023
categories: CODE-LIFE
date: 2023-06-05 16:18:05
tags: [杂记]
---
本周（05/29 ~ 06/04）是六月份的第一周。

## Life

\#1

这一两年经济低迷，所以六月份一开始 JD 618 店庆就开始了。

半年前就盘算着买个 Bose QC 45 刚好这几天看着价格合适就入手了。

算起来这个是手里第三个 Bose 的耳机了：一个用了快十年的 bose qc 20 在办公室开会专用；三四年前买的 bose earbud 鲨鱼鳍（忘了是不是叫这个名字）因为是入耳式的，呆久了总感觉有点难受；所以这才考虑买个头戴式的 QC 45

整体用下来体验还是非常符合预期的

\#2

本周看了两部电影：

- 环太平洋2 2.5/5 动作场景还行，但是比起1还是有点小打小闹。而且整体叙事就完全不如1，加上金主塞得演员太多，割裂感太重
- 乱 媳妇儿点名要看的，不过就目前就看了一个小时四多分钟，下周看完在评价。

## Work

\#1

这周比较懒散，学习进度不太行

**雪球基金第一课 1. 基金是普通人财富管理的最佳选择**

- 投资重新学起来

**CppCon 2021 | Plenary: Six Impossible Things - Kevlin Henney**

- 讲了很多，不过不是太自成体系

\#2

这周因为在公司业务需要，写了一个直接操作 fd 的文件读写，绕开 FILE* 和 C++ 的 fstream。

原因是我们需要能保证每一个步骤在发生错误时都能拿到确定的错误上下文；直接用 fd 配合 syscall 的话就可以直接用 `errno` 了；不过缺点就是需要考虑的因素比较多，代码写起来会比较长。

改写了一个版本丢到了自己的 Eureka 里，参见 https://github.com/kingsamchen/Eureka/pull/5

\#3

调整了一下之前习惯用的 gcc/clang 的编译告警参数，做了一点加强。

目前是给 esl [试了一下](https://github.com/kingsamchen/esl/pull/7)，回头打算给 anvil 加上，这样以后新工程默认就是新告警参数

\#4

之前用 clangd 最头疼的就是 cmake build conf 切换时候 compile_commands.json 没法自动跟着切。

这周突然发现有不少人遇到了类似的问题，并且有一个 [workaround](https://github.com/microsoft/vscode-cmake-tools/issues/654#issuecomment-592983916)

```json
"cmake.buildDirectory": "${workspaceFolder}/out/${generator}-${buildType}",
"cmake.copyCompileCommands": "${workspaceFolder}/out/compile_commands.json",
"clangd.arguments": [
    "--compile-commands-dir=${workspaceFolder}/out",
		...
],
```

瞬间感觉心情都好许多... 🥱

---

好了，这周就这样，下周见。
