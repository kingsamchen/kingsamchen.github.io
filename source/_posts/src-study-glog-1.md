---
title: glog 源码分析 Part 1
categories: PROGRAMMING
date: 2023-04-09 00:08:07
tags: [glog, cpp]
toc: true
---
Revision: v0.6.0 (latest release)

## 为什么要初始化/初始化做了什么

使用 glog 前需要在入口函数中做初始化

```cpp
int main(int argc, char *argv[]) {
  google::InitGoogleLogging(argv[0]);
  LOG(INFO) << "Welcome Study GLOG";
  return 0;
}
```

这个函数定义在 `logging.cc` 中，是内部函数的一个包装

```cpp
void InitGoogleLogging(const char* argv0) {
  glog_internal_namespace_::InitGoogleLoggingUtilities(argv0);
}

void InitGoogleLoggingUtilities(const char* argv0) {
  CHECK(!IsGoogleLoggingInitialized())
      << "You called InitGoogleLogging() twice!";
  const char* slash = strrchr(argv0, '/');
#ifdef GLOG_OS_WINDOWS
  if (!slash)  slash = strrchr(argv0, '\\');
#endif
  g_program_invocation_short_name = slash ? slash + 1 : argv0;

#ifdef HAVE_STACKTRACE
  InstallFailureFunction(&DumpStackTraceAndExit);
#endif
}
```

`argv0` 是程序的 image path，初始化操作会从这个 image path 中反向查找（`strrchr()`）分离出程序名，然后将程序名存到内部的全局变量 `g_program_invocation_short_name` 中。

而这个变量是否有值也是 glog 是否初始化过的标志。

另外如果定义了 `HAVE_STACKTRACE` 则会安装 crash handler，这个后面再看

## 单条日志流程

下面以 `LOG(ERROR) << "study glog";` 为例子研究这条日志的大致流程。

```cpp
typedef int LogSeverity;

const int GLOG_INFO = 0, GLOG_WARNING = 1, GLOG_ERROR = 2, GLOG_FATAL = 3,
  NUM_SEVERITIES = 4;

#define COMPACT_GOOGLE_LOG_ERROR google::LogMessage( \
      __FILE__, __LINE__, google::GLOG_ERROR)

#define LOG(severity) COMPACT_GOOGLE_LOG_ ## severity.stream()
```

利用宏拼接，`LOG(ERROR)` 会被转换为 `COMPACT_GOOGLE_LOG_ERROR.stream()`，然后再次转换为 `google::LogMessage(__FILE__, __LINE__, google::GLOG_ERROR).stream()`。

等价于构造一个临时的 `LogMessage` 对象，然后提供内部 stream 允许流式写入，最后在析构中将日志写入指定地点。

---

构造函数的重载很多，我们只看这个流程中用到的这个

```cpp
// Used for LOG(severity) where severity != INFO.  Implied
// are: ctr = 0, send_method = &LogMessage::SendToLog
//
// Using this constructor instead of the more complex constructor above
// saves 17 bytes per call site.
LogMessage(const char* file, int line, LogSeverity severity);

class LogMessage {
  ...
 private:
  // We keep the data in a separate struct so that each instance of
  // LogMessage uses less stack space.
  LogMessageData* allocated_;
  LogMessageData* data_;
  LogMessageTime logmsgtime_;
};
  
LogMessage::LogMessage(const char* file, int line, LogSeverity severity)
    : allocated_(NULL) {
  Init(file, line, severity, &LogMessage::SendToLog);
}
```

- 单个 `LogMessage` 实例只有如上三个成员，`LogMessageTime` 占 80-byte，两个指针加起来 16-byte，所以单个消息实例需要占用 96-byte 大小
- 因为这里把 `allocated_` 置为 nullptr 所以先跳过这个
- 真正的初始化由 `LogMessage::Init()` 函数完成，这个函数也不短
- 注意这里的 `LogMessage::SendToLog`，这个是具体的 logging strategy，细节见后面

虽然 `Init()` 做的事情很多，但是总结起来只有两件事

1. 找个地方存放 `LogMessageData` 对象，并且用 `data_` 指向它
2. 设置这个 `LogMessageData` 对象

glog 的实现中有两个地方可以存放这个 message data，1) 当前线程的 TLS 2) 直接堆上分配一个新的。

```cpp
static GLOG_THREAD_LOCAL_STORAGE bool thread_data_available = true;

static GLOG_THREAD_LOCAL_STORAGE
    std::aligned_storage<sizeof(LogMessage::LogMessageData),
                         alignof(LogMessage::LogMessageData)>::type thread_msg_data;

// No need for locking, because this is thread local.
if (thread_data_available) {
    thread_data_available = false;
    data_ = new (&thread_msg_data) LogMessageData;
} else {
    allocated_ = new LogMessageData();
    data_ = allocated_;
}
```

利用 aligned storage 在线程的 TLS 上搞了一块内存专门复用 message data 存储以减少预期外的动态内存分配。

并且有几点 implications：

- `LogMessageData` 自身不应该再有额外的动态分配操作，否则得不偿失
- 利用 placement new 在指定内存上构造对象是常用技法了
- 因为 TLS 上这样的存储只有一块，所以即使单线程下也要考虑可能会出现的“竞争”问题
    
    我暂时能想到的会导致竞争的原因大概当前 `Init()` 执行到一般触发了某个 signal handler，handler 中又打了日志；或者 `Init()` 执行中又创建新的日志消息递归地触发了 `Init()`
    
    所以为了解决潜在地竞争问题，这用同是 TLS 地 `thread_data_available` 作 guard，并且 TLS 存储不可用时 fallback 到 dynamic allocation
    
- 成员 `data_` 始终指向当前的 message data，不管这个 message data 存在哪儿；`allocated_` 指向堆上分配地 message data，所以如果是 TLS 的优化逻辑，这个 `allocated_` 始终是个 nullptr

<aside>
⚠️ 对于不支持 TLS 的架构/平台不会使用 TLS 的优化方案

</aside>

另外这里有对于平台不支持 aligned storage 时手动地址对齐的逻辑，这里不展开直接跳过。

---

因为 glog 支持 `LOG(FATAL)`，这个级别的日志会强制挂掉程序，所以概念上和设计上会区分 *first fatal* 和 *subsequent fatal*。

前者有自己专用的 message data，后者会共享一个 message data。因此针对 FATAL 级别的消息，前面的 message data 的创建流程就完全不一样了

因为两个变量都是全局的所以这里用锁保护是必须的。

```cpp
// Static log data space to avoid alloc failures in a LOG(FATAL)
//
// Since multiple threads may call LOG(FATAL), and we want to preserve
// the data from the first call, we allocate two sets of space.  One
// for exclusive use by the first thread, and one for shared use by
// all other threads.
static Mutex fatal_msg_lock;
static CrashReason crash_reason;
static bool fatal_msg_exclusive = true;
static LogMessage::LogMessageData fatal_msg_data_exclusive;
static LogMessage::LogMessageData fatal_msg_data_shared;

if (severity != GLOG_FATAL || !exit_on_dfatal) {
  // ...
  // already analyzed
} else {
  MutexLock l(&fatal_msg_lock);
  if (fatal_msg_exclusive) {
    fatal_msg_exclusive = false;
    data_ = &fatal_msg_data_exclusive;
    data_->first_fatal_ = true;
  } else {
    data_ = &fatal_msg_data_shared;
    data_->first_fatal_ = false;
  }
}
```

---

有了 message data 之后剩下的就是往 `data_->stream_` 中写入这条消息的 header，比如消息级别，时间，pid，tid

甚至还有当触发的日志的指定文件的某一行，也打入 callstack

```cpp
if (!FLAGS_log_backtrace_at.empty()) {
    char fileline[128];
    snprintf(fileline, sizeof(fileline), "%s:%d", data_->basename_, line);
#ifdef HAVE_STACKTRACE
    if (FLAGS_log_backtrace_at == fileline) {
      string stacktrace;
      DumpStackTraceToString(&stacktrace);
      stream() << " (stacktrace:\n" << stacktrace << ") ";
    }
#endif
}
```

---

真正的 logging 行为由 `LogMessage` 的析构函数完成。

```cpp
LogMessage::~LogMessage() {
  Flush();
#ifdef GLOG_THREAD_LOCAL_STORAGE
  if (data_ == static_cast<void*>(&thread_msg_data)) {
    data_->~LogMessageData();
    thread_data_available = true;
  }
  else {
    delete allocated_;
  }
#else // !defined(GLOG_THREAD_LOCAL_STORAGE)
  delete allocated_;
#endif // defined(GLOG_THREAD_LOCAL_STORAGE)
}
```

为了避免析构的逻辑理解起来过于复杂，这里把真正干活的都塞进了 `Flush()` 中，这样也方便其他场景复用。

另外可以验证前面提到的，如果 message data 是存在 TLS 的，则不需要做任何释放行为，只需要手动触发一下 `LogMessageData` 的析构函数即可。

而对于分配在 heap 上的则需要显式清理。

---

接下来重点看 `LogMessage::Flush()` 的行为。

首先对于不需要刷日志的情况就可以直接返回了

```cpp
if (data_->has_been_flushed_ || data_->severity_ < FLAGS_minloglevel) {
  return;
}
```

如果这条日志要写到 *nix 系统的 syslog，那么则不需要 message header，这会干扰 syslog，所以需要专门针对处理一下。

```cpp
data_->num_chars_to_log_ = data_->stream_.pcount();
data_->num_chars_to_syslog_ =
  data_->num_chars_to_log_ - data_->num_prefix_chars_;
```

一般来说单条日志都是需要换行的，但是这个换行符一般会希望日志库自动帮你加上。

这块刚好 glog 也有内部处理逻辑。

```cpp
// Do we need to add a \n to the end of this message?
bool append_newline =
    (data_->message_text_[data_->num_chars_to_log_-1] != '\n');
char original_final_char = '\0';

// If we do need to add a \n, we'll do it by violating the memory of the
// ostrstream buffer.  This is quick, and we'll make sure to undo our
// modification before anything else is done with the ostrstream.  It
// would be preferable not to do things this way, but it seems to be
// the best way to deal with this.
if (append_newline) {
  original_final_char = data_->message_text_[data_->num_chars_to_log_];
  data_->message_text_[data_->num_chars_to_log_++] = '\n';
}
data_->message_text_[data_->num_chars_to_log_] = '\0';

// ...

if (append_newline) {
  // Fix the ostrstream back how it was before we screwed with it.
  // It's 99.44% certain that we don't need to worry about doing this.
  data_->message_text_[data_->num_chars_to_log_-1] = original_final_char;
}
```

这里需要一大段的逻辑来处理是因为 glog 的 `LogStream` 继承自 `std::osteram` 导致不仅要遵循 `std::ostream` 的 spec 约束，也不好方便的对 stream buf 做一些 hack。

另外上面的代码可以发现，使用 glog 时如果单条消息我们显式给了一个 `\n` 那么最终输出的时候也会只有这一个 `\n`

真正写日志的操作由下面这部分代码完成

```cpp
// A mutex that allows only one thread to log at a time, to keep things from
// getting jumbled.  Some other very uncommon logging operations (like
// changing the destination file for log messages of a given severity) also
// lock this mutex.  Please be sure that anybody who might possibly need to
// lock it does so.
static Mutex log_mutex;

// Prevent any subtle race conditions by wrapping a mutex lock around
// the actual logging action per se.
{
  MutexLock l(&log_mutex);
  (this->*(data_->send_method_))();
  ++num_messages_[static_cast<int>(data_->severity_)];
}
```

翻一下前面的代码可以知道，我们的代码中 `data_->send_method_` 其实就是 `LogMessage::SendToLog()`， 并且要注意的是，调用这个函数时是 lock hold 的状态。

我们的场景下，不会设置 `FLAGS_logtostderr` 和 `FLAGS_logtostdout` 并且 glog 也是初始化的，所以会执行如下的写日志操作：

```cpp
// log this message to all log files of severity <= severity_
LogDestination::LogToAllLogfiles(data_->severity_, logmsgtime_.timestamp(),
                                 data_->message_text_,
                                 data_->num_chars_to_log_);

LogDestination::MaybeLogToStderr(data_->severity_, data_->message_text_,
                                 data_->num_chars_to_log_,
                                 data_->num_prefix_chars_);
LogDestination::MaybeLogToEmail(data_->severity_, data_->message_text_,
                                data_->num_chars_to_log_);
LogDestination::LogToSinks(data_->severity_,
                           data_->fullname_, data_->basename_,
                           data_->line_, logmsgtime_,
                           data_->message_text_ + data_->num_prefix_chars_,
                           (data_->num_chars_to_log_
                            - data_->num_prefix_chars_ - 1) );
// NOTE: -1 removes trailing \n
```

下面一个一个来展开分析。

---

首先是写日志文件：和寻常的日志库不一样，如果一条日志的级别是 K 则 glog 会将这条日志消息写入所有级别小于等于 K 的文件中。

即，`LOG(ERROR)` 的日志消息会同样写入 WARNING 和 INFO 的文件，但是 `LOG(INFO)` 的消息只会写入 INFO 文件。当然前提是这个级别没有被跳过。

这部分逻辑由专门的类 `LogDestination` 负责，并且这个类有大量的 static members…感觉不是很清真。

```cpp
inline void LogDestination::LogToAllLogfiles(LogSeverity severity,
                                             time_t timestamp,
                                             const char* message,
                                             size_t len) {
  if (FLAGS_logtostdout) {  // global flag: never log to file
    ColoredWriteToStdout(severity, message, len);
  } else if (FLAGS_logtostderr) {  // global flag: never log to file
    ColoredWriteToStderr(severity, message, len);
  } else {
    for (int i = severity; i >= 0; --i) {    // <--- here
      LogDestination::MaybeLogToLogfile(i, timestamp, message, len);
    }
  }
}
```

而每个级别的日志文件是一个全局维护的，并且：

- 创建是 lazy init 的，即第一次使用时创建
- 销毁则是需要调用 `ShutdownGoogleLogging()`

而写文件操作由内部的 `LogFileObject` 负责，它的基类是 `base::Logger`，因为还有一些其他 destination object 的需要，所以这里是做一个 sub-typing。

这里跳过一些很细碎的细节，简单讲一下日志文件的创建和写入。

为了跨平台，文件是通过 C runtime 的 `FILE*` 管理的，消息的写入也是通过 `fwrite()`。

但是在 Linux 这样的平台上还要避免文件的 fd 被子进程继承，需要设置 `FD_CLOEXEC`，所以 glog 的做法是这样的：

1. 首先用 syscall `open()` 以 `O_WRONLY | O_CREAT` 创建文件，拿到 fd
2. 再利用 `fcntl()` 设置 `FD_CLOEXEC`
3. 最后用 `fdopen()` 从 fd 创建 `FILE*`

另外创建的时候会利用 `fcntl()` 设置 `flock`， 说是为了防止多个 clients 同时写一个文件，这种情况是用户手动关闭了 `FLAGS_timestamp_in_logfile_name` 导致多个进程可能会写同一个日志文件做的处理。

写文件的操作里有一些额外的细节，这里简单提一下，但是不展开：

- 每个文件都会有固定的信息头，算作 column header 吧
- 会专门考虑磁盘写满的情况
- 会定时去 fflush 文件，保证确实往磁盘写了；时间周期默认是 `1S * FLAGS_logbufsecs`
- 还有个 log cleaner 定时清理文件

---

`MaybeLogToStderr()` 会调用

```cpp
ColoredWriteToStderrOrStdout(stderr, severity, message, len);
```

跟进这个函数就会发现实际上就是利用 `fwrite()` 往 stderr 写日志而已；只不过还会顺带利用控制字符设置控制台颜色。

---

glog 还支持将日志消息通过邮件发送…估计是没有 kibana 这种上古时代的设计吧。

默认情况下这个功能不会被开启，需要设置两个对应的 flags，可以参见函数 `MaybeLogToEmail()`

邮件发送是通过 `popen()` 直接调用 `/bin/mail` 来完成，所以这还要依赖外部程序。

另外前面我们知道日志逻辑执行到这里的时候其实是有一把大锁的，所以 `SendEmailInternal()` 还要专门考虑这种 case。

---

日志库为了考虑扩展一般会允许外部注册自定义的 log consumer，也叫 sink。

比如通过网络往 kibana 收集也可以作为 sink 之一。

```cpp
inline void LogDestination::LogToSinks(LogSeverity severity,
                                       const char* full_filename,
                                       const char* base_filename, int line,
                                       const LogMessageTime& logmsgtime,
                                       const char* message,
                                       size_t message_len) {
  ReaderMutexLock l(&sink_mutex_);
  if (sinks_) {
    for (size_t i = sinks_->size(); i-- > 0; ) {
      (*sinks_)[i]->send(severity, full_filename, base_filename,
                         line, logmsgtime, message, message_len);
    }
  }
}
```

`sink_mutex_` 保护的是 sink vector，而不是某个 sink 对象，所以这里用的读锁。

每个 sink 要从 `LogSink` 继承并且重写 `send()` 而且要保证这个函数是线程安全的。

`WaitTillSent()` 是可选的，默认实现是一个 noop。

增加/删除 sink 通过

```cpp
// Add or remove a LogSink as a consumer of logging data.  Thread-safe.
GLOG_EXPORT void AddLogSink(LogSink *destination);
GLOG_EXPORT void RemoveLogSink(LogSink *destination);
```
