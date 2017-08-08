---
title: GetEnvironmentVariable, API 设计的反面教材
categories: PROGRAMMING
date: 2017-08-08 23:52:40
tags: [windows, design, environment]
---
本次 post 的主角 `GetEnvironmentVariableW()` 用于获取某个环境变量的值，它的文档可以参见[这里](https://msdn.microsoft.com/en-us/library/windows/desktop/ms683188.aspx)，看的时候记得仔细研究一下返回值细节。

这个 API 的设计可以说是一个生动的反面教材。

根据 MSDN，它的返回值如下：
- 如果成功，则返回保存到缓存中的值的字符数，**不包括 terminating null character**
- 如果缓冲区不够，那么就返回需要的缓冲区大小，以字符数计数，且**包含 terminating null character**
- 如果函数失败，则**返回 0** （具体细节由 `GetLastError()` 给出）

这里不吐槽在返回值非 0 的前提下，存在两种计数的返回值，我们考虑一种很 rare 的情况：环境变量的值是一个 empty string 时，这个 API 应该返回什么

Windows 上是可以通过 `SetEnvironmentVariableW()` 将某个环境变量的值设置为空字符串的

```cpp
const wchar_t* env_name = L"KCPath";
const wchar_t* str = L"";
BOOL rv = SetEnvironmentVariableW(env_name, str);
assert(rv);
```

那么此时用 `GetEnvironmentVariableW()` 获取值时，会返回 0 （因为不包含 terminating null，字符数为 0）。那么问题来了，如果我事先不知道这是一个空字符串，那么我怎么知道这个返回 0 意味着失败还是空字符串？

答案是没法区分，即使你再进一步调用 `GetLastError()`。

为什么？

因为 `GetLastError()` 的文档说的很清楚：在大部分 API **失败**时， calling thread 的 last error code 会被设置；而有部分（少量）API在成功时，也会设置 last error code。

但是很遗憾，通过实验验证，环境变量相关的 API 不属于后者。

这意味着存在一种可能：首先调用了某个环境变量的API，例如 `GetEnvironmentVariableW()`，但是他失败了，于是 last error code 被设置了；然后紧接着的代码片段利用 `GetEnvironmentVariableW()` 获取了某个环境变量的值，而这个值是 empty string，于是 API 返回 0；此时你调用了一下 `GetLastError()`，他返回了一开始调用 API 错误设置的那个错误值，于是这下你就以为后面的调用失败了....

来上一个 PoC 吧

```cpp
int main()
{
    const wchar_t* env_name = L"KCPath";
    const wchar_t* str = L"";

    // Failed, and last error code will be set.
    DWORD size_needed = GetEnvironmentVariableW(env_name, nullptr, 0);
    assert(size_needed == 0);
    assert(GetLastError() == ERROR_ENVVAR_NOT_FOUND);

    // set an empty string.
    BOOL rv = SetEnvironmentVariableW(env_name, str);
    assert(rv);

    // Request buffer size from the API.
    // Returned size includes null-terminate character.
    size_needed = GetEnvironmentVariableW(env_name, nullptr, 0);
    assert(size_needed == 1);

    wchar_t buf[MAX_PATH] {0};
    DWORD size_read = GetEnvironmentVariableW(env_name, buf, MAX_PATH);

    // WAT? is last call failed?
    assert(size_read == 0);

    auto err = GetLastError();
    // Boom!
    assert(err == ERROR_ENVVAR_NOT_FOUND);

    return 0;
}
```

这个故事告诉我们，一个接口的返回值语义的清晰和不重叠是多么的重要。

最后：这个故事来自于我一个很意外的单元测试用例；我一开始一度以为问题出在 `GetEnvironmentVariableW()` 不能正确处理空字符串值，以至于这篇 post 最开始的标题是 *the werid behavior of GetEnvironmentVariable*；但是在我编写 PoC 的阶段我却发现问题怎么也复现不出来...经过快有半小时的跟踪分析后，我才最终确定真正的 root cause；这也告诉我们一个道理，verification 是多么的重要...