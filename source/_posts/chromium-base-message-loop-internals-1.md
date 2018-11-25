---
title: Chromium Base MessageLoop Internals (1)
categories: PROGRAMMING
date: 2018-11-25 22:47:07
tags: [source internals, chromium, message-loop]
---
## class MessageLoop

Version: r70_3538
File: `base/message_loop/message_loop.{h, cc}`

A `MessageLoop` is used to process events for a particular thread, i.e. the core infrastructure for implementing Communicating Sequential Process (CSP) model.

There is at most one MessageLoop instance on a thread.

`MessageLoop` is a farily complex class, it driveds from multiple base classes:

```cpp
class BASE_EXPORT MessageLoop : public MessagePump::Delegate,
                                public RunLoop::Delegate,
                                public MessageLoopCurrent {
    // omitted...
};
```

### MessageLoop Type

A MessageLoop has a particular type, differred by the set of asynchronous events it is capable of handling.
<!-- more -->
```cpp
  // TYPE_DEFAULT
  //   This type of ML only supports tasks and timers.
  //
  // TYPE_UI
  //   This type of ML also supports native UI events (e.g., Windows messages).
  //   See also MessageLoopForUI.
  //
  // TYPE_IO
  //   This type of ML also supports asynchronous IO.  See also
  //   MessageLoopForIO.
  //
  // TYPE_JAVA
  //   This type of ML is backed by a Java message handler which is responsible
  //   for running the tasks added to the ML. This is only for use on Android.
  //   TYPE_JAVA behaves in essence like TYPE_UI, except during construction
  //   where it does not use the main thread specific pump factory.
  //
  // TYPE_CUSTOM
  //   MessagePump was supplied to constructor.
  //
  enum Type {
    TYPE_DEFAULT,
    TYPE_UI,
    TYPE_CUSTOM,
    TYPE_IO,
#if defined(OS_ANDROID)
    TYPE_JAVA,
#endif  // defined(OS_ANDROID)
  };
```

The `TYPE_CUSTOM` is recently added, IIRC.

### Create a MessageLoop

The common usage of MessageLoop is as follows

```cpp
// Create a TYPE_DEFAULT message-loop.
base::MessageLoop loop;

base::RunLoop run_loop;

// run_loop internally calls loop.Run()
run_loop.Run();
```

Although `base::MessageLoop::Run()` is private, it is actually inherited from `base::RunLoop::Delegate`, in which the function is public. So we simply skip `base::RunLoop` related context here and just focus on `base::MessageLoop` itself.

Let's first check its constructor:

```cpp
using MessagePumpFactoryCallback =
    OnceCallback<std::unique_ptr<MessagePump>()>;

// type = TYPE_DEFAULT
MessageLoop::MessageLoop(Type type)
    : MessageLoop(type, MessagePumpFactoryCallback()) {
  BindToCurrentThread();
}
```

The ctor
1. delegates the construction to another ctor
2. calls `BindToCurrentThread()`

and we know that pump factory callback must whatever returns a `std::unique_ptr<MessagePump>()` instance, and for the message-loop in default-type, the factory callback is empty.

Now, we should better continue our construction-adventure, examing what the delegatee constructor does.

```cpp
MessageLoop::MessageLoop(Type type, MessagePumpFactoryCallback pump_factory)
    : MessageLoopCurrent(this),
      type_(type),
      pump_factory_(std::move(pump_factory)),
      message_loop_controller_(
          new Controller(this)),  // Ownership transferred on the next line.
      underlying_task_runner_(MakeRefCounted<internal::MessageLoopTaskRunner>(
          WrapUnique(message_loop_controller_))),
      sequenced_task_source_(underlying_task_runner_.get()),
      task_runner_(underlying_task_runner_) {
  // If type is TYPE_CUSTOM non-null pump_factory must be given.
  DCHECK(type_ != TYPE_CUSTOM || !pump_factory_.is_null());

  // Bound in BindToCurrentThread();
  DETACH_FROM_THREAD(bound_thread_checker_);
}
```

One of its bases, `MessageLoopCurrent` is created, as we are creating a MessageLoop bound for the current thread.

Next few fancy members are actually `underlying_task_runner`-centric; a `MessageLoopTaskRunner` receives and queues tasks destined to its owning `MessageLoop`, and this `MessageLoop` will extract its tasks from the `underlying_task_runner`.

So, we can think this underlying task runner is the source of tasks the message-loop demands.

`message_loop_controller`, in `MessageLoop::Controller`, is used by the uderlying-task-runner, to control its owning message-loop in some limited ways.

Note, `DETACH_FROM_THREAD()` here has nothing to do with our topic, so we skip it too and turn our spotlight on `BindToCurrentThread()`.

This function configures various members and binds this message loop to the current thread.

```cpp
void MessageLoop::BindToCurrentThread() {
  DCHECK_CALLED_ON_VALID_THREAD(bound_thread_checker_);

  DCHECK(!pump_);
  if (!pump_factory_.is_null())
    pump_ = std::move(pump_factory_).Run();
  else
    pump_ = CreateMessagePumpForType(type_);

  DCHECK(!MessageLoopCurrent::IsSet())
      << "should only have one message loop per thread";
  MessageLoopCurrent::BindToCurrentThreadInternal(this);

  underlying_task_runner_->BindToCurrentThread();
  message_loop_controller_->StartScheduling();
  SetThreadTaskRunnerHandle();
  thread_id_ = PlatformThread::CurrentId();

  scoped_set_sequence_local_storage_map_for_current_thread_ = std::make_unique<
      internal::ScopedSetSequenceLocalStorageMapForCurrentThread>(
      &sequence_local_storage_map_);

  RunLoop::RegisterDelegateForCurrentThread(this);

#if defined(OS_ANDROID)
  // On Android, attach to the native loop when there is one.
  if (type_ == TYPE_UI || type_ == TYPE_JAVA)
    static_cast<MessagePumpForUI*>(pump_.get())->Attach(this);
#endif
}
```

For what interests us, this function mainly
1. creates the message-pump the loop is going to use.
2. binds the instance to `MessageLoopCurrent`'s TLS member
3. binds the underlying-task-runner to the thread
4. starts scheduling via message-loop-controller

Then we see what does `message_loop_controller_->StartScheduling()` do inside.

```cpp
void MessageLoop::Controller::StartScheduling() {
  AutoLock lock(message_loop_lock_);
  DCHECK(message_loop_);
  DCHECK(!is_ready_for_scheduling_);
  is_ready_for_scheduling_ = true;
  if (pending_schedule_work_)
    message_loop_->ScheduleWork();
}
```

Initially, `is_ready_for_scheduling_` and `pending_schedule_work_` are both `false`, and the latter aren't `true` until `MessageLoop::Controller::DidQueueTask()` is called.

Thus, this function is more like to declare that the message-loop is ready for scheduling/running; and, I guess, `pending_schedue_work_` being true here should be rare and only in unusual cases.

### Run a MessageLoop

From our example

```cpp
run_loop.Run();
```

is equivalent to

```cpp
base::MessageLoop loop;

// application_tasks_allowed == true
loop.Run(true);
```

Let's see it.

```cpp
void MessageLoop::Run(bool application_tasks_allowed) {
  DCHECK_CALLED_ON_VALID_THREAD(bound_thread_checker_);
  if (application_tasks_allowed && !task_execution_allowed_) {
    // Allow nested task execution as explicitly requested.
    DCHECK(RunLoop::IsNestedOnCurrentThread());
    task_execution_allowed_ = true;
    pump_->Run(this);
    task_execution_allowed_ = false;
  } else {
    pump_->Run(this);
  }
}
```

Key point here is

```cpp
pump_->Run(this);
```

and calling this function will 'block' on here, until the message-pump quits.

Similarly for `MessageLoop::Quit()`, which forwards the call to `MessagePump::Quit()`.

So I guess member `pump_`'s type, `MessagePump` its our next big guy to trace.

### class MessagePump

File: `base/message_loop/message_pump*.{h, cc}`

`MessagePump` is an abstract/interface class and its whole hierarchy is fairly complex; and yet, it defines the basic routine of what `MessagePump::Run()` should do.

```cpp
// The anatomy of a typical run loop:

for (;;) {
  bool did_work = DoInternalWork();
  if (should_quit_)
    break;

  did_work |= delegate_->DoWork();
  if (should_quit_)
    break;

  TimeTicks next_time;
  did_work |= delegate_->DoDelayedWork(&next_time);
  if (should_quit_)
    break;

  if (did_work)
    continue;

  did_work = delegate_->DoIdleWork();
  if (should_quit_)
    break;

  if (did_work)
    continue;

  WaitForWork();
}
```

`DoInternalDowrk()` is highly related with the actual pump we are using:
- for UI-pump, it receives and dispatches UI events from native system
- for IO-pump, it receives and notifies IO events
- but for Default-pump, it even does not provide this kind of function, because there is no such _internal_ work for this kind of pump.

And for simplicity, we focus on the class `MessagePumpDefault` this time.

The `MessagePumpDefault` is simple and succinct, yet with full capabilities for normal message-loop uses.

Ctor and dtor of the class are both simple:

```cpp
MessagePumpDefault::MessagePumpDefault()
    : keep_running_(true),
      event_(WaitableEvent::ResetPolicy::AUTOMATIC,
             WaitableEvent::InitialState::NOT_SIGNALED) {}

MessagePumpDefault::~MessagePumpDefault() = default;
```

`event_` is a `WaitableEvent` and used for controlling execution of the pump.

Now let us see the key function, `Run()`:

```cpp
void MessagePumpDefault::Run(Delegate* delegate) {
  AutoReset<bool> auto_reset_keep_running(&keep_running_, true);

  for (;;) {
#if defined(OS_MACOSX)
    mac::ScopedNSAutoreleasePool autorelease_pool;
#endif

    bool did_work = delegate->DoWork();
    if (!keep_running_)
      break;

    did_work |= delegate->DoDelayedWork(&delayed_work_time_);
    if (!keep_running_)
      break;

    if (did_work)
      continue;

    did_work = delegate->DoIdleWork();
    if (!keep_running_)
      break;

    if (did_work)
      continue;

    ThreadRestrictions::ScopedAllowWait allow_wait;
    if (delayed_work_time_.is_null()) {
      event_.Wait();
    } else {
      // No need to handle already expired |delayed_work_time_| in any special
      // way. When |delayed_work_time_| is in the past TimeWaitUntil returns
      // promptly and |delayed_work_time_| will re-initialized on a next
      // DoDelayedWork call which has to be called in order to get here again.
      event_.TimedWaitUntil(delayed_work_time_);
    }
    // Since event_ is auto-reset, we don't need to do anything special here
    // other than service each delegate method.
  }
}
```

Some observations:
- no internal work for default pump
- if had work done, try to handle subsequent work immedately
- otherwise, block until being wakeup or for a given period (`delayed_work_time_`)
- actual works are done by pump's owner, the owning `MessageLoop`, via `MessagePump::Delegate`.
- after execution of each kind of work, it checks if the loop should quit, which is indicated by `keep_running_`

This loop model, doesn't handles all pending tasks at once, it instead handles one task of a specific kind at one iteration, to make the whole loop more responsive.

By the way, quitting the pump-loop is simply to make `keep_running_ = false`, which is exactly what `MessagePump::Quit()` does:

```cpp
void MessagePumpDefault::Quit() {
  keep_running_ = false;
}
```

We now focus on `MessageLoop` again, to examine how works are processed.

From code above, we know there are three categories of work:
- work
- delayed work
- idle work

Let's go through one-by-one:

```cpp
bool MessageLoop::DoWork() {
  if (!task_execution_allowed_)
    return false;

  // Execute oldest task.
  while (sequenced_task_source_->HasTasks()) {
    PendingTask pending_task = sequenced_task_source_->TakeTask();
    if (pending_task.task.IsCancelled())
      continue;

    if (!pending_task.delayed_run_time.is_null()) {
      int sequence_num = pending_task.sequence_num;
      TimeTicks delayed_run_time = pending_task.delayed_run_time;
      pending_task_queue_.delayed_tasks().Push(std::move(pending_task));
      // If we changed the topmost task, then it is time to reschedule.
      if (pending_task_queue_.delayed_tasks().Peek().sequence_num ==
          sequence_num) {
        pump_->ScheduleDelayedWork(delayed_run_time);
      }
    } else if (DeferOrRunPendingTask(std::move(pending_task))) {
      return true;
    }
  }

  // Nothing happened.
  return false;
}

bool MessageLoop::DeferOrRunPendingTask(PendingTask pending_task) {
  if (pending_task.nestable == Nestable::kNestable ||
      !RunLoop::IsNestedOnCurrentThread()) {
    RunTask(&pending_task);
    // Show that we ran a task (Note: a new one might arrive as a
    // consequence!).
    return true;
  }

  // We couldn't run the task now because we're in a nested run loop
  // and the task isn't nestable.
  pending_task_queue_.deferred_tasks().Push(std::move(pending_task));
  return false;
}
```

The function first checks to see if there is any pending task in the `sequenced_task_source_`, then exracts it out and
- saves into the `pending_task_queue_.delayed_task` queue, if the task is a delayed-task
- runs the pending task

Note, if the delayed task would expire earlier than any tasks in the delayed queue, the message-loop will reset expiration time.

This is done via `MessagePump::ScheduleDelayedWork()`:

```cpp
void MessagePumpDefault::ScheduleDelayedWork(
    const TimeTicks& delayed_work_time) {
  // We know that we can't be blocked on Wait right now since this method can
  // only be called on the same thread as Run, so we only need to update our
  // record of how long to sleep when we do sleep.
  delayed_work_time_ = delayed_work_time;
}
```

Delayed tasks in `pending_task_queue_.delayed_task` will be processed subsequentely inside `MessageLoop::DoDelayedWork()`:

```cpp
bool MessageLoop::DoDelayedWork(TimeTicks* next_delayed_work_time) {
  if (!task_execution_allowed_ ||
      !pending_task_queue_.delayed_tasks().HasTasks()) {
    *next_delayed_work_time = TimeTicks();
    return false;
  }

  // When we "fall behind", there will be a lot of tasks in the delayed work
  // queue that are ready to run.  To increase efficiency when we fall behind,
  // we will only call Time::Now() intermittently, and then process all tasks
  // that are ready to run before calling it again.  As a result, the more we
  // fall behind (and have a lot of ready-to-run delayed tasks), the more
  // efficient we'll be at handling the tasks.

  TimeTicks next_run_time =
      pending_task_queue_.delayed_tasks().Peek().delayed_run_time;

  if (next_run_time > recent_time_) {
    recent_time_ = TimeTicks::Now();  // Get a better view of Now();
    if (next_run_time > recent_time_) {
      *next_delayed_work_time = CapAtOneDay(next_run_time);
      return false;
    }
  }

  PendingTask pending_task = pending_task_queue_.delayed_tasks().Pop();

  if (pending_task_queue_.delayed_tasks().HasTasks()) {
    *next_delayed_work_time = CapAtOneDay(
        pending_task_queue_.delayed_tasks().Peek().delayed_run_time);
  }

  return DeferOrRunPendingTask(std::move(pending_task));
}
```

This function runs every tasks that has been expired.

Because we said before, that the pump handles a task at a time, so the above code contains a minor optimization for handling delayed takss: it calls `TimeTicks::Now()` only when `next_run_time` maybe in the future.

Doing so can reduce number of times it calls `Time::Now()`.

Function `MessageLoop::DoIdleWork()` is rather boring, it does some metrics work only.

### Post Tasks

Two frequently used functions for adding a task to the message-loop are:
- PostTask()
- PostDelayedTask()

and in fact, `PostTask()` is merely a call of `PostDelayedTask()` with 0-delay.

And in our example, this function is implemented by class `MessageLoopTaskRunner`.

For brevity, I deleted some not-so-related code on purpose:

```cpp
bool MessageLoopTaskRunner::PostDelayedTask(const Location& from_here,
                                            OnceClosure task,
                                            base::TimeDelta delay) {
  return AddToIncomingQueue(from_here, std::move(task), delay,
                            Nestable::kNestable);
}

bool MessageLoopTaskRunner::AddToIncomingQueue(const Location& from_here,
                                               OnceClosure task,
                                               TimeDelta delay,
                                               Nestable nestable) {
  PendingTask pending_task(from_here, std::move(task),
                           CalculateDelayedRuntime(from_here, delay), nestable);

#if defined(OS_WIN)
  // We consider the task needs a high resolution timer if the delay is
  // more than 0 and less than 32ms. This caps the relative error to
  // less than 50% : a 33ms wait can wake at 48ms since the default
  // resolution on Windows is between 10 and 15ms.
  if (delay > TimeDelta() &&
      delay.InMilliseconds() < (2 * Time::kMinLowResolutionThresholdMs)) {
    pending_task.is_high_res = true;
  }
#endif

  bool was_empty;
  {
    AutoLock auto_lock(incoming_queue_lock_);
    if (accept_new_tasks_) {
      // Initialize the sequence number. The sequence number is used for delayed
      // tasks (to facilitate FIFO sorting when two tasks have the same
      // delayed_run_time value) and for identifying the task in about:tracing.
      pending_task.sequence_num = next_sequence_num_++;

      task_source_observer_->WillQueueTask(&pending_task);

      was_empty = outgoing_queue_empty_ && incoming_queue_.empty();
      incoming_queue_.push(std::move(pending_task));
    }
  }

  // Let |task_source_observer_| know about the task just queued. It's important
  // to do this outside of |incoming_queue_lock_| to avoid blocking tasks
  // incoming from other threads on the resolution of DidQueueTask().
  task_source_observer_->DidQueueTask(was_empty);
  return true;
}

TimeTicks CalculateDelayedRuntime(const Location& from_here, TimeDelta delay) {
  return delay > TimeDelta() ? TimeTicks::Now() + delay : TimeTicks();
}
```

We can see that, if a task is a delayed task, its time-detal will be converted into an absolute time-tick.

Then the task is enqueued into `incoming_queue_`, which is

```cpp
using TaskQueue = base::queue<PendingTask>;

// An incoming queue of tasks that are acquired under a mutex for processing
// on this instance's thread. These tasks have not yet been been pushed to
// |outgoing_queue_|.
TaskQueue incoming_queue_;
```

Insertion is done with a lock being hold, because the task may come from other threads.

Finally it notifies a task was queued, to let the pump reschedule if necessary.

Retrieval of tasks out of the runner is not so complicated too:

```cpp
// Tasks to be returned to TakeTask(). Reloaded from |incoming_queue_| when
// it becomes empty.
TaskQueue outgoing_queue_;

PendingTask MessageLoopTaskRunner::TakeTask() {
  // Must be called on execution sequence, unless clearing tasks from an unbound
  // MessageLoop.
  DCHECK(RunsTasksInCurrentSequence() || valid_thread_id_ == kInvalidThreadId);

  // HasTasks() will reload the queue if necessary (there should always be
  // pending tasks by contract).
  const bool has_tasks = HasTasks();
  DCHECK(has_tasks);

  PendingTask pending_task = std::move(outgoing_queue_.front());
  outgoing_queue_.pop();
  return pending_task;
}

bool MessageLoopTaskRunner::HasTasks() {
  // Must be called on execution sequence, unless clearing tasks from an unbound
  // MessageLoop.
  DCHECK(RunsTasksInCurrentSequence() || valid_thread_id_ == kInvalidThreadId);

  if (outgoing_queue_.empty()) {
    AutoLock lock(incoming_queue_lock_);
    incoming_queue_.swap(outgoing_queue_);
    outgoing_queue_empty_ = outgoing_queue_.empty();
  }

  return !outgoing_queue_.empty();
}
```

`HasTasks()` has major side-effects, it reloads `outgoing_queue_` from `incoming_queue_` when necessary; while `TaskTask()` simply extracts a task from the `outgoing_queue_`.

**NOTE**: accesses to `outgoing_queue_` is thread-safe without locks, because we only dequeue tasks on the thread running the message-loop.

Using two queues here, is to reduce contention.

For completeness, we now take a look at `pending_task_queue_.delayed_tasks`, which stores timed-tasks.

```cpp
DelayedQueue delayed_tasks_;

// The queue for holding tasks that should be run later and sorted by expected
// run time.
class DelayedQueue : public Queue {
public:
  DelayedQueue();
  ~DelayedQueue() override;

private:
  DelayedTaskQueue queue_;

  DISALLOW_COPY_AND_ASSIGN(DelayedQueue);
};

// PendingTasks are sorted by their |delayed_run_time| property.
using DelayedTaskQueue = std::priority_queue<base::PendingTask>;
```

um...it is a simple priority queue.