---
title: Build Your Own HandlerThread Part 2
categories: PROGRAMMING
date: 2018-12-05 23:49:29
tags: [Android, HandlerThread, Looper, Handler, MessageLoop]
---
有了 `MessagePumpDefault`  之后，我们就可以开始着手实现最核心的 `MessageLoop` 了。

不过在此之前，我们还首先需要实现一个辅助设施：`PendingTask`。

### PendingTask

一个 `PendingTask` instance 表示一个等待被执行的 task，并且这个 task 可能是一个 delayed task。

因此 `PendingTask` 需要能够表示时序上的顺序，这个可以利用 `Instant` 类型的一个时间戳，结合一个 `long` 类型的 sequence-number。因为有可能两个 delayed tasks 的时间戳相同，此时就必须要用 seq-num  来区分先后顺序。

另外，`PendingTask` 为了能够表示 task 语义，他必须可以被执行。这可以通过内部存储一个 `Runnable` 或者 `Callable` 成员做到。

我们的实现选择 `Runnable`，因为

- 我们当前的实现不考虑返回值，因为实现返回值和我们的主题没有直接关系。
  CSP 的并发模型上如果要考虑使用某个任务的返回值，一般会使用类似于 `PostTaskAndReplyWithResult()` 来组合两个函数；或者更一般的，使用 continuation 来 lifting restrictions
- 懒得处理异常

```java
class PendingTask {
    private static AtomicLong s_seqNumGenerator = new AtomicLong(0);

    private final long _seqNum;
    private final Instant _endTime;

    private Runnable _task;

    PendingTask(Runnable task, Instant endTime) {
        _seqNum = s_seqNumGenerator.getAndIncrement();
        _endTime = endTime;

        _task = task;
    }

    void run() {
        _task.run();
    }

    Instant endTime() {
        return _endTime;
    }

    long seqNum() {
        return _seqNum;
    }
}
```

我们使用了 `AtomicLong` 来存储当前的 sequence-number，因为我们不知道某个 `PendingTask` 会在哪个线程上被创建。
<!-- more -->
此外，为了凸显 `PendingTask` 的时序顺序性，我们需要实现他的 comparator，方便后面在一些有序容器中使用：

```java
class PendingTaskComparator implements Comparator<PendingTask> {
    @Override
    public int compare(PendingTask lhs, PendingTask rhs) {
        if (lhs.endTime().isBefore(rhs.endTime())) {
            return -1;
        }

        if (lhs.endTime().isAfter(rhs.endTime())) {
            return 1;
        }

        long diff = lhs.seqNum() - rhs.seqNum();

        if (diff < 0) {
            return -1;
        }

        if (diff > 0) {
            return 1;
        }

        return 0;
    }
}
```

（Java 不支持 operator overloads 真是因噎废食的设计....）

有了这俩，我们就可以正式开始构筑 `MessageLoop` 了。



### MessageLoop

定位上，这里的 `MessageLoop` 就是 Android 里的 `Looper`。

我们的 `MessageLoop` 需要实现 `MessagePump.Delegate`，不过柿子先挑软的捏，我们先实现比较简单的 `MessageLoop` 三杰：run, quit, 和 wakeup。

```java
public void run() {
    _pump.run(this);
}

// Thread-safe.
public void quit() {
    if (belongsToCurrentThread()) {
        _pump.quit();
    } else {
        enqueueTask(new PendingTask(_pump::quit, Instant.EPOCH));
    }
}

// Thread-safe.
public void wakeup() {
    _pump.wakeup();
}
```

三个函数都是将具体操作转发给内部的 pump 完成。

注意，这里 `wakeup()` 是线程安全的，因为我们需要在其他线程唤醒阻塞中的 message-loop；同时为了能够方便的退出消息循环，`quit()` 也设计成了线程安全的。

实现中需要一个辅助函数 `belongsToCurrentThread()` 来检测当前线程是否是 message-loop 运行的线程：

```java
// Init during construction.
private final long _ownerThreadID = Thread.currentThread().getId();

public boolean belongsToCurrentThread() {
    return _ownerThreadID == Thread.currentThread().getId();
}
```

做了上面的基础工作，接下里就可以实现 `MessagePump.Delegate` 接口里的三个函数了。

#### Delayed Works

我们先实现只涉及 delayed-task 的后两个：`doDelayedWork()` 和 `nextDelayedWorkExpiration()`。

因为我们的 message-pump 采用了**“一次（循环）只运行一个同一类任务”**的模型，因此我们用于保存 delayed tasks 的队列只需要能够每次获取过期时间最早的任务即可。

不难想到，符合这种特性的队列就是优先级队列，在 Java 里的名字是 `PriorityQueue<>`。这个类同时要求存储的元素类型要实现 `Comparable`，刚好也是我们前面实现了的。

```java
private PriorityQueue<PendingTask> _delayedTasks = new PriorityQueue<>(new PendingTaskComparator());
```

有了存任务的地方，`nextDelayedWorkExpiration()` 的实现就变得非常简单：

- 返回队首任务的过期时间
- 返回 `Instant.EPOCH` 如果队列为空

```java
@Override
public Instant nextDelayedWorkExpiration() {
    PendingTask top = _delayedTasks.peek();
    return top == null ? Instant.EPOCH : top.endTime();
}
```

有了这个函数，前面实现的 `MessagePumpDefault` 就可以根据 delayed tasks 的时序要求进行调度了。

函数 `doDelayedWork()` 的实现也很简单：如果队列不为空，并且队首的任务已经超时，那么就执行这个任务。

```java
@Override
public boolean doDelayedWork() {
    PendingTask nextTask = _delayedTasks.peek();
    if (nextTask == null) {
        return false;
    }

    Instant nextRunTime = nextTask.endTime();
    if (nextRunTime.isAfter(_recentTime)) {
        // Calibrate only when the last view of 'now' has fallen behind.
        _recentTime = Instant.now();
        if (nextRunTime.isAfter(_recentTime)) {
            return false;
        }
    }

    _delayedTasks.poll();

    nextTask.run();

    return true;
}
```

我们在上面用了一个小优化点：我们缓存了上一次调用 `Instant.now()` 的结果，并且只有当当前时间**早于**队首任务的超时时间时，我们才再次调用 `Instant.now()` 进行校准。

这么做的原因是 `Instant.now()` 的调用有一定的开销，并且如果当前累积了相当一部份的过期任务，没有必要每次都调用比较。

另外再思考一个问题：`_delayedTasks` 中的任务从何而来？

这个和实现有关，可以是外部直接存入，或者我们这里的实现，由普通的 task 在处理时“迁移“而来。

细节请接着往下看。

 #### Do Work As We Must

普通任务的处理略微麻烦点，因为定时任务一开始也是当作普通任务处理。

```java
private AutoReentrantLock _incomingTaskLock = new AutoReentrantLock();
private Queue<PendingTask> _incomingTasks = new ArrayDeque<>();

private Queue<PendingTask> _outgoingTasks = new ArrayDeque<>();

// Retrieve a pending task.
// Returns null when no pending task.
private PendingTask takeTask() {
    if (_outgoingTasks.isEmpty()) {
        try (AutoCloseableLock lock = _incomingTaskLock.lockAsAuto()) {
            _outgoingTasks.addAll(_incomingTasks);
            _incomingTasks.clear();
        }
    }

    return _outgoingTasks.poll();
}

@Override
public boolean doWork() {
    for (PendingTask nextTask = takeTask(); nextTask != null; nextTask = takeTask()) {
        if (Instant.EPOCH.equals(nextTask.endTime())) {
            nextTask.run();
            return true;
        }

        _delayedTasks.add(nextTask);
    }

    return false;
}
```

一旦任务是通过外部存入，我们就要考虑线程安全，这也是我们前面决定将定时任务的来源不对外暴露的原因，但是到了普通任务这里，我们必须要考虑外部提供以及线程安全性。

对于这种场景，常见的做法是提供一个线程安全的 queue，保证两端的读写线程安全，即构造一个 producer-consumer model。

这里我们再做一个优化：我们使用两个队列，分别存储外部传入的任务（`_incomingTasks`)以及内部 message-loop 要消化的任务(`_outgoingTasks`)。因为我们可以保证 `_outgoingTasks` 这个队列只在 message-loop 所在的线程上被访问，因此它是天然线程安全的。

这样我们就将 contention 减少到了只涉及 `_incomingTasks` 的访问上：

- 将 pending tasks 从 `_incomingTasks` 转移到 `_outgoingTasks`
- 任务将 pending task 投递到 `_incomingTasks` 中

```java
public void enqueueTask(PendingTask task) {
    try (AutoCloseableLock lock = _incomingTaskLock.lockAsAuto()) {
        _incomingTasks.add(task);
    }

    if (!belongsToCurrentThread()) {
        wakeup();
    }
}
```

投递完任务后，如果当前线程不是 message-loop 运行的线程，那么有可能那个线程正在被挂起，我们需要手动唤醒一下。

注：从这里可以看出前面 `SyncEvent` 要是 level-trigger 的原因。如果此时 message-pump 在忙，并且 `SyncEvent` 是 edge-trigger，那么就有可能 message-pump 忙完之后直接阻塞在这个 event 上，导致前面投递的任务无法得到处理。

至此，`MessageLoop` 的大部分功能都已经实现了。

#### Scaffold

为了让我们的实现看起来更像 Android 的版本，我们接下来添加一些简单的脚手架函数：

```java
private static ThreadLocal<MessageLoop> tls_loopInThread = new ThreadLocal<>();

// Can be called only once on a thread.
public static void prepare() {
    if (tls_loopInThread.get() != null) {
        throw new RuntimeException("Current thread is already bound with a MessageLoop");
    }

    tls_loopInThread.set(new MessageLoop());
}

// Unbind the MessageLoop to current thread.
public static void reset() {
    tls_loopInThread.remove();
}

public static MessageLoop current() {
    if (tls_loopInThread.get() == null) {
        throw new RuntimeException("Current thread is not bound with a MessageLoop");
    }

    return tls_loopInThread.get();
}

// Run the message-loop bound with the thread.
public static void loop() {
    MessageLoop.current().run();
}
```

这样一来我们就可以写出如下代码了：

```java
MessageLoop.prepare();
// ...
MessageLoop.loop();
```

嗯，看起来有点像了。



### Misc

前面我们用一个 priority-queue 保存 delayed tasks，这是从我们使用的 pump 模型得出的；如果是实现 event-loop 那种一次处理尽可能多的任务，使用 priority-queue 就会因为频繁的调整 min-heap 导致性能不好。

这个时候一个比较合理的做法是使用平衡树，通过类似 lower-bound / upper-bound 的上下界算法直接摘取出所有已经超时的任务。

注意，这里不能使用 hash table 作为容器，可以想一下为什么。

虽然我们有了 `MessageLoop`，但是直接使用它不是一个好的选择（在业务侧暴露了太多抽象），而且也不像 Android 的做法。

所以我们后面会实现一个类似 `Handler` 的东西，我们这里称之为 `TaskRunner`。
