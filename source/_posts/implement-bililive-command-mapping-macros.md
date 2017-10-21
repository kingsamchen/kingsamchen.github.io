---
title: 一个轻量型的 Command-Mapping Macros
categories: PROGRAMMING
date: 2017-10-21 10:31:55
tags: [c++, bililive]
---
某 Bililive 沿用了 Chromium 负责在 UI 层不同模块通讯的 command/handler 的机制，但是因为 Chromium 对 command 机制使用得很节制，因此相关的代码量不多，其处理函数一直是一个大大的 `switch...case`

```c++
void ExecuteCommandWithParams(Bililive* receiver, int command, const CommandParamsDetails& params)
{
    switch (command) {
        case IDC_ALPHA:
            // handler code
            break;

        case IDC_BRAVO:
            // handler code
            break;

        case IDC_CHARLIE:
            // handler code
            break;
        ...
    }
}
```

然而到了 Bililive 这里，因为开发人员水平限制，没有最好模块内外的通讯规划，导致存在大量的 command 通信，而 handler 代码依然是直接原封不动写在 `switch` 里。于是 `ExecuteCommandWithParams()` 就包含了上千行的实现，而且由于都在一个函数内，没有语法上的 scope 和清晰的语义划分，维护起来相当痛苦。

一个有效的解决方案是采用类似 MFC Message-Mapping 的方式，将不同的 message handler 分离到单独的函数里。至于参数，因为所有 command 的参数都是一致的，直接简化了处理。

```c++
#include "base/logging.h"

#define BEGIN_BILILIVE_COMMAND_MAP(cmd_receiver, cmd_id, cmd_params)        \
    {                                                                       \
        auto __receiver = cmd_receiver;                                     \
        auto __id = cmd_id;                                                 \
        const auto& __params = cmd_params;                                  \
        switch (cmd_id) {                                                   \

#define ON_BILILIVE_COMMAND(cmd_id, fn)                                     \
    case cmd_id: {                                                          \
        fn(__receiver, __params);                                           \
    }                                                                       \
    break;                                                                  \

#define ON_BILILIVE_COMMAND_UNHANDLED_ERROR()                               \
    default: {                                                              \
        NOTREACHED() << "Unhandled command " << __id;                       \
    }                                                                       \
    break;                                                                  \

#define END_BILILIVE_COMMAND_MAP()                                          \
        }                                                                   \
    }
```

于是处理相关的代码就变成

```c++

void OnAlpha(Bililive* receiver, const CommandParamsDetails& params)
{}

void OnBravo(Bililive* receiver, const CommandParamsDetails& params)
{}

void OnCharlie(Bililive* receiver, const CommandParamsDetails& params)
{}

void ExecuteCommandWithParams(Bililive* receiver, int command, const CommandParamsDetails& params)
{
    BEGIN_BILILIVE_COMMAND_MAP(receiver, command, params)
        ON_BILILIVE_COMMAND(IDC_ALPHA, OnAlpha)
        ON_BILILIVE_COMMAND(IDC_BRAVO, OnBravo)
        ON_BILILIVE_COMMAND(IDC_CHARLIE, OnCharlie)
        ON_BILILIVE_COMMAND_UNHANDLED_ERROR()
    END_BILILIVE_COMMAND_MAP()
}
```
