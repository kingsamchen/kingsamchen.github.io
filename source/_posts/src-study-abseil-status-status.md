---
title: abseil status/status 源码分析
categories: PROGRAMMING
date: 2024-05-12 14:52:37
tags: [cpp, abseil, absl::Status]
toc: true
---
Revision：

- LTS_2024_01_16

💡 LTS_2024_01_16 开始 rep_ 成员的解释开始发生变化，所以之前版本的需要找对应源码

## Status 成员结构

`Status` 只有一个成员，即 `rep_`

```cpp
  // Status supports two different representations.
  //  - When the low bit is set it is an inlined representation.
  //    It uses the canonical error space, no message or payload.
  //    The error code is (rep_ >> 2).
  //    The (rep_ & 2) bit is the "moved from" indicator, used in IsMovedFrom().
  //  - When the low bit is off it is an external representation.
  //    In this case all the data comes from a heap allocated Rep object.
  //    rep_ is a status_internal::StatusRep* pointer to that structure.
  uintptr_t rep_;
```

这个 64-bit 长的成员做了一些空间优化：

- 可能是 error code (`rep_ >> 2`)，称之为 *inlined representation*
- 也可能代表某个堆内存的地址，类型是 `status_internal::StatusRep*`

第一种情况出现于没有 message 或者 payload 的情况下，按照目前实际的使用情况看，对应的基本是返回 Ok status 的场景，因为这是大多数情况下的结果

💡 即使是 non-ok status，也可以没有具体 message，这个时候也是第一种情况

在这个设计下 Status 本体的大小只有 8-byte，在函数间传递非常轻量

```cpp
// 8-byte
constexpr auto size = sizeof(absl::Status);
```

## Ok Status

### 1. absl::OkStatus()

最推荐的方式，也是大多数时候创建 ok status 的方式

```cpp
inline Status OkStatus() { return Status(); }
```

内部就是直接调用 default constructor

### 2. default constructor

```cpp
// kOk == int(0)
inline Status::Status() : rep_(CodeToInlinedRep(absl::StatusCode::kOk)) {}

constexpr uintptr_t Status::CodeToInlinedRep(absl::StatusCode code) {
  return (static_cast<uintptr_t>(code) << 2) + 1;
}

constexpr absl::StatusCode Status::InlinedRepToCode(uintptr_t rep) {
  ABSL_ASSERT(IsInlined(rep));
  return static_cast<absl::StatusCode>(rep >> 2);
}

constexpr bool Status::IsInlined(uintptr_t rep) { return (rep & 1) != 0; }
```

- inline rep 模式下，low bit（最低位）一定是1，这也是 `Status::IsInlined()` 的检查思路
- inline-rep-to-code 的时候不用 -1，因为右移的时候会自动覆盖最低位

### 3. Status(absl::StatusCode code, absl::string_view msg)

这个函数内部专门针对 `absl::StatusCode::kOk` 做了优化，如果用户真的用这个构造函数并且传了 `kOk` ，那么 `msg` 会被忽略

注意：`StatusCode::kOk == 0`

```cpp
Status::Status(absl::StatusCode code, absl::string_view msg)
    : rep_(CodeToInlinedRep(code)) {
  if (code != absl::StatusCode::kOk && !msg.empty()) {
    rep_ = PointerToRep(new status_internal::StatusRep(code, msg, nullptr));
  }
}
```

### 4. 推论： Ok status 的 msg 是事先硬编码的

直接看一下 `Status::ToString()`

```cpp
inline std::string Status::ToString(StatusToStringMode mode) const {
  return ok() ? "OK" : ToStringSlow(mode);
}
```

## Non-ok Status

以 InternalError 为例，一般创建 non-ok status 的做法是用 help function

```cpp
auto s = absl::InternalError("something went wrong");
```

等价于

```cpp
Status InternalError(absl::string_view message) {
  return Status(absl::StatusCode::kInternal, message);
}

Status::Status(absl::StatusCode code, absl::string_view msg)
    : rep_(CodeToInlinedRep(code)) {
  if (code != absl::StatusCode::kOk && !msg.empty()) {
    rep_ = PointerToRep(new status_internal::StatusRep(code, msg, nullptr));
  }
}
```

因为这次 `msg` 不为空，所以需要创建额外的 Rep 对象

```cpp
rep_ = PointerToRep(new status_internal::StatusRep(code, msg, nullptr));

// =>

inline uintptr_t Status::PointerToRep(
    absl::Nonnull<status_internal::StatusRep*> rep) {
  return reinterpret_cast<uintptr_t>(rep);
}

inline absl::Nonnull<const status_internal::StatusRep*> Status::RepToPointer(
    uintptr_t rep) {
  assert(!IsInlined(rep));
  return reinterpret_cast<const status_internal::StatusRep*>(rep);
}

class StatusRep {
 public:
  StatusRep(absl::StatusCode code_arg, absl::string_view message_arg,
            std::unique_ptr<status_internal::Payloads> payloads_arg)
      : ref_(int32_t{1}),
        code_(code_arg),
        message_(message_arg),
        payloads_(std::move(payloads_arg)) {}

  absl::StatusCode code() const { return code_; }
  const std::string& message() const { return message_; }

  ...

 private:
  mutable std::atomic<int32_t> ref_;
  absl::StatusCode code_;

  // As an internal implementation detail, we guarantee that if status.message()
  // is non-empty, then the resulting string_view is null terminated.
  // This is required to implement 'StatusMessageAsCStr(...)'
  std::string message_;
  std::unique_ptr<status_internal::Payloads> payloads_;
};
```

当 Status 需要表示 non-trivial case 时就需要动态创建 `StatusRep` 对象，这个对象保存：

- status code
- message
- payload
- ref count，因为是新构造实例，所以计数自动设置为1

并且当前实现版本，`rep_` 保存的就是这个堆上对象的指针

### 1. Check if ok

```cpp
bool ok = s.ok();

// =>

inline bool Status::ok() const {
  return rep_ == CodeToInlinedRep(absl::StatusCode::kOk);
}
```

这里没有先获取 status code 再比较是否是 kOk，应该是为了性能考虑，毕竟这个调用是及其高频的

注意，为了保证 OK status 具有唯一的 rep 值，对于 OK status 的情况一定不能使用动态分配的 StatusRep 保存

这也是前面对于 OK status 会自动丢弃 message 的原因

### 2. 检查是否是某种错误

其实就是常用的 `absl::IsXXXX()`

这类依然以 InternalError 为例

```cpp
bool IsInternal(const Status& status) {
  return status.code() == absl::StatusCode::kInternal;
}
```

既然不是高频调用，就走传统一点的做法，获取 status code 进行比较

其他类别的也是类似的处理

### 3. 读取 code

```cpp
inline absl::StatusCode Status::code() const {
  return status_internal::MapToLocalCode(raw_code());
}

// =>

inline int Status::raw_code() const {
  if (IsInlined(rep_)) return static_cast<int>(InlinedRepToCode(rep_));
  return static_cast<int>(RepToPointer(rep_)->code());
}

absl::StatusCode StatusRep::code() const { return code_; }

// Convert canonical code to a value known to this binary.
absl::StatusCode MapToLocalCode(int value) {
  absl::StatusCode code = static_cast<absl::StatusCode>(value);
  switch (code) {
    case absl::StatusCode::kOk:
    case absl::StatusCode::kCancelled:
    case absl::StatusCode::kUnknown:
    case absl::StatusCode::kInvalidArgument:
    case absl::StatusCode::kDeadlineExceeded:
    case absl::StatusCode::kNotFound:
    case absl::StatusCode::kAlreadyExists:
    case absl::StatusCode::kPermissionDenied:
    case absl::StatusCode::kResourceExhausted:
    case absl::StatusCode::kFailedPrecondition:
    case absl::StatusCode::kAborted:
    case absl::StatusCode::kOutOfRange:
    case absl::StatusCode::kUnimplemented:
    case absl::StatusCode::kInternal:
    case absl::StatusCode::kUnavailable:
    case absl::StatusCode::kDataLoss:
    case absl::StatusCode::kUnauthenticated:
      return code;
    default:
      return absl::StatusCode::kUnknown;
  }
}
```

- 获取 raw code 时需要考虑当前内部的 rep，分别获取
- 最后返回前还需要做一个是否当前实现可识别的 status code 检查；不认识的一律当作 unknown 返回

这样一套下来确实不太适合作为 `ok()` 这样的高频调用的实现

### 4. 获取 message

前面提到了 non-ok status 要存储 error message 的话就要额外在堆上创建一个 StatusRep 对象来存放 error message，所以这部分读取类似前面的 raw code，要从 StatusRep 上取

```cpp
inline absl::string_view Status::message() const {
  return !IsInlined(rep_)
             ? RepToPointer(rep_)->message()
             : (IsMovedFrom(rep_) ? absl::string_view(kMovedFromString)
                                  : absl::string_view());
}

// =>

// MSVC 14.0 limitation requires the const.
static constexpr const char kMovedFromString[] =
    "Status accessed after move.";

// 2 = 0b10
constexpr bool Status::IsMovedFrom(uintptr_t rep) { return (rep & 2) != 0; }
```

特地针对 moved status 做了处理，猜测是为了发现潜在 use-after-move 特意做的

💡 OkStatus 的 `message()` 也返回空字符串，这里需要注意和 `ToString()` 是不同的

## Move 语义处理

上面提到 Status 特意针对 move 做了一些处理，所以这里直接继续看它的 move ctor 和 move assignment

```cpp
  // The moved-from state is valid but unspecified.
  Status(Status&&) noexcept;
  Status& operator=(Status&&);
```

- move ctor 是 noexcept 但是 move assignment 并不是

### 1. 移动构造

```cpp
inline Status::Status(Status&& x) noexcept : Status(x.rep_) {
  x.rep_ = MovedFromRep();
}

// =>

// It's in private access leve.
// Underlying constructor for status from a rep_.
explicit Status(uintptr_t rep) : rep_(rep) {}

// Status code is set to (kInternal | mark)
constexpr uintptr_t Status::MovedFromRep() {
  return CodeToInlinedRep(absl::StatusCode::kInternal) | 2;
}
```

调用私有的构造直接让新对象使用 moved-from 对象的 rep

然后将 moved-from 对象的 rep 专门设置为一个带 bit-mark 的值

注意，这个 rep 仍然是满足 `IsInlined()` 的，因为它的最低位是 1；所以这里的 moved-from bit mark 是次低位

对 Status 对象来说，只要是 inlined rep，就说明没有关联堆上分配的 StatusRep，自身析构的时候不需要做任何额外的操作

### 2. 移动赋值

移动赋值需要处理原对象，所以会麻烦一些

```cpp
inline Status& Status::operator=(Status&& x) {
  uintptr_t old_rep = rep_;
  if (x.rep_ != old_rep) {
    rep_ = x.rep_;
    x.rep_ = MovedFromRep();
    Unref(old_rep);
  }
  return *this;
}

// =>

inline void Status::Unref(uintptr_t rep) {
  if (!IsInlined(rep)) RepToPointer(rep)->Unref();
}
```

`x.rep_ != old_rep` 成立有两种情况

1. 两个 status 都是 inlined rep，这种是 trivial case，不需要做任何事
2. 两个 status 都是 non-inlined rep，并且内部都指向同一个堆上的 StatusRep；这种情况也不需要做任何操作

因为堆上的 StatusRep 是自带引用计数的，所以移动后，原来的 StatusRep 的计数需要自减

💡 其实严格来说移动赋值这里也是可以实现成 noexcept 的，`Unref()` 并未涉及可能抛出异常的操作，并且后文可以发现析构函数直接就调用这个

## Ref-counted StatusRep

为了 non-inlined rep 情况下 Status 对象也能够**轻量高效的传递**，`StatusRep` 设计上是 ref-counted，并且考虑到了不同的 Status 对象内部引用同一个 `StatusRep` 的情况，这个 ref-counted 是可以做到 thread-safe 的

```cpp
inline void Status::Ref(uintptr_t rep) {
  if (!IsInlined(rep)) RepToPointer(rep)->Ref();
}

inline void Status::Unref(uintptr_t rep) {
  if (!IsInlined(rep)) RepToPointer(rep)->Unref();
}

// Internal

class StatusRep {
  // ...
  mutable std::atomic<int32_t> ref_;
};

// Ref and unref are const to allow access through a const pointer, and are
// used during copying operations.
void StatusRep::Ref() const { ref_.fetch_add(1, std::memory_order_relaxed); }

void StatusRep::Unref() const {
  // Fast path: if ref==1, there is no need for a RefCountDec (since
  // this is the only reference and therefore no other thread is
  // allowed to be mucking with r).
  if (ref_.load(std::memory_order_acquire) == 1 ||
      ref_.fetch_sub(1, std::memory_order_acq_rel) - 1 == 0) {
    delete this;
  }
}
```

`Status` 的 `Ref()`/`Unref()` 是 static 函数

`StatusRep::Unref()` 的 fast path 优化的原因是：

- 虽然引用计数是 thread-safe 的，但是 Status 对象本身不是 thread-safe
- 所以在遵循 thread-safe access 前提下，如果当前 Status 的 rep reference count 是1，则说明当前的 Status 是唯一一个 refer 这个 StatusRep 的对象，因为多个线程同时访问同一个 Status 本身就是 non-thread-safe 的

计数减到0之后的 delete this 是侵入式引用计数的老操作了

## Copy 语义操作

Status 语义上呈现值语义，所以肯定是要支持 copy semantics 的

Copy 的处理和 Move 非常像，最大的区别在于源对象需要继续保持，同时如果是 non-inlined rep，计数需要自增：

```cpp
inline Status::Status(const Status& x) : Status(x.rep_) { Ref(rep_); }

inline Status& Status::operator=(const Status& x) {
  uintptr_t old_rep = rep_;
  if (x.rep_ != old_rep) {
    Ref(x.rep_);
    rep_ = x.rep_;
    Unref(old_rep);
  }
  return *this;
}
```

因为 `Status::Ref()` 和 `Status::Unref()` 内部会透明地处理 inlined rep 的情况，所以拷贝和移动的实现上非常干净

## 析构

分析完 ref counting 部分之后这里就比较简单

```cpp
inline Status::~Status() { Unref(rep_); }
```

## 输出流

Status 重载了 `operator<<()` 所以可以直接往 stream 输出

```cpp
std::ostream& operator<<(std::ostream& os, const Status& x) {
  os << x.ToString(StatusToStringMode::kWithEverything);
  return os;
}
```

### 1. ToString

```cpp
inline std::string Status::ToString(StatusToStringMode mode) const {
  return ok() ? "OK" : ToStringSlow(rep_, mode);
}

// =>

std::string Status::ToStringSlow(uintptr_t rep, StatusToStringMode mode) {
  if (IsInlined(rep)) {
    return absl::StrCat(absl::StatusCodeToString(InlinedRepToCode(rep)), ": ");
  }
  return RepToPointer(rep)->ToString(mode);
}

// =>

std::string StatusCodeToString(StatusCode code) {
  switch (code) {
    case StatusCode::kOk:
      return "OK";
    case StatusCode::kCancelled:
      return "CANCELLED";
    case StatusCode::kUnknown:
      return "UNKNOWN";
    case StatusCode::kInvalidArgument:
      return "INVALID_ARGUMENT";
    case StatusCode::kDeadlineExceeded:
      return "DEADLINE_EXCEEDED";
    case StatusCode::kNotFound:
      return "NOT_FOUND";
    case StatusCode::kAlreadyExists:
      return "ALREADY_EXISTS";
    case StatusCode::kPermissionDenied:
      return "PERMISSION_DENIED";
    case StatusCode::kUnauthenticated:
      return "UNAUTHENTICATED";
    case StatusCode::kResourceExhausted:
      return "RESOURCE_EXHAUSTED";
    case StatusCode::kFailedPrecondition:
      return "FAILED_PRECONDITION";
    case StatusCode::kAborted:
      return "ABORTED";
    case StatusCode::kOutOfRange:
      return "OUT_OF_RANGE";
    case StatusCode::kUnimplemented:
      return "UNIMPLEMENTED";
    case StatusCode::kInternal:
      return "INTERNAL";
    case StatusCode::kUnavailable:
      return "UNAVAILABLE";
    case StatusCode::kDataLoss:
      return "DATA_LOSS";
    default:
      return "";
  }
}

std::string StatusRep::ToString(StatusToStringMode mode) const {
  std::string text;
  absl::StrAppend(&text, absl::StatusCodeToString(code()), ": ", message());

  const bool with_payload = (mode & StatusToStringMode::kWithPayload) ==
                            StatusToStringMode::kWithPayload;

  if (with_payload) {
    status_internal::StatusPayloadPrinter printer =
        status_internal::GetStatusPayloadPrinter();
    this->ForEachPayload([&](absl::string_view type_url,
                             const absl::Cord& payload) {
      absl::optional<std::string> result;
      if (printer) result = printer(type_url, payload);
      absl::StrAppend(
          &text, " [", type_url, "='",
          result.has_value() ? *result : absl::CHexEscape(std::string(payload)),
          "']");
    });
  }

  return text;
}
```

1. OK Status 会有 short cut, 这个前面也分析过了
2. 如果是 Inlined rep 则不管是不是 ok status，都没有 message，所以只需要返回 status code 对应的文字描述即可，这里 `StatusCodeToString()` 没有用额外的数据结构，而是直接用 switch..case 打表
3. 有 message 则一定是 non-inlined rep，那就要把 status code 拼上 message
4. 如果有额外的 payload（存储在 StatusRep 上，具体后文分析）则如果 mode 需要 stringify payload 则要一并处理

## Payload

这个功能应该是 Google 内部的需求吧，起码我自己在日常中几乎没用过…

Abseil-cpp 要求 payload 需要以 `[key, value]` pair 的方式管理，并且

- key 要符合 URL 的格式规范
- value 的类型是 `absl::Cord`，一个转为 large string data 优化的 string-like type

### 1. Set payload

```cpp
inline void Status::SetPayload(absl::string_view type_url, absl::Cord payload) {
  if (ok()) return;
  status_internal::StatusRep* rep = PrepareToModify(rep_);
  rep->SetPayload(type_url, std::move(payload));
  rep_ = PointerToRep(rep);
}

// =>

absl::Nonnull<status_internal::StatusRep*> Status::PrepareToModify(
    uintptr_t rep) {
  if (IsInlined(rep)) {
    return new status_internal::StatusRep(InlinedRepToCode(rep),
                                          absl::string_view(), nullptr);
  }
  return RepToPointer(rep)->CloneAndUnref();
}

absl::Nonnull<StatusRep*> StatusRep::CloneAndUnref() const {
  // Optimization: no need to create a clone if we already have a refcount of 1.
  if (ref_.load(std::memory_order_acquire) == 1) {
    // All StatusRep instances are heap allocated and mutable, therefore this
    // const_cast will never cast away const from a stack instance.
    //
    // CloneAndUnref is the only method that doesn't involve an external cast to
    // get a mutable StatusRep* from the uintptr_t rep stored in Status.
    return const_cast<StatusRep*>(this);
  }
  std::unique_ptr<status_internal::Payloads> payloads;
  if (payloads_) {
    payloads = absl::make_unique<status_internal::Payloads>(*payloads_);
  }
  auto* new_rep = new StatusRep(code_, message_, std::move(payloads));
  Unref();
  return new_rep;
}

// =>

std::unique_ptr<status_internal::Payloads> StatusRep::payloads_;

void StatusRep::SetPayload(absl::string_view type_url, absl::Cord payload) {
  if (payloads_ == nullptr) {
    payloads_ = absl::make_unique<status_internal::Payloads>();
  }

  absl::optional<size_t> index =
      status_internal::FindPayloadIndexByUrl(payloads_.get(), type_url);
  if (index.has_value()) {
    (*payloads_)[index.value()].payload = std::move(payload);
    return;
  }

  payloads_->push_back({std::string(type_url), std::move(payload)});
}

// =>

// Container for status payloads.
struct Payload {
  std::string type_url;
  absl::Cord payload;
};

using Payloads = absl::InlinedVector<Payload, 1>;
```

OkStatus 是不应该携带 payload 的，所以 set 直接返回

Non-ok status 需要区分是不是 inlined rep，这里的核心点是这样：

- StatusRep 可能被多个 Non-ok Status 共享，而 set payload 会需要修改到 StatusRep 内部状态，所以这本只是一个 copy-on-write 的操作
- 如果是 inlined，则没有共享的 StatusRep，所以只需要创建一个全新的 StatusRep 就行
- 小优化的目的也是如此：考虑到大多数时候一个 StatusRep 不会被多个 Status 共享，所以单独判断计数来做 short cut 还是有优势的

`absl::InlinedVector` 是 absl 的类 SSO 的 vector 优化版本，这里 unique_ptr 来保存 inlined vector 也算一个优化：因为大多数时候 status 是没有 payload 的，所以不希望白白浪费内存，所以这部分利用 unique_ptr 仅在需要的时候存储。

而如果需要 payload 了，大多数时候都只有一个（考虑一下 `absl::Cord` 能存大字符串），所以用 inlined vector 还能避免无谓的动态内存分配

### 2. Get payload

```cpp
inline absl::optional<absl::Cord> Status::GetPayload(
    absl::string_view type_url) const {
  if (IsInlined(rep_)) return absl::nullopt;
  return RepToPointer(rep_)->GetPayload(type_url);
}

absl::optional<absl::Cord> StatusRep::GetPayload(
    absl::string_view type_url) const {
  absl::optional<size_t> index =
      status_internal::FindPayloadIndexByUrl(payloads_.get(), type_url);
  if (index.has_value()) return (*payloads_)[index.value()].payload;

  return absl::nullopt;
}
```

Get payload 不涉及修改，所以很直接的实现，没啥好说的

### 3. Erase payload

erase 也是一个写操作，所以也需要处理 copy-on-write

```cpp
inline bool Status::ErasePayload(absl::string_view type_url) {
  if (IsInlined(rep_)) return false;
  status_internal::StatusRep* rep = PrepareToModify(rep_);
  auto res = rep->ErasePayload(type_url);
  rep_ = res.new_rep;
  return res.erased;
}

// =>

StatusRep::EraseResult StatusRep::ErasePayload(absl::string_view type_url) {
  absl::optional<size_t> index =
      status_internal::FindPayloadIndexByUrl(payloads_.get(), type_url);
  if (!index.has_value()) return {false, Status::PointerToRep(this)};
  payloads_->erase(payloads_->begin() + index.value());
  if (payloads_->empty() && message_.empty()) {
    // Special case: If this can be represented inlined, it MUST be inlined
    // (== depends on this behavior).
    EraseResult result = {true, Status::CodeToInlinedRep(code_)};
    Unref();
    return result;
  }
  return {true, Status::PointerToRep(this)};
}
```

Erase 部分有个trick：如果 erase payload 之后这个 StatusRep 全空了，即 status 可以被 inlined rep，那么就把他 inlined rep；否则前面的 inlined/non-inlined 的前提就不一致了。

### 4. Visit payloads

这部分比较有意思

```cpp
// Status::ForEachPayload()
//
// Iterates over the stored payloads and calls the
// `visitor(type_key, payload)` callable for each one.
//
// NOTE: The order of calls to `visitor()` is not specified and may change at
// any time.
//
// NOTE: Any mutation on the same 'absl::Status' object during visitation is
// forbidden and could result in undefined behavior.
inline void Status::ForEachPayload(
    absl::FunctionRef<void(absl::string_view, const absl::Cord&)> visitor)
    const {
  if (IsInlined(rep_)) return;
  RepToPointer(rep_)->ForEachPayload(visitor);
}

// =>

void StatusRep::ForEachPayload(
    absl::FunctionRef<void(absl::string_view, const absl::Cord&)> visitor)
    const {
  if (auto* payloads = payloads_.get()) {
    bool in_reverse =
        payloads->size() > 1 && reinterpret_cast<uintptr_t>(payloads) % 13 > 6;

    for (size_t index = 0; index < payloads->size(); ++index) {
      const auto& elem =
          (*payloads)[in_reverse ? payloads->size() - 1 - index : index];

#ifdef NDEBUG
      visitor(elem.type_url, elem.payload);
#else
      // In debug mode invalidate the type url to prevent users from relying on
      // this string lifetime.

      // NOLINTNEXTLINE intentional extra conversion to force temporary.
      visitor(std::string(elem.type_url), elem.payload);
#endif  // NDEBUG
    }
  }
}
```

首先可以把 `absl::FunctionRef<>` 当作 `const std::function<>&` 等价物，这是 absl 自己做的一个优化

其次函数 contract 中说如果有多个 payloads，那么他们的遍历顺序是不确定的，并且为了贯彻这个行为，遍历的时候做了一个很有意思的概率处理：有一定概率是反向遍历的

```cpp
reinterpret_cast<uintptr_t>(payloads) % 13 > 6;
```

这个 trick 感觉可以借鉴一下，开销比用 rand 会低很多
