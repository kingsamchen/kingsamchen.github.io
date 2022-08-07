---
title: Facebook 如何实现 Golang 服务优雅重启 —— 以 Grace 为例
categories: PROGRAMMING
date: 2022-08-07 15:25:04
tags: [graceful restart, graceful shutdown]
---

[Grace](https://github.com/facebookarchive/grace) 是 Facebook 推出的一个用于 Golang 服务优雅重启的框架库，设计目的上可以作为 `net/http` 的一个 drop-in replacement。

这篇文章就来研究一下 Grace 实现优雅重启的核心方法。

## 方法论

通常来说，本机环境上实现 graceful restart 的方法都可以归纳为：

1. 保留当前进程 `A` 的情况下先创建一个新进程 `B`，并且 `B` 监听相同的端口
2. 确保新连接都是由 `B` 处理，即 A 不再接受新连接
3. `A` 处理完旧链接请求之后在关闭

其中步骤 2 和 3 可以合并，实现 graceful shutdown。

步骤1中的监听相同端口既可以通过继承父进程的 listen fd 也可以利用 linux 的 `SO_REUSEPORT` 来直接监听。

并且通常此时新进程 `B` 是一个新部署的服务二进制文件。

方法看着不难，但是实际实现往往要考虑更多的细节，可能踩的坑也会更多。

## 具体实现

整个库的核心是 `grace/net` 即 socket 层面的实现替换；http server 部分的支持也是依赖这里。

外部用户通过 `net.Listen()` 调用就可以获得经过包装的 TCP 或者 Unix Domain Socket 对象；为了专注讨论，这里只以 TCP 为例子。

这里的使用方法和 Golang 的 net 很类似，区别在于 `grace/net` 对象支持一个 `StartProcess()` 函数。这个函数的作用就是创建承接未来流量的新进程，也是实现 graceful restart 的起点。

### 0x0 StartProcess()

```golang
func (n *Net) StartProcess() (int, error) {
	listeners, err := n.activeListeners()
	if err != nil {
		return 0, err
	}

	// Extract the fds from the listeners.
	files := make([]*os.File, len(listeners))
	for i, l := range listeners {
		files[i], err = l.(filer).File()
		if err != nil {
			return 0, err
		}
		defer files[i].Close()
	}

	// Use the original binary location. This works with symlinks such that if
	// the file it points to has been changed we will use the updated symlink.
	argv0, err := exec.LookPath(os.Args[0])
	if err != nil {
		return 0, err
	}

	// Pass on the environment and replace the old count key with the new one.
	var env []string
	for _, v := range os.Environ() {
		if !strings.HasPrefix(v, envCountKeyPrefix) {
			env = append(env, v)
		}
	}
	env = append(env, fmt.Sprintf("%s%d", envCountKeyPrefix, len(listeners)))

	allFiles := append([]*os.File{os.Stdin, os.Stdout, os.Stderr}, files...)
	process, err := os.StartProcess(argv0, os.Args, &os.ProcAttr{
		Dir:   originalWD,
		Env:   env,
		Files: allFiles,
	})
	if err != nil {
		return 0, err
	}
	return process.Pid, nil
}
```

代码逻辑其实很简单，压缩一下就是：

1. 先拿到当前的 listener 列表，因为一个服务进程可能会监听多个端口
2. 然后将每个 listener 转换得到对应的 `os.File*`；因为 Golang `os/exec` 要继承父进程的 fd 必须要在调用里显式给出 `os.File` 的序列；而 listener 作为 net 的对象显然是不认得。
  listener files 会连同 stdio 的 files 最终存到 `allFiles`，作为 `os.StartProcess()` 参数一部分
3. 因为可能有多个 listener，所以数量会通过环境变量 `LISTEN_FDS={count}` 传递给子进程
4. 通过 `os.Args[0]` 获得程序路径，用来启动子进程

可以发现有相当一部分工作量都是在倒腾 Golang 自己的数据类型以适应 runtime 的接口。后面这种感觉会特别强烈。

### 0x1 inherit()

另一个关键函数就是 `Net.inherit()`，这个函数仅在被热启动的子进程中调用上面 `net.Listen()` 时会被调用，并且利用 `sync.Once` 保证实际逻辑只会被调用一次。

这个函数同样很简单

```golang
func (n *Net) inherit() error {
	var retErr error
	n.inheritOnce.Do(func() {
		n.mutex.Lock()
		defer n.mutex.Unlock()
		countStr := os.Getenv(envCountKey)
		if countStr == "" {
			return
		}
		count, err := strconv.Atoi(countStr)
		if err != nil {
			retErr = fmt.Errorf("found invalid count value: %s=%s", envCountKey, countStr)
			return
		}

		// In tests this may be overridden.
		fdStart := n.fdStart
		if fdStart == 0 {
			// In normal operations if we are inheriting, the listeners will begin at
			// fd 3.
			fdStart = 3
		}

		for i := fdStart; i < fdStart+count; i++ {
			file := os.NewFile(uintptr(i), "listener")
			l, err := net.FileListener(file)
			if err != nil {
				file.Close()
				retErr = fmt.Errorf("error inheriting socket fd %d: %s", i, err)
				return
			}
			if err := file.Close(); err != nil {
				retErr = fmt.Errorf("error closing inherited socket fd %d: %s", i, err)
				return
			}
			n.inherited = append(n.inherited, l)
		}
	})
	return retErr
}
```

1. 首先通过环境变量拿到 listener 的数量
2. 然后从 fd=3 开始做 `fd -> *os.File -> net.Listener`，因为 Golang runtime 的需要 `net.Listener`

这段代码背后有几个假设。

首先是 `n.fdStart` 必须要是准确的第一个 listener，这个由父进程保证；并且绝大多数时候是 3，因为 stdin, stdout, stderr 已经占了 0, 1, 2。也说明不要瞎关闭标准输入输出的 fd。

其次是所有的 listener 必须是连续的。这个假设在 `StartProcess()` 中不太能一眼看得出来。不过考虑到一般服务启动都是先把 listener 给一齐启动好的，所以也算合理。

经过这么一顿操作之后 `n.inherited` 里保存的就是继承来的 listener 了，而且这几个 listener 也都是活跃的。

### 0x2 Reuse Listener

Listener 的 reuse 其实也很简单：当外部用 `net.Listen("tcp", addr)` 请求一个 listener 时，检查一下这个 `addr` 是不是已经被某个继承来的 listener 在监听了，如果是的话就直接返回这个 listener。

否则的话就创建一个新的 listener。

### 0x3 信号触发

需要 graceful restart 的时候，比如部署了服务的新二进制版本，就可以给服务进程发送一个 `SIGUSR2` 信号。

服务进程收到信号后，调用 `StartProcess()` 创建子进程；子进程启动后会继承父进程的 listener，就绪后再给父进程发送一个 `SIGTERM` 信号，通知父进程退出。

此时端口上的新请求就可以被子进程平滑接收处理，而父进程进入 graceful shutdown 流程。

这部分逻辑在 `grace/gracehttp` 中，而 HTTP 服务的优雅退出则是由 Facebook 的另外一个库 [httpdown](https://github.com/facebookarchive/httpdown) 实现。

## Faceboook/httpdown

httpdown 的核心逻辑只有一个[文件](https://github.com/facebookarchive/httpdown/blob/master/httpdown.go)

httpdown 内部为每一个连接维护一个状态。

当请求关闭时：

1. 设置 http server 禁用未来链接的 keep-alive
  即使 HTTP 默认有 keep-alive，但是大多时候也是充当短链接的用途，而这里直接禁用 keep-alive 可以让链接关闭的更快
2. 通过 `s.listener.Close()` 关闭监听，让新请求都由前面启动的新进程处理
3. 然后等待现有链接结束
  a. 一个链接结束之后会触发 `<-s.closed`，从链接列表里移除后检查是否还有其他链接；如果连接空了，那么就可以出发 `stopDone` 结束服务
  b. 但是就这么一直等到天荒地老也不对，所以同时会有个定时器，默认一分钟，超时后会进入 kill mode，就是直接强行关掉每个链接

如果你的业务是长连接，那么就需要在应用层协议设计一个 go-away 的消息，在需要优雅退出的时候告知对端此连接马上要结束了，你准备准备然后关闭这个连接并重连，或者迁移到另外一个服务节点上。

---

grace 和 httpdown 都是 facebook 里同一个人写的，也都被 archive 了，不知道是不是内部已经不用了。

这两个库的核心逻辑都很简单，但是有很多协调控制 goroutine 的逻辑倒是比较复杂。
