---
title: Build Your Own HandlerThread Part 4
categories: PROGRAMMING
date: 2018-12-07 00:50:37
tags: [Android, HandlerThread, Looper, Handler, MessageLoop]
---
有了前面的铺垫我们终于可以开始实现我们的主角 ActiveThread 了，虽然它登场的有点晚。

这个系列的开头我们提到 `ActiveThread` 有两个鲜明的特点：

- 它是线程，可以运行，代表一个单独的执行上下文（execution unit/context）
- 每个 `ActiveThread` 内部运行一个 message-loop，方便持续的执行我们提交的任务

这两点和 Android 原生提供的 `HandlerThread` 是一模一样的。

不过和 `HandlerThread` 不同，我们这里不打算采用继承 `Thread` 的方式，而是采用 composition。原因之一是我个人非常反感传统的 OO 继承手法；并且 Java 8 开始正式支持 lambda 之后，不用继承我们的工作也可以做得很好。

不适用继承同时有个好处，我们可以只暴露我们需要的接口，避免误用（比如经典的用 `run()` 而不是 `start()`）
<!-- more -->
```java
import java.util.concurrent.locks.Condition;

public class ActiveThread {
    private Thread _thread;
    private MessageLoop _loop;
    private AutoReentrantLock _loopInitLock = new AutoReentrantLock();
    private Condition _loopInited = _loopInitLock.newCondition();

    public ActiveThread(String name) {
        _thread = new Thread(this::runThread, name);
    }

    public void start() {
        // Doesn't block on here
        _thread.start();
    }

    public boolean quit() {
        if (getMessageLoop() == null) {
            return false;
        }

        // Once we reach here, the message-loop has been inited, and no lock needed.
        _loop.quit();

        return true;
    }

    public long getId() {
        return _thread.getId();
    }

    public String getName() {
        return _thread.getName();
    }

    public boolean isAlive() {
        return _thread.isAlive();
    }

    // Block until the message-loop is prepared.
    public MessageLoop getMessageLoop() {
        if (!_thread.isAlive()) {
            return null;
        }

        try (AutoCloseableLock lock = _loopInitLock.lockAsAuto()) {
            while (_thread.isAlive() && _loop == null) {
                try {
                    _loopInited.await();
                } catch (InterruptedException ignored) {
                }
            }
        }

        return _loop;
    }

    private void runThread() {
        MessageLoop.prepare();

        try (AutoCloseableLock lock = _loopInitLock.lockAsAuto()) {
            _loop = MessageLoop.current();
            _loopInited.signalAll();
        }

        MessageLoop.loop();

        MessageLoop.reset();
    }
}
```

通过调用 `start()` 开始运行线程后，我们准备的 `runThread()` 函数会被执行，这个函数的作用就是

1. 准备消息循环
2. 执行消息循环

一旦这个函数返回，就代表着我们的线程即将狗带。

外界通过 `getMessageLoop()` 来获取线程的消息循环，这点和 `HandlerThread.getLooper()` 类似。

并且，因为线程初始化时可能会和用户在外部尝试获取 message-loop 产生 race-condition，所以这里我们使用一个 condition-variable 来做同步，保证对 message-loop 的访问是线程安全的。

另外，一旦 `_loop` 被设置，后续的函数对它只有读操作，不会引发 race-condition。

注：对初始化 latency 不是很敏感的程序，例如网络服务程序，在这种环境下可以直接 block 线程的 `start()` 函数，保证 `start()` 返回后 message-loop 已经是正常工作的。

### Misc

如果线程还需要暴露一些操作，比如设置 exception handler 啥的，可以直接通过 ActiveThread 的函数做一层转发即可。

整个基础设施而言，最复杂的是如何设计 `MessageLoop` （以及内部的 `MessagePump`）。有了这些基础工作，上面的活都是水到渠成。
