---
title: 在 Windows 10 上获取正确的系统版本
categories: PROGRAMMING
date: 2016-07-30 23:45:38
tags: [windows, system version]
---
大约从 Windows 8 开始，微软开始觉得大家之前那种不同系统版本给不同的代码的方式太不和谐了，怎么可以搞 discrimination 呢？要和谐，要有爱！于是把常用的 `GetVersionEx()` 给标记成了 deprecated，并且建议大家使用另外一套更加政治正确的 version verification API。

然后就到了 Windows 10，微软发现大家这个版本歧视还是搞得很严重啊，太不和谐了，于是不光把原来的 version verification 给 deprecate 了，甚至 `GetVersionEx()` 返回的结果都给强行改成和 windows 8.1 一样。

这下没法简单区分 Windows 8.1 和 Windows 10 了，耶，one Windows, a universal windows platform! Hail Microsoft!

当然微软还是留了一条后路，如果真的需要获得正确的 Windows 版本，那么就需要给 EXE 挂一个 manifest，往里面加兼容版本的 UUID item。

但是这样做就非常麻烦，而且似乎就算加了 manifest，程序也得使用正确的 SDK 版本。

于是就有其他非常规的方式，比如 `RtlGetVersion()`。

`RtlGetVersion()` 非常神奇，`GetVersionEx()` 实际上内部调用的就是这个函数，但是 `RtlGetVersion()` 不会做额外的模糊版本号的事情。

这个 API 位于 *ntdll.dll* 中，所以直接动态调用就好（或者你有 DDK，直接用 DDK 提供的那份声明也可以）。

于是只需要

```c++
#define DECLARE_DLL_FUNCTION(fn, type, dll) \
    auto fn = reinterpret_cast<type>(GetProcAddress(GetModuleHandleW(L##dll), #fn))

struct VersionNumber {
    unsigned long major_version;
    unsigned long minor_version;
};

void GetSystemVersion(VersionNumber& version_number) noexcept
{
    constexpr NTSTATUS kStatusSuccess = 0L;
    DECLARE_DLL_FUNCTION(RtlGetVersion, NTSTATUS(WINAPI*)(PRTL_OSVERSIONINFOW), "ntdll.dll");
    if (!RtlGetVersion) {
        return;
    }

    RTL_OSVERSIONINFOW ovi { sizeof(ovi) };
    if (RtlGetVersion(&ovi) != kStatusSuccess) {
        return;
    }

    version_number.major_version = ovi.dwMajorVersion;
    version_number.minor_version = ovi.dwMinorVersion;
}
```

就可以达到生命的伟大和谐。

RANT #1: 有另外一种方式的思路是通过获取 *kernel32.dll* 或其他系统 DLL 的文件版本，比如 chromium 就是这么做的。但是总觉得这种方式太容易玩脱。

RANT #2: 宏 `DECLARE_DLL_FUNCTION` 是从某花之前的一篇 [post](http://flowercode.org/archives/51) 改的。我觉得他给的宏问题太大，实用性比较差。

API 原型没有必要分离出来，太累赘；如果硬要说分离有什么好处的话，大概就是这些 API 都保存在一个单独的 *.h* 里，不用自己手写。但是这样又会引入另外一个问题，很容易玩脱违反 ODR，然后打出 GG，`extern` 都拯救不了。

其实 `DECLARE_DLL_FUNCTION` 长现在这个样子有个原因是没发现什么简单有效的方法从函数指针的原型声明中分理处变量名...

RANT #3: 微软刻意模糊系统版本估计是希望大家尽量走 UWP 的方式，毕竟可以通过链接到不同版本的 SDK 来区分支持的系统，而分发又是直接通过 microsoft app store 完成。但是对于 native code app 来说，这样做简直就是自己挖坑别人跳，快赶上 Apple 一样无赖了。

Bonus: 欢迎使用 `kbase::OSInfo::IsVersionOrGreater()` 来解决版本检查，Sample 可以看[这里](https://github.com/kingsamchen/KBase/blob/master/Docs/os_info.md)