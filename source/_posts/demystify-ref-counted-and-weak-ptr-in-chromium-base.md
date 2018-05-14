---
title: 浅析 RefCounted 和 WeakPtr：Chromium Base 篇
categories: PROGRAMMING
date: 2018-05-14 14:47:38
tags: [shared_ptr-internals, source-code-study, shared_ptr, weak_ptr, c++, chromium, base lib]
---
序言请移步[此处](https://kingsamchen.github.io/2018/03/13/shared-ptr-internals-introduction/)

MSVC STL 的分析版本请移步[此处](https://kingsamchen.github.io/2018/03/16/demystify-shared-ptr-and-weak-ptr-in-msvc-stl/)

Libstdc++ 的分析版本请移步[此处](https://kingsamchen.github.io/2018/03/30/demystify-shared-ptr-and-weak-ptr-in-libstdcpp/)

Boost 的分析版本请移步[此处](https://kingsamchen.github.io/2018/05/02/demystify-shared-ptr-and-weak-ptr-in-boost/)

注 1：因为这不是第一篇分析，所以会直入主题，跳过文学写作常用的累赘的过渡。

注 2：这是系列最后一篇。

## 目标版本选择

Chromium tag 68.0.3421.1

代码位置：`base/memory/ref_counted.{h, cc}`

## RefCountedBase 和 RefCounted

两个类实现了非线程安全的引用计数，即：内部计数使用的是 built-in integer

先看看 `RefCountedBase` 的大致结构：

```cpp
class RefCountedBase {
protected:
    explicit RefCountedBase(StartRefCountFromZeroTag);

    explicit RefCountedBase(StartRefCountFromOneTag);

    ~RefCountedBase();

    void AddRef() const;

    bool Release() const;

private:
    mutable uint32_t ref_count_ = 0;

#if DCHECK_IS_ON()
    mutable bool needs_adopt_ref_ = false;
    mutable bool in_dtor_ = false;
    mutable SequenceChecker sequence_checker_;
#endif

    DISALLOW_COPY_AND_ASSIGN(RefCountedBase);
};

```

可以看出核心 `ref_count_` 类型是 `uint32_t`

ctor 和 dtor 都被定义为 protected，说明这类使用做基类；同时提供了 `AddRef()` 和 `Release()`，进行内部的计数增减。

```cpp
void AddRef() const {
#if DCHECK_IS_ON()
    DCHECK(!in_dtor_);
    DCHECK(!needs_adopt_ref_)
        << "This RefCounted object is created with non-zero reference count."
        << " The first reference to such a object has to be made by AdoptRef or"
        << " MakeRefCounted.";
    if (ref_count_ >= 1) {
        DCHECK(CalledOnValidSequence());
    }
#endif
 
    AddRefImpl();
}
 
void RefCountedBase::AddRefImpl() const {
    // Check if |ref_count_| overflow only on 64 bit archs since the number of
    // objects may exceed 2^32.
    // To avoid the binary size bloat, use non-inline function here.
    CHECK(++ref_count_ > 0);
}

// Returns true if the object should self-delete. 
bool Release() const {
    --ref_count_;
 
#if DCHECK_IS_ON()
    DCHECK(!in_dtor_);
    if (ref_count_ == 0)
        in_dtor_ = true;
 
    if (ref_count_ >= 1)
        DCHECK(CalledOnValidSequence());
    if (ref_count_ == 1)
        sequence_checker_.DetachFromSequence();
#endif
 
    return ref_count_ == 0;
}
```

两个函数除了计数增减外，还有相当多用于调试检查的代码。

吐槽一句，这里把两个函数标记为 const，同时使用 mutable 成员的操作真是...

接下来看一下 `RefCounted`：

```cpp
template <typename T>
struct DefaultRefCountedTraits {
  static void Destruct(const T* x) {
    RefCounted<T, DefaultRefCountedTraits>::DeleteInternal(x);
  }
};
 
template <class T, typename Traits = DefaultRefCountedTraits<T>>
class RefCounted : public subtle::RefCountedBase {
 public:
  static constexpr subtle::StartRefCountFromZeroTag kRefCountPreference =
      subtle::kStartRefCountFromZeroTag;
 
  RefCounted() : subtle::RefCountedBase(T::kRefCountPreference) {}
 
  void AddRef() const {
    subtle::RefCountedBase::AddRef();
  }
 
  void Release() const {
    if (subtle::RefCountedBase::Release()) {
      // Prune the code paths which the static analyzer may take to simulate
      // object destruction. Use-after-free errors aren't possible given the
      // lifetime guarantees of the refcounting system.
      ANALYZER_SKIP_THIS_PATH();
 
      Traits::Destruct(static_cast<const T*>(this));
    }
  }
 
 protected:
  ~RefCounted() = default;
 
 private:
  friend struct DefaultRefCountedTraits<T>;
 
  template <typename U>
  static void DeleteInternal(const U* x) {
    delete x;
  }
 
  DISALLOW_COPY_AND_ASSIGN(RefCounted);
}; 
```

这个实现可以算是 CRTP 的经典实现，毕竟父类要正确的 delete 子类必须要保证操作能拿到子类的实际类型。

具体的 deletion 可以通过模板参数注入，默认的策略就是使用 `delete this`

还有一点需要注意一下：`RefCounted` 的构造函数结束后，计数仍然是0，除非子类修改了 preferred start-count；这意味着 `scoped_refptr` 一定会在构造时候手动调用 `AddRef()`，这点后面可以看到。

接下来看一下线程安全的 `RefCountedThreadSafeBase`：

```cpp
mutable AtomicRefCount ref_count_{0};
 
// for AddRef()
ref_count_.Increment();
 
// for Release()
if (!ref_count_.Decrement()) {
    return true;
}
return false;
```

这个版本的 `AtomicRefCount` 内部实现是 `std::atomic_int`，代码位置在 `base/atomic_ref_count.h`

因为 `RefCountedThreadSafe` 实现基本和 `RefCounted` 一致，这里就不细究了。

**Ref-count Start Policy**

当 `RefCounted` / `RefCountedThreadSafe` 初始化他们的父类时，会传入 `kRefCountPreference`，这个值指明引用计数应该从0还是1开始。这个值可以被子类重定义。

**对比 shared_ptr**

base 的 `RefCounted(ThreadSafe)` 是侵入式的实现，并且 deleter 的提供是直接作为类型一部分。

另外，因为侵入式，所以不需要 `std::enable_shared_from_this` 这类东西了。

## RAII for Reference Counting: scoped_refptr

`scoped_refptr` 在这里完全就是一个引用计数操作的 RAII 设施

```cpp
template <class T>
class scoped_refptr {
 public:
  typedef T element_type;
 
 // omitted
 
 protected:
  T* ptr_ = nullptr;
};
```

它的 `AddRef()` 和 `Release()` 被定义为了 static functions：

```cpp
// Non-inline helpers to allow:
//     class Opaque;
//     extern template class scoped_refptr<Opaque>;
// Otherwise the compiler will complain that Opaque is an incomplete type.
static void AddRef(T* ptr);
static void Release(T* ptr);
 
// static
template <typename T>
void scoped_refptr<T>::AddRef(T* ptr) {
  ptr->AddRef();
}
 
// static
template <typename T>
void scoped_refptr<T>::Release(T* ptr) {
  ptr->Release();
}
```

类似的，这里有两种方式创建一个 `scoped_refptr` 对象：

```cpp
// The old-fashioned way
scoped_refptr<MyFoo> foo(new MyFoo());
 
// The make-way
scoped_refptr<MyFoo> foo = MakeRefCounted<MyFoo>();

we check the old-fashioned way first, examing what it will do.

// Constructs from raw pointer. constexpr if |p| is null.
constexpr scoped_refptr(T* p) : ptr_(p) {
  if (ptr_)
    AddRef(ptr_);
}
```

先看看传统方式：

```cpp
// Constructs from raw pointer. constexpr if |p| is null.
constexpr scoped_refptr(T* p) : ptr_(p) {
  if (ptr_)
    AddRef(ptr_);
}
```

做了两件事：赋值 + 加计数

接下来看一下新版的 `MakeRefCounted()`：

```cpp
// Constructs an instance of T, which is a ref counted type, and wraps the
// object into a scoped_refptr<T>.
template <typename T, typename... Args>
scoped_refptr<T> MakeRefCounted(Args&&... args) {
  T* obj = new T(std::forward<Args>(args)...);
  return subtle::AdoptRefIfNeeded(obj, T::kRefCountPreference);
}
 
namespace subtle {
 
template <typename T>
scoped_refptr<T> AdoptRefIfNeeded(T* obj, StartRefCountFromZeroTag) {
  return scoped_refptr<T>(obj);
}
 
template <typename T>
scoped_refptr<T> AdoptRefIfNeeded(T* obj, StartRefCountFromOneTag) {
  return AdoptRef(obj);
}
 
}  // namespace subtle
 
// Creates a scoped_refptr from a raw pointer without incrementing the reference
// count. Use this only for a newly created object whose reference count starts
// from 1 instead of 0.
template <typename T>
scoped_refptr<T> AdoptRef(T* obj) {
  using Tag = std::decay_t<decltype(T::kRefCountPreference)>;
  static_assert(std::is_same<subtle::StartRefCountFromOneTag, Tag>::value,
                "Use AdoptRef only for the reference count starts from one.");
 
  DCHECK(obj);
  DCHECK(obj->HasOneRef());
  obj->Adopted();
  return scoped_refptr<T>(obj, subtle::kAdoptRefTag);
}
 
scoped_refptr(T* p, base::subtle::AdoptRefTag) : ptr_(p)
{}
```

这种方式是 ref-count-start-policy aware，如果选择计数从1开始，那么构造时就不会自增。

相比较而言，这也是 preferable way

剩下的最重要的点就是复制和移动，毕竟 ref-counted 最重要的就是计数相关的语义：

```cpp
// Copy constructor. This is required in addition to the copy conversion
// constructor below.
scoped_refptr(const scoped_refptr& r) : scoped_refptr(r.ptr_)
{}
 
// Copy conversion constructor.
template <typename U,
          typename = typename std::enable_if<
              std::is_convertible<U*, T*>::value>::type>
scoped_refptr(const scoped_refptr<U>& r) : scoped_refptr(r.ptr_)
{}
 
// Move constructor. This is required in addition to the move conversion
// constructor below.
scoped_refptr(scoped_refptr&& r) noexcept : ptr_(r.ptr_) {
  r.ptr_ = nullptr;
}
 
// Move conversion constructor.
template <typename U,
          typename = typename std::enable_if<
              std::is_convertible<U*, T*>::value>::type>
scoped_refptr(scoped_refptr<U>&& r) noexcept : ptr_(r.ptr_) {
  r.ptr_ = nullptr;
}
 
scoped_refptr& operator=(T* p) {
  return *this = scoped_refptr(p);
}
 
// Unified assignment operator.
scoped_refptr& operator=(scoped_refptr r) noexcept {
  swap(r);
  return *this;
}
```

## WeakPtr-related Utils

base 提供了诸如 `WeakPtr` 允许使用者检查一个对象是否已经析构（毕竟千古难题）

但是和 `std::weak_ptr` 依赖 `std::shared_ptr` 不一样，`WeakPtr` 和 `RefCounted` 是（逻辑上）互相独立的

常规用法如下：

```cpp
class Foo {
public:
    explicit Foo(std::string s)
        : str_(std::move(s)), weak_factory_(this)
    {}
 
    ~Foo() = default;
 
    const std::string& str() const noexcept
    {
        return str_;
    }
 
    base::WeakPtr<Foo> as_weak_ptr()
    {
        return weak_factory_.GetWeakPtr();
    }
 
private:
    std::string str_;
    base::WeakPtrFactory<Foo> weak_factory_;
};
 
base::WeakPtr<Foo> ptr;
 
{
    Foo f("test");
    ptr = f.as_weak_ptr();
    if (ptr) {
        std::cout << ptr->str() << std::endl;
    }
}
 
if (!ptr) {
    std::cout << "The foo has been dead\n";
}
```

先从 `WeakPtrFactory` 入手：

```cpp
namespace internal {
class BASE_EXPORT WeakPtrFactoryBase {
 protected:
  WeakPtrFactoryBase(uintptr_t ptr)
  : ptr_(ptr)
  {}
 
  ~WeakPtrFactoryBase() {
    ptr_ = 0;
  }
 
  internal::WeakReferenceOwner weak_reference_owner_;
  uintptr_t ptr_;
};
}  // namespace internal
 
template <class T>
class WeakPtrFactory : public internal::WeakPtrFactoryBase {
 public:
  explicit WeakPtrFactory(T* ptr)   // Note: ptr here usually is this-ptr.
      : WeakPtrFactoryBase(reinterpret_cast<uintptr_t>(ptr)) {}
 
  ~WeakPtrFactory() = default;
 
  WeakPtr<T> GetWeakPtr() {
    DCHECK(ptr_);
    return WeakPtr<T>(weak_reference_owner_.GetRef(),
                      reinterpret_cast<T*>(ptr_));
  }
 
  // Omitted...
 
 private:
  DISALLOW_IMPLICIT_CONSTRUCTORS(WeakPtrFactory);
};
```

`WeakPtrFactory` 没有直接定义任何成员，唯二的两个定义在父类 `WeakPtrFactoryBase`中：
- `ptr_` 保存了管理对象的指针
- `weak_reference_owner_` 用来跟踪对象是否还活着

所以看一下 `WeakReferenceOwner`

```cpp
class BASE_EXPORT WeakReferenceOwner {
 public:
  WeakReferenceOwner() = default;
 
  ~WeakReferenceOwner();
 
  WeakReference GetRef() const;
 
  bool HasRefs() const { return flag_ && !flag_->HasOneRef(); }
 
  void Invalidate();
 
 private:
  mutable scoped_refptr<WeakReference::Flag> flag_;
};
 
WeakReferenceOwner::~WeakReferenceOwner() {
  Invalidate();
}
 
WeakReference WeakReferenceOwner::GetRef() const {
  // If we hold the last reference to the Flag then create a new one.
  if (!HasRefs())
    flag_ = new WeakReference::Flag();
 
  return WeakReference(flag_);
}
 
void WeakReferenceOwner::Invalidate() {
  if (flag_) {
    flag_->Invalidate();
    flag_ = nullptr;
  }
}
 
// An inner class of WeakReference
class BASE_EXPORT Flag : public RefCountedThreadSafe<Flag> {
 public:
  Flag();
 
  void Invalidate();
  bool IsValid() const;
 
 private:
  friend class base::RefCountedThreadSafe<Flag>;
 
  ~Flag();
 
  SequenceChecker sequence_checker_;
  bool is_valid_;
};
```

`WeakReferenceOwner` 内部维护了 `WeakReference::Flag`，which 是一个引用计数对象，并且 `WeakReferenceOwner` 可以通过这个 flag 创建 `WeakReference` 对象，这个就是 `WeakReferenceOwner::GetRef()` 做的事儿。

所以我们可以提出一个假设：所有相关的 `WeakReference` 内部都有一个相同的 flag，并且这个 flag 由一个 `WeakReferenceOwner` 控制：如果 invalidate 这个 flag，比如 `WeakReferenceOwner` 进入析构了，那么所有的 `WeakReference` 都能知道他们关心的对象要狗带了。

为了证实这个假设，我们看一下 `WeakReference` 的实现：

```cpp
class BASE_EXPORT WeakReference {
 public:
  WeakReference() = default;
  explicit WeakReference(const scoped_refptr<Flag>& flag);
  ~WeakReference() = default;
 
  WeakReference(WeakReference&& other) = default;
  WeakReference(const WeakReference& other) = default;
  WeakReference& operator=(WeakReference&& other) = default;
  WeakReference& operator=(const WeakReference& other) = default;
 
  bool is_valid() const;
 
 private:
  scoped_refptr<const Flag> flag_;
};
 
WeakReference::WeakReference(const scoped_refptr<Flag>& flag) : flag_(flag)
{}
 
bool WeakReference::is_valid() const {
  return flag_ && flag_->IsValid();
}
```

从上面的实现可以看出，`WeakReference` 实例实际上代表对 flag 的一个引用访问。

接下来我们看一下 `WeakPtr` 是怎么把这些东西组合在一起的：

```cpp
class BASE_EXPORT WeakPtrBase {
 public:
  WeakPtrBase();
  ~WeakPtrBase();
 
  WeakPtrBase(const WeakPtrBase& other) = default;
  WeakPtrBase(WeakPtrBase&& other) = default;
  WeakPtrBase& operator=(const WeakPtrBase& other) = default;
  WeakPtrBase& operator=(WeakPtrBase&& other) = default;
 
  void reset() {
    ref_ = internal::WeakReference();
    ptr_ = 0;
  }
 
 protected:
  WeakPtrBase(const WeakReference& ref, uintptr_t ptr);
 
  WeakReference ref_;
 
  // This pointer is only valid when ref_.is_valid() is true.  Otherwise, its
  // value is undefined (as opposed to nullptr).
  uintptr_t ptr_;
};
 
WeakPtrBase::WeakPtrBase() : ptr_(0) {}
 
WeakPtrBase::~WeakPtrBase() = default;
 
WeakPtrBase::WeakPtrBase(const WeakReference& ref, uintptr_t ptr)
    : ref_(ref), ptr_(ptr)
{}
 
template <typename T>
class WeakPtr : public internal::WeakPtrBase {
 public:
  WeakPtr() = default;
 
  WeakPtr(std::nullptr_t) {}
 
  // Allow conversion from U to T provided U "is a" T. Note that this
  // is separate from the (implicit) copy and move constructors.
  template <typename U>
  WeakPtr(const WeakPtr<U>& other) : WeakPtrBase(other) {
    // Need to cast from U* to T* to do pointer adjustment in case of multiple
    // inheritance. This also enforces the "U is a T" rule.
    T* t = reinterpret_cast<U*>(other.ptr_);
    ptr_ = reinterpret_cast<uintptr_t>(t);
  }
  template <typename U>
  WeakPtr(WeakPtr<U>&& other) : WeakPtrBase(std::move(other)) {
    // Need to cast from U* to T* to do pointer adjustment in case of multiple
    // inheritance. This also enforces the "U is a T" rule.
    T* t = reinterpret_cast<U*>(other.ptr_);
    ptr_ = reinterpret_cast<uintptr_t>(t);
  }
 
  T* get() const {
    return ref_.is_valid() ? reinterpret_cast<T*>(ptr_) : nullptr;
  }
 
  T& operator*() const {
    DCHECK(get() != nullptr);
    return *get();
  }
  T* operator->() const {
    DCHECK(get() != nullptr);
    return get();
  }
 
  // Allow conditionals to test validity, e.g. if (weak_ptr) {...};
  explicit operator bool() const { return get() != nullptr; }
 
 private:
  friend class internal::SupportsWeakPtrBase;
  template <typename U> friend class WeakPtr;
  friend class SupportsWeakPtr<T>;
  friend class WeakPtrFactory<T>;
 
  WeakPtr(const internal::WeakReference& ref, T* ptr)
      : WeakPtrBase(ref, reinterpret_cast<uintptr_t>(ptr)) {}
};
```

`WeakPtr` 有两个成员：
- `ref_` 来反映被管理对象的生命周期
- `ptr_` 保存被管理对象的地址，仅当 `ref_` 有效时这个成员才是可以安全访问的

**Conclusion**

这部分实现和 `std::weak_ptr` 颇有类似，核心都是通过：被多个探测器(`WeakPtr`)共享的一个引用技术控制块，这里是 `WeakReference::Flag`，来保存被管理对象的状态，因为控制块的生命周期可以远超过对象本身，因此即使对象狗带了，这个控制块也可以被访问到。

当对象行将就木之际，控制块设置为 invalid 状态，这样 `WeakPtr::get()` 就能探测到对象已经挂了，返回 nullptr。

base 版本的实现的最大的缺点是：这部分不是线程安全的。

不过有时候这也是一个优点，强迫规范线程的使用，同时结合 CSP 来尽可能促进一个对象的生老病死只发生在一个县城。

## Epilogue

这篇 post 是整个系列的最后一篇了，这四篇分析过来基本也可以对 reference counting 以及 weak-reference idiom 有了一定的了解。

从这几篇 post 的源代码可以看出，Chromium 的 C++ 写的并不好，但是工程化做的很漂亮。有时候可能这点更加重要。