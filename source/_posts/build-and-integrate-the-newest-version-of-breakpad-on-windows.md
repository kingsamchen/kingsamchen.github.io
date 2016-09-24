---
title: 在 Windows 上构建并接入最新发布分支的 breakpad
categories: PROGRAMMING
date: 2016-09-24 11:12:02
tags: [cant, breakpad]
---
新直播姬项目重构的差不多了，于是前几天 leader 对我说：组织已经决定了，你来接入 crash dump 的处理收集！

于是我就念了句口号：*Hail Hydra*，然后开始研究怎么接入 [google-breakpad](https://chromium.googlesource.com/breakpad/breakpad/)。

breakpad 一直是 chromium 收集 crash dump 的御用雏形（在其之上加了一些私货），不过之前在做大屎豹的时候一直都是别人在负责这块功能，所以并不熟悉；不过没吃过猪肉还是见过猪跑的。

项目最新的发布分支是 **chrome_53**， 那就选择从这个 remote branch 切出一个 local branch。但是切完之后我就发现不对劲了：特么以前一直随包附带的 `gyp` 目录呢？？？难道最新的发布版本修改了 Windows 上的构建流程？

按照 Google 的一贯尿性，这是极有可能的，看看隔壁 chromium 的 tool-chain 改了多少次就知道了；于是打开官方项目的 README 看看 build instructions，嗯，果然改了，踏马直接把 build on Windows 的相关内容给删了！ WTF？？！

Google 一下发现虽然 gyp 和 doc 被移除了，但是之前的 gyp 构建方案还是可以用的。（嗯，我日你个仙人板）

研究了一下，可用的构建流程如下：

1. 在本地 clone 一个 [gyp](https://chromium.googlesource.com/external/gyp)，直接用 `master` 分支就好，并且保证 `gyp.bat` 在环境变量 `%PATH%` 中（无所谓是你手动添加还是在命令行中用 `set path` 添加）
2. 打开文件 `breakpad\src\client\windows\breakpad_client.gyp`，注释掉 `targets : dependencies` 列表中的最后三个和 test 有关的依赖引用；因为某些测试工程的编译依赖 `gtest`，但是当前项目并不附带，所以你直接编译肯定会一坨错误，所以这里直接不构建测试工程。当然如果你需要的化，自己根据 gyp 设置一下 gtest 目录。
3. 在 breakpad 的项目目录打开 cmd，用 `SET GYP_MSVS_VERSION=2013` 设置目标 Visual Studio 工程的版本；如果要生成 VS 2015 的文件，版本改成 2015 就好了
4. 运行 `gyp.bat --no-circular-check src\client\windows\breakpad_client.gyp -Dwin_release_RuntimeLibrary=0 -Dwin_debug_RuntimeLibrary=1`；最后两个参数设置的是 `/MT(d)` 还是 `/MD(d)`
  - 0：`/MT`
  - 1：`/MTd`
  - 2：`/MD`
  - 3：`/MDd`
5. 生成的项目文件在目录 `src\client\windows\` 中，直接打开编译即可；（Visual Studio 2015 编译可能会出现 warning C4091，导致编译失败，直接屏蔽掉这个错误）

### 使用上要注意的几个坑

Disclaimers：因为服务端暂时不支持，考虑到简易性，我在直播姬里用的是进程内抓 dump。

一个简单靠谱的 demo

```c++
namespace {

using google_breakpad::ExceptionHandler;

const wchar_t kCrashDumpDirName[] = L"crash_dumps";
const wchar_t kHintMessage[] = L"我...挂了...囧rz";

// We leak the object on purpose.
ExceptionHandler* exception_handler = nullptr;

bool OnMinidumpGenerated(const wchar_t* dump_path, const wchar_t* minidump_id, void* context,
                         EXCEPTION_POINTERS* exinfo, MDRawAssertionInfo* assertion, bool succeeded)
{
    if (succeeded)
    {
        MessageBoxW(nullptr, kHintMessage, L"Error", MB_OK);
    }

    return succeeded;
}

void InstallCrashHandler()
{
    // This is needed for CRT to not show dialog for invalid param
    // failures and instead let the code handle it.
    _CrtSetReportMode(_CRT_ASSERT, 0);

    std::wstring dump_dir_path = SetupCrashDumpDirectory(kCrashDumpDirName);
    exception_handler = new ExceptionHandler(dump_dir_path,
                                             nullptr,
                                             OnMinidumpGenerated,
                                             nullptr,
                                             ExceptionHandler::HANDLER_ALL,
                                             MiniDumpNormal,
                                             L"",
                                             nullptr);
}

}   // namespace

int APIENTRY wWinMain(HINSTANCE instance, HINSTANCE prev, wchar_t*, int)
{
    InstallCrashHandler();
    // ...
    return 0;
}
```

首先要保证项目的 include directory 中一定要包含 `breakpad\src\` 这个路径，因为 breakpad 内部代码的 include 是基于这个假设

只需要在 exe 文件的入口函数（`main` 或者 `WinMain`）中设置 breakpad 的 `exception handler` 即可，哪怕你的项目有很多额外的 DLLs；Handler 是进程全局的，这点由操作系统保证（本来 breakpad 就是基于操作系统提供的 API 做的一个 wrapper）。

不过要注意在自己的代码中，尤其是用的第三方的 DLL，不要出现用了 `SetUnexpectedHandler()` 导致 override 了 breakpad 的 handler 的情况。因为这个我被坑过一下午 -''-

保存 crash dump 的文件夹一定要**事先存在**，breakpad 不会帮你创建，事实上，如果文件夹不存在，breakpad 的 handler 甚至工作不正常，还是走回 Windows 的崩溃反馈流程去了。

第二个参数是一个 exception filter callback，用来过滤一些异常，这里不需要直接填空。

第三个参数是 mini dump 写完之后的回调，用来做一些提示用户的善后操作，参数 `succeeded` 提示 dump 是否成功写入；通常情况下，这个 routine 返回后 breakpad 会**自动结束程序**，避免异常造成更进一步的破坏，这是非常合理的做法，所以不要在这个 callback 里做一些 non-trivial 的事情。

### 王之鄙视

最后鄙视一下 google breakpad team 这种不想做兼容就索性删掉相关内容的做法。