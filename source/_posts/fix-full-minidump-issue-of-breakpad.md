---
title: 修复 Breakpad 不能启用 Full Minidump
categories: PROGRAMMING
date: 2017-05-08 23:58:18
tags: [minidump, breakpad]
---
发布分支为 chrome-58 的 google-breakpad 存在无法启用 full minidump 的问题，表现症状是，一旦启用 `MiniDumpWithFullMemory` 标志，则输出的 dump 文件为 0 字节，但是整个 dump 生成流程没有任何其他异常，相关返回值甚至是 `true`。

经过一个上午的跟踪调试，发现问题出现在，breakpad 针对 `MiniDumpWithFullMemory` 做了特殊处理（会生成一个普通的 minidump 和一个 full dump，后者的文件名多了一个full 后缀），但是这个版本（chrome-58）里却忘了调用生成 full dump 的生成函数.....

解决方法：对 [master]((https://github.com/google/breakpad/blob/67649c61853108eb0c29703f6ff0db42e9d69f10/src/client/windows/crash_generation/crash_generation_server.cc) 和 ) 分支的 `CrashGenerationServer::GenerateDump` 和 [chrome-58](https://github.com/google/breakpad/blob/chrome_58/src/client/windows/crash_generation/crash_generation_server.cc) 分支做一个 diff，将缺的代码加回来...

缺失的代码就是下面这段...

```c++
// If the client requests a full memory dump, we will write a normal mini
// dump and a full memory dump. Both dump files use the same uuid as file
// name prefix.
if (client.dump_type() & MiniDumpWithFullMemory) {
  std::wstring full_dump_path;
  if (!dump_generator.GenerateFullDumpFile(&full_dump_path)) {
    return false;
  }
}
```

另外，开启了 full minidump 模式之后，完整的 dump 体积会暴增到 200MB 上下，要做好心理准备。