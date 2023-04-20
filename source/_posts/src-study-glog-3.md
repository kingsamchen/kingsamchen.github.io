---
title: glog 源码分析 Part 3
categories: PROGRAMMING
date: 2023-04-20 21:18:59
tags: [glog, cpp]
toc: true
---
## LOG_EVERY_N 如何实现

每个 LOG_EVERY_N() 都会生成一个 std::atomic<int> 全局内部对象 counter，用 counter 来计数。

实际上为了避免直接取模，glog 实现用了另外一个变量利用减法来做 wrap

```cpp
#define LOG_EVERY_N(severity, n)                                        \
  SOME_KIND_OF_LOG_EVERY_N(severity, (n), google::LogMessage::SendToLog)

#define SOME_KIND_OF_LOG_EVERY_N(severity, n, what_to_do) \
  static std::atomic<int> LOG_OCCURRENCES(0), LOG_OCCURRENCES_MOD_N(0); \
  GLOG_IFDEF_THREAD_SANITIZER(AnnotateBenignRaceSized(__FILE__, __LINE__, &LOG_OCCURRENCES, sizeof(int), "")); \
  GLOG_IFDEF_THREAD_SANITIZER(AnnotateBenignRaceSized(__FILE__, __LINE__, &LOG_OCCURRENCES_MOD_N, sizeof(int), "")); \
  ++LOG_OCCURRENCES; \
  if (++LOG_OCCURRENCES_MOD_N > n) LOG_OCCURRENCES_MOD_N -= n; \
  if (LOG_OCCURRENCES_MOD_N == 1) \
    google::LogMessage( \
        __FILE__, __LINE__, google::GLOG_ ## severity, LOG_OCCURRENCES, \
        &what_to_do).stream()

// Use macro expansion to create, for each use of LOG_EVERY_N(), static
// variables with the __LINE__ expansion as part of the variable name.
#define LOG_EVERY_N_VARNAME(base, line) LOG_EVERY_N_VARNAME_CONCAT(base, line)
#define LOG_EVERY_N_VARNAME_CONCAT(base, line) base ## line

#define LOG_OCCURRENCES LOG_EVERY_N_VARNAME(occurrences_, __LINE__)
#define LOG_OCCURRENCES_MOD_N LOG_EVERY_N_VARNAME(occurrences_mod_n_, __LINE__)
```

`LOG_OCCURRENCES(0)` 宏展开之后就是

```cpp
static std::atomic<int> occurrences_$LINE(0)
```

所以那个 0 其实是 counter 初始值。

---

再来看为啥要搞两个 counter 变量。

首先这种情况下会调用的 `LogMessage` 构造函数是

```cpp
LogMessage::LogMessage(const char* file, int line, LogSeverity severity,
                       int64 ctr, void (LogMessage::*send_method)())
    : allocated_(NULL) {
  Init(file, line, severity, send_method);
  data_->stream_.set_ctr(ctr);
}
```

额外的操作是为 LogStream 保存了当前的 counter。

而这个 counter 的作用就是当用户向知道这条日志消息目前的总 counter 数，即 `google::COUNTER` 时通过 steam 输出的

```cpp
// LOG_EVERY_N(INFO, 10) << "Got the " << google::COUNTER << "th cookie";

// We want the special COUNTER value available for LOG_EVERY_X()'ed messages
enum PRIVATE_Counter {COUNTER};

// Output the COUNTER value. This is only valid if ostream is a
// LogStream.
ostream& operator<<(ostream &os, const PRIVATE_Counter&) {
#ifdef DISABLE_RTTI
  LogMessage::LogStream *log = static_cast<LogMessage::LogStream*>(&os);
#else
  LogMessage::LogStream *log = dynamic_cast<LogMessage::LogStream*>(&os);
#endif
  CHECK(log && log == log->self())
      << "You must not use COUNTER with non-glog ostream";
  os << log->ctr();
  return os;
}
```

## Raw Logging

针对那些底层模块无法使用普通 LOG 宏的场景，代码在 `glog/raw_logging.h` 中；区别在于

- 不会在堆上分配内存
- 只会往 stderr 写日志并且不带 buffering
- 不用锁
- 自动截断过长的消息

```cpp
#define RAW_LOG(severity, ...) \
  do { \
    switch (google::GLOG_ ## severity) {  \
      case 0: \
        RAW_LOG_INFO(__VA_ARGS__); \
        break; \
      case 1: \
        RAW_LOG_WARNING(__VA_ARGS__); \
        break; \
      case 2: \
        RAW_LOG_ERROR(__VA_ARGS__); \
        break; \
      case 3: \
        RAW_LOG_FATAL(__VA_ARGS__); \
        break; \
      default: \
        break; \
    } \
  } while (0)
  
#define RAW_LOG_INFO(...) google::RawLog__(google::GLOG_INFO, \
                                   __FILE__, __LINE__, __VA_ARGS__)
```

类似的手法，不过这回是直接调用 `RawLog__()` 这个函数。

`DoRawLog()` 封装了 `vsnprintf()`，可以往一个给定的 buffer 写入格式化字符串；buffer 就是一个局部数组，大小是 3000。

写操作用系统调用直接往 stderr 的 fd 写，目的是避开 crt 这一层内部的 buffering。

## Syslog

和前面的 LOG 的流程类似，只不过这回 send method 是 `SendToSyslogAndLog()`

这个 method 核心

- 调用 `openlog()` 设置往 syslog 的连接
- 用 `syslog()` 写日志

## **Automatically Remove Old Logs**

核心是利用 `LogCleaner` 全局对象

```cpp
void EnableLogCleaner(unsigned int overdue_days) {
  log_cleaner.Enable(overdue_days);
}

void DisableLogCleaner() {
  log_cleaner.Disable();
}
```

在 `LogFileObject::Write()` 最后执行清理

```cpp
// Remove old logs
if (log_cleaner.enabled()) {
  log_cleaner.Run(base_filename_selected_,
                  base_filename_,
                  filename_extension_);
}
```

`Run()` 其实就是

1. 遍历每个 log directory
2. 在 directory 中找到找到属于当前进程的日志文件，并且这个文件已经 overdue 了，塞到 overdue file list 中
3. 然后对每个文件逐一删除（调用 `unlink()`）

## Crash/Signal Handler

现代一些的 Linux 发行版都支持 sigaction，所以 glog 这里也就强制要求用这个来完成 signal handler 安装。

```cpp
void InstallFailureSignalHandler() {
#ifdef HAVE_SIGACTION
  // Build the sigaction struct.
  struct sigaction sig_action;
  memset(&sig_action, 0, sizeof(sig_action));
  sigemptyset(&sig_action.sa_mask);
  sig_action.sa_flags |= SA_SIGINFO;
  sig_action.sa_sigaction = &FailureSignalHandler;

  for (size_t i = 0; i < ARRAYSIZE(kFailureSignals); ++i) {
    CHECK_ERR(sigaction(kFailureSignals[i].number, &sig_action, NULL));
  }
  kFailureSignalHandlerInstalled = true;
#elif defined(GLOG_OS_WINDOWS)
  for (size_t i = 0; i < ARRAYSIZE(kFailureSignals); ++i) {
    CHECK_NE(signal(kFailureSignals[i].number, &FailureSignalHandler),
             SIG_ERR);
  }
  kFailureSignalHandlerInstalled = true;
#endif  // HAVE_SIGACTION
}

const struct {
  int number;
  const char *name;
} kFailureSignals[] = {
  { SIGSEGV, "SIGSEGV" },
  { SIGILL, "SIGILL" },
  { SIGFPE, "SIGFPE" },
  { SIGABRT, "SIGABRT" },
#if !defined(GLOG_OS_WINDOWS)
  { SIGBUS, "SIGBUS" },
#endif
  { SIGTERM, "SIGTERM" },
};
```

<aside>
💡 不过在 Windows 也用 `signal()` 安装 handler 这是我没想到的

</aside>

---

在多线程环境下，可能会有多个线程同时触发 signal handler，比如

- 虽然某些 signal 只会发给 calling thread，但是架不住多个线程（多核下）并行同时触发
- 触发了发给所有 thread 的 handler （不过我怀疑安装了 handler 的 signal 没有这样的…）

所以 glog 进入 signal handler 之后首先要检查自己是否是第一个，用的是 CAS 指令，并且

- 如果自己不是第一个，则无限 sleep；也就是等待第一个处理的线程完成 dumping
- 如果自己是第一个线程并且发生了重入，这种情况是严重错误，所以立即调用默认的 handler 挂掉
- 如果真的是第一个，那就开始 dump。

dumping 过程包含

1. dump `RIP` 所在的栈/符号信息，这里用的 ucontext 并且只针对 Linux
2. dump signal 自身信息
3. dump stacktrace

不同平台/环境下 stacktrace 获取方法还不一样，这里 Linux 上推荐的是安装 libunwind 之后走 libunwind 的实现，最靠谱。

glibc 的实现（也即 `backtrace()` ）很容易死锁

还有 libgcc/llvm 提供的 `_Unwind_Backtrace`

Windows 上就省事多了，直接 `CaptureStackBackTrace()`。