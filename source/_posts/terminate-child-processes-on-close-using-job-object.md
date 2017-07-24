---
title: 利用 Job 内核对象实现父进程关闭时自动结束所有子进程
categories: PROGRAMMING
date: 2017-07-24 23:41:00
tags: [job, windows]
---
投稿工具的压制功能一直来都有一个问题：如果主进程被强行结束了（例如利用任务管理器），那么创建的 ffmpeg 压制进程仍然会继续运行。

因为 ffmpeg 是以二进制的方式部署的，因此不存在修改它的代码，自己和主进程建立 IPC 监控的方式。

至于采用远线程注入的方式来强行 HACK，我一直对这种无视客观规律的霸道方式都不太感冒，毕竟我们又不是做安全软件。

所以我想到了曾经在 Windows 核心编程中看到的一个方法：使用 Job 内核对象。

核心方法总结起来就是一句话：将 ffmpeg 压制进程加入到一个 Job 对象中，利用 Job 对象的 *Kill-On-Close* 特性，在 Job 内核对象被释放时，Job 内的所有进程都会被系统结束。

于是我写了一个 demo snippet，解决了几个坑之后验证了我的想法。

```cpp
#include <conio.h>

#include <cassert>
#include <iostream>
#include <string>

#include <Windows.h>

bool LaunchAndWait(const std::wstring& cmdline, HANDLE job)
{
    wchar_t buf[255] {0};
    wcscpy_s(buf, cmdline.c_str());

    STARTUPINFO startup {0};
    startup.cb = sizeof(startup);
    PROCESS_INFORMATION process_information {nullptr};

    if (!CreateProcessW(nullptr,
                        buf,
                        nullptr,
                        nullptr,
                        FALSE,
                        CREATE_SUSPENDED | CREATE_BREAKAWAY_FROM_JOB,   // important
                        nullptr,
                        nullptr,
                        &startup,
                        &process_information)) {
        auto err = GetLastError();
        std::cerr << "Failed to create process: " << err;
        return false;
    }

    if (!AssignProcessToJobObject(job, process_information.hProcess)) {
        std::cerr << "Failed to assign process to job: " << GetLastError();
        return false;
    }

    ResumeThread(process_information.hThread);

    CloseHandle(process_information.hProcess);
    CloseHandle(process_information.hThread);

    return true;
}

int main()
{
    BOOL is_in_job;
    IsProcessInJob(GetCurrentProcess(), nullptr, &is_in_job);
    std::cout << "Is the main process in job: " << is_in_job << std::endl;

    std::cout << "Wait for constructing job object!\n";

    HANDLE job = CreateJobObjectW(nullptr, nullptr);
    if (!job) {
        auto err = GetLastError();
        std::cerr << "Failed to create job: " << err << std::endl;
        return 0;
    }

    // Enforce limits.
    JOBOBJECT_EXTENDED_LIMIT_INFORMATION job_limits;
    memset(&job_limits, 0, sizeof(job_limits));
    job_limits.BasicLimitInformation.LimitFlags = JOB_OBJECT_LIMIT_KILL_ON_JOB_CLOSE;

    if (!SetInformationJobObject(job, JobObjectExtendedLimitInformation, &job_limits, sizeof(job_limits))) {
        auto err = GetLastError();
        std::cerr << "Failed to set job information: " << err << std::endl;
    }

    std::cout << "Job object created! Launch test subjects...\n";

    bool r = LaunchAndWait(LR"(C:\Windows\notepad.exe)", job);
    assert(r);

    _getch();

    return 0;
}
```

最终的产品代码思路是完全一样的，当然代码不可能像上面这样随意。

这里有两个比较隐蔽的坑（MSDN 上不一定能看到）需要注意一下：

第一个是创建进程时最好带上 `CREATE_BREAKAWAY_FROM_JOB`，因为 Job 属性存在自动继承性：如果一个进程属于一个 Job，那么他创建的子进程会自动关联到这个 Job。

`explorer` 和 VS 都是存在于 Job 中的，因此如果在这两个环境下运行程序， `IsProcessInJob()` 会返回 `true`

第二个坑特别隐蔽：我一开始的 test subject 是 calc.exe，在 Windows 10 上这是一个 UWP 程序，然而无论我怎么做，calc 进程都活得好好的，一点不像是被约束的样子。。。甚至所有 API 的调用都并没有返回错误。

而当我将 test subject 换成 Win32 程序 notepad.exe 之后，功能就可以正常跑了....

我猜想这大概和 UWP 程序的一些安全特性有关，不过因为本身对安全并没有偏执的热爱，所以在这个点上就没有继续深入了。
