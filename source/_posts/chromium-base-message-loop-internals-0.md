---
title: Chromium Base MessageLoop Internals (0)
categories: PROGRAMMING
date: 2018-11-04 10:04:38
tags: [source internals, chromium, message-loop]
---
Our fancy star in this post is class `base::MessageLoopCurrent`.

## MessageLoopCurrent

Version: r70_3538
File: `base/message_loop/message_loop_current.{h, cc}`

MessageLoopCurernt is a proxy class for interactions with the MessageLoop bound to the current thread.

It is introduced to avoid direct uses of `MessageLoop::current()`, quoting from original comments:

> Why: Historically MessageLoop::current() gave access to the full MessageLoop API, preventing both addition of powerful owner-only APIs as well as making it harder to remove callers of deprecated APIs (that need to stick around for a few owner-only use cases and re-accrue callers after cleanup per remaining publicly available).

Because it is a light-weight proxy, it contains only a single pointer to the MessageLoop bound to the current thread.
<!-- more -->
It also comes with **value semantics**.

```c++
class BASE_EXPORT MessageLoopCurrent {
public:
  // MessageLoopCurrent is effectively just a disguised pointer and is fine to
  // copy around.
  MessageLoopCurrent(const MessageLoopCurrent& other) = default;
  MessageLoopCurrent& operator=(const MessageLoopCurrent& other) = default;

  // Returns a proxy object to interact with the MessageLoop running the
  // current thread. It must only be used on the thread it was obtained.
  static MessageLoopCurrent Get();

  // Irrelevant code omitted...

protected:
  explicit MessageLoopCurrent(MessageLoop* current) : current_(current) {}

  MessageLoop* const current_;
};
```

The constructor is protected because the class wants users to access it via explicitly `Get()`:

```cpp
// Note it is a static member function.
MessageLoopCurrent::Get();
```

Other major functions of MessageLoopCurrent simply forward invocations to `current_`, except manipulations on thread-local `MessageLoop` instance.

Suprisingly, the tls for MessageLoop instance now is managed by MessageLoopCurrent, over several static functions:

```cpp
namespace {

base::ThreadLocalPointer<MessageLoop>* GetTLSMessageLoop() {
  static NoDestructor<ThreadLocalPointer<MessageLoop>> lazy_tls_ptr;
  return lazy_tls_ptr.get();
}

}  // namespace

// Returns true if the current thread is running a MessageLoop. Prefer this to
// verifying the boolean value of Get() (so that Get() can ultimately DCHECK
// it's only invoked when IsSet()).
static bool IsSet();

// Binds |current| to the current thread. It will from then on be the
// MessageLoop driven by MessageLoopCurrent on this thread. This is only meant
// to be invoked by the MessageLoop itself.
static void BindToCurrentThreadInternal(MessageLoop* current);

// Unbinds |current| from the current thread. Must be invoked on the same
// thread that invoked |BindToCurrentThreadInternal(current)|. This is only
// meant to be invoked by the MessageLoop itself.
static void UnbindFromCurrentThreadInternal(MessageLoop* current);

// Returns true if |message_loop| is bound to MessageLoopCurrent on the
// current thread. This is only meant to be invoked by the MessageLoop itself.
static bool IsBoundToCurrentThreadInternal(MessageLoop* message_loop);
```

By the way, the TLS instance is exposed with a wrapping function `GetTLSMessageLoop()`, preventing any direct accesses on the variable.

## MessageLoopCurrentForUI and MessageLoopCurrentForIO

They are derived classes from MessageLoopCurrent, and both specialized for UI-message-loop and IO-message-loop.

Therefore, other than a pointer to the MessageLoop instance, they also contain a pointer to an associated message-pump.

```cpp
// ForUI extension of MessageLoopCurrent.
class BASE_EXPORT MessageLoopCurrentForUI : public MessageLoopCurrent {
 public:
  // Returns an interface for the MessageLoopForUI of the current thread.
  // Asserts that IsSet().
  static MessageLoopCurrentForUI Get();

  // irrelevant code omitted...

 private:
  MessageLoopCurrentForUI(MessageLoop* current, MessagePumpForUI* pump)
      : MessageLoopCurrent(current), pump_(pump) {
    DCHECK(pump_);
  }

  MessagePumpForUI* const pump_;
};

// static
MessageLoopCurrentForUI MessageLoopCurrentForUI::Get() {
  MessageLoop* loop = GetTLSMessageLoop()->Get();
  DCHECK(loop);
#if defined(OS_ANDROID)
  DCHECK(loop->IsType(MessageLoop::TYPE_UI) ||
         loop->IsType(MessageLoop::TYPE_JAVA));
#else   // defined(OS_ANDROID)
  DCHECK(loop->IsType(MessageLoop::TYPE_UI));
#endif  // defined(OS_ANDROID)
  auto* loop_for_ui = static_cast<MessageLoopForUI*>(loop);
  return MessageLoopCurrentForUI(
      loop_for_ui, static_cast<MessagePumpForUI*>(loop_for_ui->pump_.get()));
}
```

MessageLoopForIO is quite similarly:

```cpp
// static
MessageLoopCurrentForIO MessageLoopCurrentForIO::Get() {
  MessageLoop* loop = GetTLSMessageLoop()->Get();
  DCHECK(loop);
  DCHECK_EQ(MessageLoop::TYPE_IO, loop->type());
  auto* loop_for_io = static_cast<MessageLoopForIO*>(loop);
  return MessageLoopCurrentForIO(
      loop_for_io, static_cast<MessagePumpForIO*>(loop_for_io->pump_.get()));
}
```
