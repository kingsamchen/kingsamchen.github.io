---
title: Build Your Own HandlerThread Part 1
categories: PROGRAMMING
date: 2018-12-04 09:51:41
tags: [Android, HandlerThread, Looper, Handler, MessageLoop]
---
### MessagePump

对于我们的 ActiveThread 来说，核心仍然是持续不断运行的 message-loop；但是在实现 message-loop 前，我们需要首先来实现它的“引擎”：`MessagePump`。

大体上，message-loop 要维持运转，就要能够源源不断地从某个地方获取消息/事件，而获取消息/事件的这个行为，就是由 message-pump 来完成，正如他的名字一样。

这里可能会产生一个疑问，为什么要单独抽出一个 message-pump，而不是直接把这部分实现做到 message-loop 里呢？

原因是：一个复杂的系统可能会有多个消息/事件来源，每个来源会有专门的获取、分发的方式。

例如：

- 界面消息可能是从系统的 GUI 消息系统中获得。一个典型的例子是 Win32 GUI 程序所使用的 `GetMessage()`
- 网络活动事件通常从 native 系统提供的 I/O multiplexor 中获得。例子包括 POSIX 标准的 `select(2)`, `poll(2)`；Linux 独有的 `epoll`；以及 Windows 提供的真-异步I/O机制 IO Completion Port。

注：GUI 系统中多使用 messages（消息）作为术语；而网络 I/O 等倾向于使用 events（事件）作为使用术语。因为此系列文章不涉及网络 I/O，因此以后均采用_消息_作为我们的使用术语。

注1：对于 Android 系统，他的 GUI 事件实质上是通过 `epoll` 监听关联到 device 抽象的 pipe fd 来完成的；Linux 的各类 X-Window 实现大多也是类似做法。

所以为了解耦具体的消息来源，同时让我们的实现看上去更加有逼格，我们采取分离实现 message-pump 和 message-loop。

前面说到可能有多种 pump，所以这里我们的 `MessagePump` 应该是一个 interface，规定了一个 message-pump 应该具有哪些基本的行为：

```java
import java.time.Instant;

interface MessagePump {
    interface Delegate {
        boolean doWork();
        boolean doDelayedWork();
        Instant nextDelayedWorkExpiration();
    }

    void run(Delegate delegate);

    // Quit the pump.
    // This method can only be called on the thread that called run().
    void quit();

    // Thread-safe
    void wakeup();
}
```

同时我们定义了一个 `Delegate` interface，message-loop 需要实现这个 interface，以便让 message-pump 可以通过这个 interface 来按照自己的实现来操作 message-loop。

### 什么都不做的 MessagePumpDefault

我们的 ActiveThread 运行的纯 Java 环境，不需要像 Android 那样能够获取设备输入消息（Frankly，你想做还做不了呢，我哪来的设备信息获取啊），有点类似 worker HandlerThread.
<!-- more -->
同时它也不需要参与网络 I/O，所以我们的 message-pump 实现  `MessagePumpDefault` 非常简单，他几乎什么也不做：

```java
@Override
public void run(Delegate delegate) {
    _running = true;

    while (true) {
        boolean doneWork = delegate.doWork();
        if (!_running) {
            break;
        }

        doneWork |= delegate.doDelayedWork();
        if (!_running) {
            break;
        }

        // Since we have completed at least one task, it is highly possible there are tasks
        // still pending.
        if (doneWork) {
            continue;
        }

        Instant expiration = delegate.nextDelayedWorkExpiration();
        if (Instant.EPOCH.equals(expiration)) {
            _event.await();
        } else {
            _event.awaitUntil(expiration);
        }
    }
}
```

这个 `MessagePumpDefault.run()` 就是核心的消息循环。

在我们的实现里，虽然它几乎自己什么苦差事都不做（处理任务），但是其实还是做了一些使唤别人的活；总的来看它还是做了三件小事：

1. 驱使 owning message-loop 去处理任务
   即上面的 `delegate.doWork()`
2. 驱使 owning message-loop 去处理延时任务
   即上面的 `delegate.doDelayedWork()`
3. 没活干或者时候未到的时候，让出 CPU，进入阻塞状态
   这是利用变量 `_event` 做到的。

另外注意我们在这里处理消息使用的模型：我们一次循环**最多只处理一个某种类型的消息**，然后迅速返回到 pump 中，及早进行下一次循环。

这么设计是因为对于通用的 message-pump 来说，尤其涉及到 UI 消息的，**保持一个较低的 latency** 是更加主要的目标，因为它可以避免产生明显的用户交互延迟，尤其在需要通过频繁的定时任务来完成动画绘制等操作时。

注：对于网络I/O 框架的事件循环（EventLoop）来说，除了 latency 还要考虑整体的 throughput，并且 I/O bound 的上下文里，对于低 latency 不是那么敏感。因此通常这类框架都是一次性尽可能处理更多的同类任务，临近的 I/O 操作对 cache 也更加友好 .etc

### 事件等待器

如果当前 message-loop 中没有需要处理的消息，或者只有延时任务需要处理，那么我们的 pump 需要让运行线程进入等待状态，让出 CPU，毕竟此时也没有什么事情可以做。

这里的等待是通过一个自己实现的 `SyncEvent` 做到的。

`SyncEvent` 基于最基本的 lock + condition-variable 的组合实现，这是在当前环境下最简单有效的实现方式。

因为 condition-variable 存在 spurious wakeup，因此在实现 timed-wait（即 `awaitUntil()`）方法时，要使用绝对时刻（time-point）而不能是一个时间间隔，避免出现 short-wait；这里我们用 Java 8 引入的 `Instant` 来表达。

```java
// This SyncEvent is auto-reset, i.e. after being signaled, its state automatically
// transitions into non-signaled.
class SyncEvent {

private AutoReentrantLock _lock = new AutoReentrantLock();
private Condition _cond = _lock.newCondition();

private boolean _signaled = false;

SyncEvent() {}

// Wait indefinitely for the event to be signaled.
void await() {
    try (AutoCloseableLock lock = _lock.lockAsAuto()) {
        while (!_signaled) {
            try {
                _cond.await();
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }

        _signaled = false;
    }
}

// Guarantee no short-wait caused by spurious-wakeup will happen.
boolean awaitUntil(Instant endTime) {
    boolean causedBySignaled = false;
    try (AutoCloseableLock lock = _lock.lockAsAuto()) {
        while (!_signaled && System.currentTimeMillis() < endTime.toEpochMilli()) {
            try {
                causedBySignaled = _cond.awaitUntil(Date.from(endTime));
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }

        _signaled = false;
    }

    return causedBySignaled;
}

// Signal the event.
void signal() {
    try (AutoCloseableLock lock = _lock.lockAsAuto()) {
        _signaled = true;
        _cond.signal();
    }
}

}
```

除了 spurious wakeup 之外，还有另外一个点在使用 condition-variable 时需要注意。

因为 condition-variable 本质是 edge-trigger，而我们的 `SyncEvent` 需要的是 level-trigger：即使先调用了 `signal()`，那么调用 `await()` 也应该能够立即返回。所以我们选取的状态抽象要能够方便的实现这种语义转变。

上面辅助类 `AutoReentrantLock` 是个人对 `ReentrantLock` 加上 `Closeable` 支持的一个封装。

毕竟作为一个常年写 C++ 的人，没有 RAII 都不知道怎么写代码了，XD。

```java
interface AutoCloseableLock extends AutoCloseable {
    @Override
    void close();
}

class AutoReentrantLock extends ReentrantLock {
    AutoCloseableLock lockAsAuto() {
        lock();
        return this::unlock;
    }
}
```

**Aside:** 上面实现的 `SyncEvent` 在 Windows 上有开箱即用的内核同步对象 `Event` 可用，语义几乎完全一致；除了 `Event` 内核对象稍微重型了一点。

Timed-wait 的实现通常和 pump 的具体实现有关：

- Windows 上除了常见的同步对象的定时等待之外，设计 I/O 的还可以直接通过 IOCP 的 `GetQueuedCompletionStatus(Ex)` 来实现，定时器分辨率（resolution）可以到 ms
- Linux上除了类似的多路复用器的超时等待外，稍微新一点的内核都可以使用 `timer_fd` 来产生定时的 fd 活动消息，将定时操作融入 I/O，且 resolution 至少在 us 和 ns 之间。
- 虽然 `select(2)` 从设计之初就号称支持 `nano` 级别的 resolution，但是系统并不保证一定能做到这么高的分辨率。
- `nano` 级别的定时分辨率有时候并不需要，因为除了系统不是 hard real-time system 之外，过高的定时分辨率对性能也会有一些影响。

### Pump 的唤醒和退出

这部分比较简单。

唤醒是通过 `SyncEvent.signal()` 来实现；而退出则是简单的将 `MessagePumpDefault` 成员 `_running` 设置为 `false`。

```java
@Override
public void quit() {
    _running = false;
}

@Override
public void wakeup() {
    _event.signal();
}
```

不过需要注意，`quit()` 并不是 thread-safe 的，他必须在运行 message-pump 的线程调用。

这要求上层的 message-loop 调用时需要确保这点。

### Misc

如果对 latency 没有很高的要求，那么实际上可以把 message-pump 简化到只从底层上下文（I/O multiplexor, system messaging system .etc）获取活动消息，把循环的 loop 上提到 message-loop，这样部分设计可以更加简单。

不过代价是，每次 pump 返回可能会携带大量的 active messages。

一个这样设计的例子我前段时间实现的一个 TCP 网络框架 ezio 的 [event-pump](https://github.com/kingsamchen/ezio/blob/master/ezio/event_pump.h) 和 [event-loop](https://github.com/kingsamchen/ezio/blob/master/ezio/event_loop.h)。
