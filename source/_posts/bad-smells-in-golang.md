---
title: 来一口 golang 做的玻璃渣
categories: CODE-LIFE
date: 2019-01-08 22:35:57
tags: [golang, rant]
---
转到后端差不多也快三个月了，拿 golang 糊代码的时间算上自己平时写的一些练手 demo 加起来差不多也有一个月。

这将近一个月的时间过来差不多能体会到 golang 的设计精髓，那就是：_simple & stupid, being convenient as the first class support_.

换句话说就是：短平快糙猛，满口玻璃渣，怎么方便怎么来。

所以接下来不免俗地是吐槽 golang 设计的内容。

吐槽不考虑 PLT 上的设计，纯粹从日常堆业务逻辑出发。毕竟理论的东西我一个鶸也不懂，且 golang 的设计目的就是方便应届毕业生快速堆业务代码。

以下吐槽点的顺序为自己在实际中遇到的顺序逆序。

### Flaky Goroutines

golang 里起一个 goroutine 很方便，但是目前感觉 goroutine 太过于 flaky，有点飘。一旦没用 `chan struct{}` 或者 `sync.WaitGroup` ”固定好“，就总有一种这玩意儿是不是已经脱离自己手心的感觉。

另外不知道是不是很多用 golang 的人之前都是 php / python 的背景，相当一部分人其实对 goroutine-safe 没有什么概念。不过严格来说这个不是 golang 自身的问题。



### Cannot assign to fields within short declaration notation

代码段

```go
func foobar() (bool, error) {
	return true, nil
}

func main() {
	var err error
	ok, err := foobar()
	if err != nil {
		fmt.Println("error")
	} else {
		fmt.Println(ok)
	}
}
```

是合法的，因为 `ok` 之前并没有被定义，所以这里 `err` 可以蹭着使用 `:=`。
<!-- more -->
然而，如果稍加修改：

```go
type E struct {
	err error
}

func (e *E)baz() bool {
	ok, e.err := foobar()
	return ok
}

func foobar() (bool, error) {
	return false, errors.New("foobar error")
}

func main() {
	e := new(E)
	fmt.Println(e.baz())
}
```

这次将已经定义过的符号换成一个 field，编译器就会提示你：_non-name e.err on left side of :=_

原因是 golang 将左边的部分卡死成一个 identifier。

有人为此提了一个 [issue](https://github.com/golang/go/issues/6842)，然而官方说用 workaround 啊....看起来这辈子应该都是这样了。

注：评论区高能。



### Stunning atomic.AddUint32

官方文档：https://golang.org/pkg/sync/atomic/#AddUint32

```go
func AddUint32(addr *uint32, delta uint32) (new uint32)
```

> AddUint32 atomically adds delta to *addr and returns the new value. To subtract a signed positive constant value c from x, do AddUint32(&x, ^uint32(c-1)). In particular, to decrement x, do AddUint32(&x, ^uint32(0)).

官方教你做减法 && 理解反码



### Unhandled error `conn.Close()`

这里的 `conn` 可以是一个 `net.Conn`

因为 golang 的 error handling 的哲学就是错误码自己处理，然后 `Close()` 也是可能会返回 `error` 的，所以如果你不处理的话，IDE 的静态分析就会提示你 _Unhandled error conn.Close()_

然而最尴尬的是，一般这个调用都是写成：

```go
defer conn.Close()
```

然后你猜我们会不会去处理这个 error？XD



### So primitive Errors

golang 的 error 真的就是一个**类型**，和 `string`, `int` 这种没差。

导致如果你要区分不同的 error，要么依靠 error-code，要么依靠 error-str。

如果你的业务架构是大仓，即mono-repo，混合了整个公司的基础、业务代码，那么你就会头疼了。

为了解决这个问题，我们公司甚至还搞了一个专门申请分配错误码的平台......

另外 `errors.New()` 是被禁止的，因为第一和现有的错误码对不上；第二你怎么（用字符串比较之外的方式）比较错误？

FYI：golang 的错误设计机制设计的真的非常非常不好，大业务下缺乏灵活性的弊端暴露无遗，而且每次的错误检查基本在考验人性。golang 粉只能通过不断地给自己洗脑来接纳这个搓比设计了。



### So convenient context.Value

`context` 的出现一开始是为了解决 goroutine 的 timeout 和 cancellation 的，不知道怎么又莫名其妙加入了 value 这种二百五的设计。

这个设计除了一开始看着似乎方便，其他的有点就真的没了，满地坑，宁愿换一个更稳妥的方式。

具体的吐槽和 alternatives 可以看看[这篇](https://medium.com/@cep21/how-to-correctly-use-context-context-in-go-1-7-8f2c0fafdf39)



### Value Type Method Receiver

对于一个 `type T`

```go
func (t T)Foobar() {}

func (t *T)Foobaz() {}
```

都是可以的，receiver 如果不是指针就拷贝一份。

这个设计上其实还好，不爽的是官方对这个设计的说辞，大意是更好的表达小对象 immutable 特性...

就不能诚实一点么……



### Outstanding Nil buffer.String

```go
// String returns the contents of the unread portion of the buffer
// as a string. If the Buffer is a nil pointer, it returns "<nil>".
//
// To build strings more efficiently, see the strings.Builder type.
func (b *Buffer) String() string {
	if b == nil {
		// Special case, useful in debugging.
		return "<nil>"
	}
	return string(b.buf[b.off:])
}
```

出处：https://golang.org/src/bytes/buffer.go

除了屌炸天我想不到其他形容词



### Magnificent sync.Mutex without ownership

这意味着，goroutine A 上的锁，goroutine B 可以解锁

其实就是直接把底层的 semaphore 的语义搬出来了。

这么做的现代语言除了 golang 我真的想不到了。

官方有一个（貌似是自己提的[issue](https://github.com/golang/go/issues/9201)）但是看内容似乎要不要再 go 2 里做这个修改还没确定。

另外官方对于：复杂的情况使用 channel .etc 的说法真是让人没法接受……



### Function-scope defer

golang 里 `{}` 是可以构建出一个 scope 的，然而不知道为什么 `defer` 还是设计成 function-scope

这个点其实还好，毕竟函数逻辑还可以慢慢再拆。



### 'Versatile' interface{}

没有 generic 的时候大家都说可以拿 `interface{}` 凑合一下，只是没想到这么一凑合，经常能看到满屏幕的 `interface{}` 接口，尤其是 `... interface{}` 做参数的时候。

实话说，自己手动做 type assertion 也是挺烦的。

这个导致的一个大问题就是 golang 里面真心没几个用的趁手的容器，和 Java / C# 都差远了；而造轮子的话，距离 C++ 的感觉又差远了。



### Copy without expanding

内建的 `copy(dest, src)` 在操作时实际上有一个月约束：`min(len(dest), len(src))`。

所以基本意味着 `copy()` 不会自动 expand `dest`。

这个虽然有点反直觉，但是只要文档写得清楚也就没问题了，可惜一开始 go doc 偏偏没有写上这个，直到后来才加。

出处见：https://stackoverflow.com/questions/30182538/why-can-not-i-duplicate-a-slice-with-copy-in-golang/30182622

另外，`append` 遇到 slice 也是一个大坑。



以上倒不是说是 golang 设计有什么重大缺陷，实在只是作为一个用户在日常使用过程中感受到的个各种蒙蔽和别扭。

