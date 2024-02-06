---
title: 一周杂记 in Week 1 Feb 2024
categories: CODE-LIFE
date: 2024-02-05 23:12:56
tags: [杂记]
---
本周（01/29 ~ 02/04）是2月份的第一周，也是春节假期前的最后一周。

## Life

\#1

媳妇儿这周五下午考雅思口语，周六笔试

尽人事，听天命

希望能达成既定目标，不然以我媳妇儿现在的心气，未必愿意再重新学习再考一次

\#2

周四 Roro 因为得了公司的 star 请大家吃衢州菜

因为下着小雨其实通行不是很方便，而且那桌菜我觉得前面的主食烤饼最好吃 🤣

吃到7点半就先走了，得回去给媳妇儿买KFC，因为媳妇儿周五要考雅思口语

天下着小雨，虽然有电动车雨衣但是没带伞，淋了一点雨。。好在 KFC 门店离家非常近，不然估计我回去就发火了

\#3

这周日是狗日的调休，不过因为 WFH 了所以也还成吧...

\#4

这周因为媳妇儿要考试没有家庭影院

自己就抽了点时间继续看美剧 **帝王计划：怪兽遗产**

## Work

\#1

本周学习进度：

**深度学习入门 | 2. 感知机**

- 介绍了与门、或门和与非门这种单层感知机，并且通过叠加单层感知机，可以构建出多层感知机来；例如可以通过叠加与门或门与非门实现异或门这种非线性感知机
- 通过叠加层，感知机能实现更灵活的表现形式，甚至能直接实现计算机

**价值投资实战手册 | 1. 正确面对股价波动**

- 公司市值 = 市盈率 × 净利润
综合上看，普通人最好选择 1) 当前市盈率是公司历史低点 2) 不在意当前市盈率，以未来超额市盈率为意外惊喜，主要着眼于企业自身的利润增长
- 还是要穿越迷雾，看清看重企业内在价值，顺带利用股价波动，而不是被股价左右情绪

**CppCon 2021 | C++11/14 at Scale: What Have We Learned? - Vittorio Romeo**

- 主讲 11/14 在 bloomberg 的落地过程，即：how to adopt a new feature in a company wise
- 他们把 features 分成了三档：safe | conditionally safe | unsafe，safe 可以快速提一下，组员们的聪明才智很快就能弄明白怎么用；conditionally safe 主要讲清楚在什么情况下用，避免不正确的使用导致问题；unsafe 的特性不做强求，仅针对部分有需要的人展开，并且只展开有必要的集合
- 落地过程感觉可以借鉴

**CppCon 2021 | Automatically Process Your Operations in Bulk With Coroutines - Francesco Zoffoli**

- 如何基于 coroutine 实现慢操作（IO etc）的批量操作来提升性能
- 还不错的一个 talk，但是 coroutine 的部分用的是简单的概念原理性impl，所以你自己想用的话还得看看自己项目选择的设施里有什么可以做到类似的

**CppCon 2021 | Failing Successfully: Reporting and Handling Errors - Robert Leahy**

- 先讲大多数操作都可能发生错误，所以错误汇报非常重要，并且不应该丢失一些重要的上下文信息，`atoi()` 是一个反面例子
- 然后从 error_code 到 exception 展开讲了常见用法，以及一些实际工程场景应该要如何使用这些机制来汇报错误
- 小哥演讲很有激情

**Nobody Needs Reliable Messaging** https://www.infoq.com/articles/no-reliable-messaging/

- 几十年前的老文章了，里面针对的还是早就不用的 WS-*/SOAP 技术栈，不过核心点是通用的，那就是：如果消息可靠性在业务层面就很重要那么就放到业务来做，这样有序和exactly-once都可以得到很好的保证；框架/中间件级别的 reliability 只能做到 generic 而且会有很多坑

**GotW #100 Solution: Preconditions, Part 1** https://herbsutter.com/2021/02/25/gotw-100-solution-preconditions-part-1-difficulty-8-10/

- if a precondition is violated, its caller’s fault and they need to fix it.
- Remember that your function’s full effective precondition is the precondition you write plus all its implicit prerequisites. Those are: (1) each parameter’s type invariants, (2) any preconditions of other function calls within the precondition, and (3) any defined results of function calls within the precondition that would make the precondition false.
- If your checked precondition has a subexpression with its own preconditions, make sure those are checked first.

\#2

这周修复了 esl 在 ubuntu-22.04/gcc-11 下 tests 失败的问题，原因是 trivial class 使用 `= default` 作为函数体后，就算显式指明了 `noexcept(false)` 也会被 gcc 认为是 nothrow move constructible。

这个问题仅在 gcc 出现，没办法只能手动加上了函数体作为一个 workaround

另外这周在 ubuntu-2204 上把 llvm/clang 整套工具链升级到了 -17，导致 esl 挂了一堆新的 tidy checks，所以也顺带做了一次 fix

完整的 PR 在：https://github.com/kingsamchen/esl/pull/12

另外发现 CMake preset 模式可以使用 `--fresh` 指明跑一个全新的 flow，非常贴心

\#3

琢磨了一下给 vim 做了一个配置，虽然平时不用 vim 写代码，但是偶尔还是会用它临时改点代码或者配置文件啥的，所以感觉还是需要加点配置

```
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

注意到强制开启了 terminal-256color；然后发现 byobu 中居然之后 vim 只能以黑白显示

搜了一下发现默认情况下 byobu 的后端 tmux/screen 都不支持 256 color，需要手动开启

```
set -g default-terminal "xterm-256color"
set-option -ga terminal-overrides ",xterm-256color:Tc"

bind-key -n Home send-keys C-a
bind-key -n End send-keys C-e
```

前两行指定之后在 byobu 里没法用 Home/End 了，所以手动加一下 bind-key，这样体验基本和之前没差，又能以 256color 运行

\#4

之前发现我们某个接口因为请求结构里会带有 big binary，以及某个 pb 定义的递归结构，导致 JSON -> Proto Msg 非常慢，不到30MB的数据这个转换操作要跑将近一分钟...

protobuf issue 转了一圈发现有类似的反馈但是官方认为这个使用不是常规情况所以他们不会专门 fix

但是我们的业务得解决这个问题...

和 tech lead 讨论了一下决定改成：client 发送 http multipart/form，直接把 proto raw binary 发出去；server 拿到之后直接 Parse as proto messsage

写了个 test 验证这个方案可行并且确实整体处理用时能缩短到几百毫秒后就开始改造。

server 这边直接加了一个 v2 的 endpoint，复用了一下之前写的 multipart reader 很快就弄完了；结果发现因为我们神经病的在 cpr 上有封了一层神经病的 http-request-util，导致没法直接用 cpr 的 multipart

考虑了一下直接又顺手加了一个给 client 用的 multipart，允许直接生成 request body，这样就可以挂接到那托神经病的 http-request-util 上了。。。

---

好了这周就这样，下周春节见
