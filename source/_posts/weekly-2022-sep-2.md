---
title: 一周杂记 in Week 2 Sep 2022
categories: CODE-LIFE
date: 2022-09-12 23:58:21
tags: [杂记]
---

本周是9月第二周，并且周末是中秋假期。

## Life

\#1

上周说到这一次的体检报告不容乐观，所以一边开始严肃的控制晚餐的热量摄入，一边加量运动。

中秋假期这个周末算了一下加起来的跑步里程差不多有12~13KM了，算上中间穿插的力量训练本周的热量消耗应该不算少了；但是体重称很不给面子的基本还是在 71 ~ 72KG 上下浮动...

想瘦到 70KG 以内（说起来这个是很早之前的目标了）真是难呀 😩

\#2

因为周末是中秋假期，周六下午岳母来家里一起过中秋，晚上老婆在家做了火锅。

味道是真不错，就是感觉这热量有点大...

吃完后三人一边看电视一边闲聊，岳母还给讲了好多老家县政府的一些八卦，以及我知道了初中某个女同学在她爸的关系运作下，从一开始是某个乡的小学体育老师升级到了数学老师...最后又升到了某个乡镇建设所副所长。

先按下喜闻乐见的“你的数学是体育老师教的”梗，感叹一句小地方真是关系户横行啊

\#3

因为老婆恰好周一 120 执勤，所以周一一个人在家看了 _Turning Red_

老实说我能 get 到这部东亚家庭教育文化的点，但是我个人不是很喜欢这电影，主要是感觉还是有点低幼...

## Work

\#1

假期期间研究了一下 clang-format-diff.py，期望是能够给 git 加一个 pre-commit hook 自动检查修改的源码是否符合 clang-format

经过了一番折腾之后发现：

1. git for windows 在这些 hooks 中也是会以 bash 执行脚本，所以不用考虑是否要针对平台写两份
2. 不同平台的 clang-format-diff.py 放置的位置不一样，所以一开始希望通过自动查找这个文件的路径被证明不实用而且坑太大；最后转而将这个文件所在的目录加入到 `$PATH` 即可
    在 bash 环境下只要这个文件有可执行权限，就可以直接被执行到
3. 可以将 hook files 放到例如 `.githooks` 目录，然后设置 repo 级别的 config
    ```shell
    git config core.hooksPath .githooks
    ```
    这样就可以让这个目录被 git 自身托管；避开了 .git 目录下修改无法被 git 自身管理的问题。
    不过其他人还是需要手动执行一遍 git config

```shell
#!/usr/bin/env bash

echo -e "Running clang-format-diff...\n"

fmt_diff=$(git diff --no-ext -U0 --no-color --relative --cached | clang-format-diff.py -p1)

colorize_diff='
    /^+/ { printf "\033[1;32m" }
    /^-/ { printf "\033[1;31m" }
    // { print $0 "\033[0m"; }'

if [ -n "$fmt_diff" ]; then
    echo "$fmt_diff" | awk "$colorize_diff"
    echo -e "\nPlease re-format above files before commit"
    exit 1
fi

echo "clang-format-diff passed"
```

git diff 增加了几个参数

- `--no-ext` 是避免某些环境下设置了 external diff 并且这个 diff 输出格式不满足预期，例如 side-by-side 显示
- `--relative` 是保证 diff 中的文件路径一定是相对于当前目录的，这很重要，否则 clang-format-diff.py 脚本找不到文件
- `--cached` 是为了保证只检查待提交的修改部分

另外专门研究了一下如何给输出增加高亮，提供一点点人性化

\#2

简单看了一下 absl/strings/strip 的源码，其实主要也是这部分实现太简单了。

然后给 esl 增加 strings/trim 的相关函数，目前写了一半

\#3

本周知识学习进度

- **透视HTTP协议** | 正常刷新，按进度差不多下周就能结束了
- _CppCon 2020 | Exceptions Under the Spotlight - Inbal Levi_ | 没有一开始想的深入，基本只讲了一下现有的 exception 机制的 sad path 下的性能劣势和展望未来。学到的新东西很少
- _Software Engineering at Google | 11. Testing Overview_ | 看了十几页，有不少共鸣。因为很多实践我们也是这么做的

本周学习的时间比起上周要少了一些，下周需要加强。

\#4

开始把之前写的 abseil 的源码分析往 blog 上搬。

往后每周搬运一片吧

---

本周就是这样，下周见
