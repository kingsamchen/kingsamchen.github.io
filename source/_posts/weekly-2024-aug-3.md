---
title: 一周杂记 in Week 3 Aug 2024
categories: CODE-LIFE
date: 2024-08-19 20:48:37
tags: [杂记]
---
本周（08/12 ~ 08/18）是8月份的第三周，气温渐渐有点下降的趋势了，算是个好兆头。

## Life

\#1

周四晚上和 iker 在微信群里闲聊提到要不提早看最新的“异形：夺命舰”，择日不如撞日，就周五中午请两个小时假去看好了 🤣

然后过了一会儿 iker 说：哎呀群里有个HR...

我：....

然后私聊 iker 反正其他人都不响应要不干脆我们俩偷偷去，在群里演一下HR好了 😂 于是我们就买了周五中午的票，但是在群里说哎呀既然如此那就算了吧，都约不到人还是换时间好

因为周五 WFH 所以11:30一到就下楼骑着电驴跑去西溪银泰了。这地方不常来吃东西，还因为他家分A、B馆所以找 KFC 还费了一番功夫。

吃饱喝足后就去B馆的博纳看电影拉~

这次的异性新片只能说非常对我胃口，一本满足。

看完后就又骑着电驴跑回家了继续上班了 😆

\#2

因为之前先和两个发小约的周六上午的西投银泰的电影院看异形，所以周六一早就出发了。

但是我没想到这次周六到紫之隧道前面的整个紫金港路全都堵上了，给我蛋疼的...提前给兄弟们预警之后一个发小说他那边也堵了 🤨

最终开到西投银泰停好车之后已经开场快十分钟了，不过还好进影院坐下之后发现前言部分已经过了，刚好到片头，毕竟我周五已经一刷了嘛

二刷的体验还是非常不错，有些桥段还是会有那种被吓倒的感觉。

看完电影后吃了一顿凑凑火锅，我感觉一般吧就味道，但是服务确实是不错

吃完火锅去了一个发小家里坐了一下，然后点了一个 007 skyfall 看了起来，中间我总感觉好像哪里看过但是完全没印象了

看到块16:40就开车回家了，不然太晚了又有蹭别人晚饭的嫌疑了

\#3

- **僧侣和枪 The Monk and the Gun** 4/5 很有趣的电影，隐喻很多，中间分断最后又交叉的叙事这里用得恰到好处。民主化代表的现代事物对传统思想习俗的冲击，官员们对于民主流程的执着远大于民主制度本身，美国来的枪支收藏家最后看着自己苦苦寻找的枪如同世俗利益一般被埋在佛塔底下
- **异形：夺命舰 Alien: Romulus** 5/5 当初那种感觉又都回来了，探讨宗教哲学还是留给扑街的前传吧
- **偷天换日 The Italian Job** 3.5/5 3.5/5 ocean-eleven 式的群像hustle片，剧本节奏没问题很流畅，塞隆是真漂亮，但是感觉角色特点都拍得一般，除了Charlie之外其他人都感觉像打酱油.…
- 007 Skyfall 我发现是2014年看的...这次算是二刷吧... Orz

\#4

周五上午的时候我们业务的 head 老登失联了，然后我们发现 CDO 下属的 head 全都是手机在线

于是我们大胆猜测他们又被叫去开北戴河会议了

结合下周马上要发的财报，估计是财报会很难看，然后可能要有裁员啥的 😅

## Work

\#1

**CppCon 2022 | Modern C++: C++ Patterns to Make Embedded Programming More Productive - Steve Bush** https://www.youtube.com/watch?v=6pXhQ28FVlU&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=15

- 一些 embeded 环境的 tips，主要以广度而不是深度为核心

**CppCon 2022 | Breaking Dependencies - The Visitor Design Pattern in Cpp - Klaus Iglberger** https://www.youtube.com/watch?v=PEcy1vYHb8A&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=14

- 传统的 visitor pattern 和使用 std::variant 实现 visitor pattern 的优缺点
- 非常不错的一个教学 talk

**CppCon 2022 | From C++ Templates to C++ Concepts - Metaprogramming: an Amazing Journey** https://www.youtube.com/watch?v=_doRiQS4GS8&list=PLHTh1InhhwT6c2JNtUiJkaH8YRqzhU7Ag&index=16

- Introduction to concepts

**Creating a co_await awaitable signal that can be awaited multiple times, part 5** https://devblogs.microsoft.com/oldnewthing/20210305-00/?p=104934

- 系列第19篇
- 这篇用了两个 trick，一个是 last node ptr 可以替代之前的 signaled indicator 这样体积大小不变还可以在最后正向遍历链表
- 另外一个trick是 last ptr 是二级指针，这个应该算是经常写链表的一个常用技法？我自己是想了一下才发现这个 trick 可以让 append 操作直接统一化

**Creating a co_await awaitable signal that can be awaited multiple times, part 6** https://devblogs.microsoft.com/oldnewthing/20210308-00/?p=104938

- 系列第20篇
- 这篇探讨了两个点，第一个是是否要考虑 event signaled 之后临时插队的 coroutine，第二个是 coroutine destroy 的时候是否要 unlink from waiting queue
- Raymond 提到 cppcoro 的代码里也并不处理 suspended  waiting coroutine

**Meeting C++ online | Victor Ciura - Symbolism: Rainbows and Crashes** https://www.youtube.com/watch?v=4qvB3HQHgMM

- focus on Windows
- Prefere /EHa (SEH) over standard c++ exception in underlying and wraps SEH in C++ exception
- Some SEH use case tips
- However, we can generate minidump instead…which contains more detailed context information
- quick introduction to C++ 23 stacktrace

**Optimizing the Clang compiler’s line-to-offset mapping** https://developers.redhat.com/blog/2021/05/04/optimizing-the-clang-compilers-line-to-offset-mapping#

- 一些 micro-optimization 的实践
- 根据业务场景来 instruct __builtin_expect/likely/unlikely 生成更好的代码；对于高概率的情况基于更多的 fast path 优化；bithack 和 sse 版本的 memchr

**Mostly harmless: An account of pseudo-normal floating point numbers** https://developers.redhat.com/blog/2021/05/12/mostly-harmless-an-account-of-pseudo-normal-floating-point-numbers#

- 介绍了 intel 引入的 80-bit floating point 类型在某些情况下“可能”会是 *pseudo-normal floating point numbers* 并且导致浮点数处理变得棘手，以及这种 ub 的 case 会导致一些安全隐患

\#2

这周解决了我们项目里子目录的源码文件会导致 ci clang-tidy 异常的问题。

原因是：

- 我们用了 makefile，没有标准的 compile_commands.json 的生成，所以 clang-tidy 的编译参数是直接通过 makefile 里定义提供的
- 这就导致我们跑 clang-tidy 必须要 cd 到某个模块所在的目录才能 make clang-tidy
- 而之前的 ci hook 都没有考虑子目录的情况，因为子目录不存在 makefile 文件，所以会失败
- 给子目录添加一个 makefile 确实可以解决问题，但是这个 workaround 让我觉得很不干净，因为我们的意图就是一个模块一个 makefile，子目录不是一个独立模块，但是如果放了 makefile 就不能阻止别人往里面直接写构建逻辑

所以最后的方案是我花了点时间，边问 GPT 边写 bash 做了从某个源文件位置开始递归向上找到第一个包含 makefile 目录的实现，改了一下其他部分就解决了这块问题

核心代码就

```bash
#!/usr/bin/env bash

function find_makefile_dir() {
  local fullpath="$1"
  local file=$(basename "${fullpath}")
  local dir=$(dirname "${fullpath}")

  while [[ "${dir}" != "." && "${dir}" != "/" ]]; do
      if [[ -f "${dir}/Makefile" ]]; then
          local remain_path="${fullpath#${dir}/}"
          echo "${dir}:${remain_path}"
          return 0
      fi
      dir=$(dirname "${dir}")
  done
}

changed_files=($(git diff --diff-filter=d --name-only HEAD~1 HEAD | grep "\.cc$"))

for file in "${changed_files[@]}"; do
  parts=$(find_makefile_dir "${file}")
  if [[ -n ${result} ]]; then
    result="${result}\n${parts}"
  else
    result=${parts}
  fi
done

dir_array=($(echo -e "${result}" | cut -d':' -f1))
file_array=($(echo -e "${result}" | cut -d':' -f2 | tr -d '.cc'))

echo "${dir_array[@]}"
echo "${file_array[@]}"
```

\#3

这周琐碎的事情还是比较多，所以还是比较温吞摸鱼的状态 🤣

---

好了这周就这样，下周见
