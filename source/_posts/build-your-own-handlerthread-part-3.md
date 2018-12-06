---
title: Build Your Own HandlerThread Part 3
categories: PROGRAMMING
date: 2018-12-06 22:34:48
tags: [Android, HandlerThread, Looper, Handler, MessageLoop]
---
前面我们实现了 `MessageLoop`，现在该轮到 `TaskRunner` 了。

在我们的设计中，`TaskRunner` 的定位是类似 `Handler`，用来驱动/使用 `MessageLoop`，将 `MessageLoop` 隐藏在日常使用中。

`TaskRunner` 的实现比较简单，因为几乎所有的实际工作都是由内部的 `MessageLoop` 完成。

```java
// Our MessageLoop doesn't provide a way to complete pending tasks when it is asked to quit.
// Therefore our post-task methods don't care about if a task was submitted.

import java.time.Instant;

public class TaskRunner {
    private final MessageLoop _loop;

    // Uses current thread's owning MessageLoop.
    // Throws an exception if the thread has no bound MessageLoop.
    public TaskRunner() {
        _loop = MessageLoop.current();
    }

    public TaskRunner(MessageLoop loop) {
        _loop = loop;
    }

    public MessageLoop getMessageLoop() {
        return _loop;
    }

    public void postTask(Runnable r) {
        _loop.enqueueTask(new PendingTask(r, Instant.EPOCH));
    }

    public void postTaskAt(Runnable r, long uptimeMillis) {
        _loop.enqueueTask(new PendingTask(r, Instant.ofEpochMilli(uptimeMillis)));
    }

    public void postDelayedTask(Runnable r, long delayMillis) {
        _loop.enqueueTask(new PendingTask(r, Instant.now().plusMillis(delayMillis)));
    }

    public void runTask(Runnable r) {
        if (_loop.belongsToCurrentThread()) {
            r.run();
        } else {
            postTask(r);
        }
    }
}
```

和 `Handler` 一个最明显的不同之处仅是我们增加了 `runTask()` 函数。

和 `postTask()` 不同，如果当前线程就是 loop thread，`r` 会被立即执行。这在某些时候是一个优势。
