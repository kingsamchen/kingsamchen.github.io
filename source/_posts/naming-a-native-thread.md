---
title: Naming a Native Thread
categories: PROGRAMMING
date: 2018-10-13 21:58:06
tags: [Windows, Linux, thread]
---
这里所说的 naming 主要是为了能够被 debugger 识别，所以单纯的通过 TLS 存储一个额外的字符串是不够的。

## Windows

Windows 上的做法有两种。

第一种是利用现成的 API [SetThreadDescription()](https://docs.microsoft.com/en-us/windows/desktop/api/processthreadsapi/nf-processthreadsapi-setthreaddescription)

通过这个 API 设置的名字据说新版的 minidump 和 WinDBG 都能认了。

不过缺点是这个 API 很新，从 Windows 10 1607 (build 14393) 开始才有，所以稳妥的使用方式还是从 kernel32.dll 里动态获取。

第二种做法比较传统，而且很不直观，来源是 MSDN 的一篇 [doc](https://docs.microsoft.com/en-us/visualstudio/debugger/how-to-set-a-thread-name-in-native-code?view=vs-2017)

```cpp
const DWORD kVCThreadNameException = 0x406D1388;

typedef struct tagTHREADNAME_INFO {
    DWORD dwType;  // Must be 0x1000.
    LPCSTR szName;  // Pointer to name (in user addr space).
    DWORD dwThreadID;  // Thread ID (-1=caller thread).
    DWORD dwFlags;  // Reserved for future use, must be zero.
} THREADNAME_INFO;

void SetThreadName()
{
    THREADNAME_INFO info;
    info.dwType = 0x1000;
    info.szName = "Worker-Traditional";
    info.dwThreadID = -1;
    info.dwFlags = 0;

    __try {
        RaiseException(kVCThreadNameException, 0, sizeof(info) / sizeof(DWORD),
                       reinterpret_cast<ULONG_PTR*>(&info));
    } __except(EXCEPTION_EXECUTE_HANDLER) {
    }
}
```

基本上照搬 doc 上的代码就好了。
<!-- more -->
另外，这个方式设置的名字 minidump 里是没有的，而且必须要在调试器已经存在的情况下使用；哪怕先进行设置，然后附加调试器，调试器里也是看不到设置的线程名的。

因此第二个方法使用前一般会判断是否存在调试器，避免做无用功。

## Linux

如果能保证完整 pthread 的 POSIX 环境，则可以使用 `pthread_setname_np()`。

不过据说很多 glibc 的 pthread 都不提供这个函数。

仅限 Linux 平台的话，更好用的一个 syscall 是 [prctl()](https://linux.die.net/man/2/prctl)

```cpp
pid_t GetCurrentThreadID()
{
    return syscall(__NR_gettid);
}

void SetThreadName(const char* name)
{
    if (GetCurrentThreadID() == getpid()) {
        return;
    }

    int err = prctl(PR_SET_NAME, name);
    if (err < 0) {
        fprintf(stderr, "error: %d", errno);
    }
}
```

使用上只需要注意不要修改 main thread 的 name，避免导致一些命令行工具失败。
