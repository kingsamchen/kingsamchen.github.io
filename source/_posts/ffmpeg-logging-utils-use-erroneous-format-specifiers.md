---
title: 被 FFmpeg 的日志模块撞了一下腰
categories: PROGRAMMING
date: 2018-03-07 00:00:55
tags: [ffmpeg, windows, crash]
---
周末的时候客服同学反馈有个用户出现了崩溃，并且要来了崩溃 dmp 文件。

挂上 windbg 后发现崩溃原因是 CRT 的 `invalid-parameter` 异常，第一现场是输出 ffmpeg 的 avcodec 模块的日志。

省略中间若干分析过程，直击结论：FFmpeg 的 Windows 构建并不是用 MSVC 编译的，因此在类似 `printf()` 函数的 format specifier 使用上出现了偏差。

代码使用 `%td` 输出 `ptrdiff_t` 类型，而 VS2017 之前的版本并不支持这个 format specifier，因此函数调用 `_vsnprintf_p()` 出现了参数异常，导致程序挂掉。

虽然 VS 2017 很厚道的支持了这个格式，但是某直播姬的工具链是 VS 2013，且短时间不可能调整，因此最后的解决方案是，上层安装的 logging hook 在输出日志时，首先修正一遍 format 里的 specifier，替换为 VS 2013 支持的 `%Id`。

注意，类似的还有 `size_t` 的 format specifier。
