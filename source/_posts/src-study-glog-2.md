---
title: glog 源码分析 Part 2
categories: PROGRAMMING
date: 2023-04-16 14:10:13
tags: [glog, cpp]
toc: true
---
## 日志的时间戳 LogMessageTime

glog 使用 `LogMessageTime` 来表示一条日志的时间戳，这个结构也是 `LogMessage` 为数不多的成员之一。

这个结构的 member fields 大概有这些

```cpp
std::tm time_struct_;  // Time of creation of LogMessage
time_t timestamp_;     // Time of creation of LogMessage in seconds
int32_t usecs_;        // Time of creation of LogMessage - microseconds part
long int gmtoffset_;
```

- 时间戳上看是支持了 second 和 microsecond part
- 存了个 `tm` 主要是为了方便格式化
- `gmtoffset_` 保存的是当前时区和 UTC 相差的时间，用 second 来表示；同时要考虑夏令时。因为搜了一下 glog 其他地方没有用到这个所以后面跳过这个 field 和他相关的函数

而且这个类整体上是 immutable 的，构造函数运行完之后就是不会再变的。

```cpp
typedef double WallTime;

LogMessageTime::LogMessageTime(std::time_t timestamp, WallTime now) {
  std::tm t;
  if (FLAGS_log_utc_time)
    gmtime_r(&timestamp, &t);
  else
    localtime_r(&timestamp, &t);
  init(t, timestamp, now);
}

void LogMessageTime::init(const std::tm& t, std::time_t timestamp,
                          WallTime now) {
  time_struct_ = t;
  timestamp_ = timestamp;
  usecs_ = static_cast<int32>((now - timestamp) * 1000000);

  CalcGmtOffset();
}

// usage sampe
WallTime now = WallTime_Now();
time_t timestamp_now = static_cast<time_t>(now);
logmsgtime_ = LogMessageTime(timestamp_now, now);
```

会注意到 `WallTime` 其实是个 `double`…估计是考虑32-bit系统上要显示大整数…

计算 `usecs_` * 1000’000 是因为 `WallTime_Now()` 返回的是浮点数，整数部分是秒，小数部分是精确到微秒的余量。

```cpp
int64 CycleClock_Now() {
  // TODO(hamaji): temporary impementation - it might be too slow.
  struct timeval tv;
  gettimeofday(&tv, NULL);
  return static_cast<int64>(tv.tv_sec) * 1000000 + tv.tv_usec;
}

WallTime WallTime_Now() {
  // Now, cycle clock is retuning microseconds since the epoch.
  return CycleClock_Now() * 0.000001;
}
```

`gettimeofday()` 能提供的就是微秒级别的精度。

同时这里先放大在缩小而不是直接给 usec 部分缩小估计是避免浮点精度操作玩飞了…

## LogStream & LogStreamBuf

LogStream 提供了 stream-like 的读写能力，同时也方便自定义类型通过提供 `operator<<(std::ostream& os, const T&)` 来实现写入，因此，glog 选择从 `std::ostream` 继承来实现。

<aside>
💡 我个人不是很favor这种管理 log buffer 的方式

</aside>

```cpp
GLOG_MSVC_PUSH_DISABLE_WARNING(4275)
  class GLOG_EXPORT LogStream : public std::ostream {
GLOG_MSVC_POP_WARNING()
  public:
    LogStream(char *buf, int len, int64 ctr)
        : std::ostream(NULL),
          streambuf_(buf, len),
          ctr_(ctr),
          self_(this) {
      rdbuf(&streambuf_);
    }

    int64 ctr() const { return ctr_; }
    void set_ctr(int64 ctr) { ctr_ = ctr; }
    LogStream* self() const { return self_; }

    // Legacy std::streambuf methods.
    size_t pcount() const { return streambuf_.pcount(); }
    char* pbase() const { return streambuf_.pbase(); }
    char* str() const { return pbase(); }

  private:
    LogStream(const LogStream&);
    LogStream& operator=(const LogStream&);
    base_logging::LogStreamBuf streambuf_;
    int64 ctr_;  // Counter hack (for the LOG_EVERY_X() macro)
    LogStream *self_;  // Consistency check hack
  };

LogMessage::LogMessageData::LogMessageData()
  : stream_(message_text_, LogMessage::kMaxLogMessageLen, 0) {
}
```

- stream 真正的 buffer 存储用的是 `LogMessageData::message_text_` ， 是一个大小为 30000 的 char 数组
- stream 利用外部的 buffer 初始化自己的 streambuf_ 然后再将这个 `streambuf_` 和自身关联；因为 `std::ostream` 规定他的 buffer 类型必须是 `streambuf` 及其子类

所以自然 `LogStreamBuf` 也得从 `std::streambuf` 继承

```cpp
// LogMessage::LogStream is a std::ostream backed by this streambuf.
// This class ignores overflow and leaves two bytes at the end of the
// buffer to allow for a '\n' and '\0'.
class GLOG_EXPORT LogStreamBuf : public std::streambuf {
 public:
  // REQUIREMENTS: "len" must be >= 2 to account for the '\n' and '\0'.
  LogStreamBuf(char *buf, int len) {
    setp(buf, buf + len - 2);
  }

  // This effectively ignores overflow.
  int_type overflow(int_type ch) {
    return ch;
  }

  // Legacy public ostrstream method.
  size_t pcount() const { return static_cast<size_t>(pptr() - pbase()); }
  char* pbase() const { return std::streambuf::pbase(); }
};
```

因为这个设计，所以前面提到要给日志消息增加 `\n` 时才会需要各种 hack。

## 为何要额外调用 .stream()

一个主要的原因是每个 `LogMessage` 创建的都是临时变量，如果直接针对 `LogMessage` 提供 `operator<<` 则传统的左值引用会遇到问题

```cpp
struct Message {
    std::string s;

    ~Message() {
        std::cout << "-> " << s << "\n";
    }
};

Message& operator<<(Message& m, std::string_view s) {
    m.s.append(s);
    return m;
}

Message{} << "abc" << "def";    // compile failed
```

workaround 有两种：

1. 把 `operator<<` 实现为成员函数，但是这样一来，自定义类型就无法扩展
2. 提供右值引用的版本。可以是可以，但是非常别扭

---

另外一个原因是职责分离。

既然内部专门提供了 LogStream，为何不让定制点依附在这个类身上呢？

这样设计上显得更加清晰。

---

另外提一嘴 glog 选择 `LogStream` 继承自 `std::ostream` 有两个原因：

- 希望能对 buffer 有 fine tune 的能力，所以 `std::stringstream` 不在考虑范围内
- 用户的自定义类型只需要针对 `std::ostream` 写一遍重载即可

陈硕的 muduo 的 base/logging 就没有让 `LogStream` 继承自 `std::ostream`， 而是让用户自己针对 `LogStream` 实现一次重载。

我个人觉得这样其实更干净。

## 日志文件名规则

文件相关的逻辑在 `LogFileObject` 中。

日志文件名由两部分组成：

1. *base name* 这部分大多数时候都是固定的
2. *time-pid part* 这部分和 pid 以及时间有关，会一直变

首先，如果没有专门指定 base name（绝大多数情况是这种），那么则使用 opt-in 的 base name，有下面部分组成

```cpp
// e.g. study_glog.be72cd068940.dev.log.INFO.
<program name>.<hostname>.<user name>.log.<severity level>.
```

第二部分的时间用的是 `20230328-161945` 格式，`-` 之后的是时分秒。

而 pid 部分用的是进程主线程的 pid/tid，其实也就是当前进程的 pid。

所以可以发现：

- 千万别以为日志名 `.` 最后的数字部分是微秒，其实这是进程的 pid
- 如果一个应用是多进程，常见于当年各种 master/worker 的模型，比如 httpd 或者 nginx，则一次运行会有多个同级别的日志，每个进程都有自己的。好处是不需要系统层面的全局锁，最多只需要进程内的线程同步的小 mutex。
    
    前提是启用了 `FLAGS_timestamp_in_logfile_name`
    

## 写磁盘的时机

刷磁盘的逻辑依然在 `LogFileObject::Write()` 中：

```cpp
  // See important msgs *now*.  Also, flush logs at least every 10^6 chars,
  // or every "FLAGS_logbufsecs" seconds.
  if ( force_flush ||
       (bytes_since_flush_ >= 1000000) ||
       (CycleClock_Now() >= next_flush_time_) ) {
    FlushUnlocked();
    // ...
  }
```

1. 当日志的 severity > FLAGS_logbuflevel 时会强制 flush；这个 flag 默认值时 INFO 级别，所以 WARNING 和 ERROR 日志不会被 buffer
2. buffer 存储的字符超过 $10^6$ 个字符时会刷盘
3. 时间间隔到了也会刷盘，而每次 `next_flush_time_` 会前进 `FLAGS_logbufsecs` 而 FLAGS_logbufsecs 默认值是30s；所以默认情况下每隔 30s 会刷一下盘
    
    不过要注意的是，如果是前两个原因引起的刷盘，则时间周期会重新计时
    

```cpp
void LogFileObject::FlushUnlocked(){
  if (file_ != NULL) {
    fflush(file_);
    bytes_since_flush_ = 0;
  }
  // Figure out when we are due for another flush.
  const int64 next = (FLAGS_logbufsecs
                      * static_cast<int64>(1000000));  // in usec
  next_flush_time_ = CycleClock_Now() + UsecToCycles(next);
}
```

## 多线程/多进程的并发安全性

对于非 FATAL 日志，消息内容都是保存在 LogMessage::LogMessageData 中的，大家都是写自己的，所以没有竟态问题。

考虑一个进程中多个线程同时写日志，同时我们可能有多个 destination，所以 logging action 就需要锁保护。前面已经看过

```cpp
  // Prevent any subtle race conditions by wrapping a mutex lock around
  // the actual logging action per se.
  {
    MutexLock l(&log_mutex);
    (this->*(data_->send_method_))();
    ++num_messages_[static_cast<int>(data_->severity_)];
  }
```

也就是说这把全局锁会一直拿着，直到整个 logging action 结束，包括往文件写日志，往 sink 等写日志；这里是完全同步的。

另外 LogFileObject 自己内部也有一个锁，因为每个日志文件对应的对象也都是全局的，所以他们的操作也都需要上锁。

同时如果系统支持 fcntl 设置文件锁 flock，则日志文件还会加上独占锁；对于自定义文件名这种可能导致多个进程使用同一个日志文件的情况用文件锁“可能”是需要的，但是代码里面是只要系统函数可用就会无条件开启…

实话说，glog 这个实现里，锁是真的多…
