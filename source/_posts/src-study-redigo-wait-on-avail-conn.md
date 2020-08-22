---
title: Redigo 源码学习：阻塞等待连接可用
categories: PROGRAMMING
date: 2020-08-22 11:24:10
tags: [golang, redigo]
---
通过[连接池管理](https://www.notion.so/400e99c27cf445d0ba9388b81dc8cc76) 了解如何回收连接到连接池以及从连接池复用连接后，可以回过头来研究一下 Redigo 支持的阻塞等待可用连接的设计与实现。

通过设置 `Pool.Wait == true` 之后如果当前连接池满了， `Pool.Get()` 不会返回连接池耗尽错误，而是阻塞在调用上，直到超时或者存在可用连接才会返回。

这个属于经典的 resource counting 问题，并且最大的 resource count 由 `Pool.MaxActive` 决定。

## 获取连接

现在看一下 `Pool.GetContext()` 如何处理这种情况。

```go
func (p *Pool) GetContext(ctx context.Context) (Conn, error) {
	// Wait until there is a vacant connection in the pool.
	waited, err := p.waitVacantConn(ctx)    // <-- wait at here
	if err != nil {
		return errorConn{err}, err
	}

	p.mu.Lock()

	if waited > 0 {
		p.waitCount++
		p.waitDuration += waited
	}

	...

	p.active++
	p.mu.Unlock()

	...
}
```

每次获取连接都会调用 `Pool.waitVacantConn()` 确保：

- 如果当前获取一个连接后仍未超过配置上限则理解从函数返回，执行获取连接逻辑
- 阻塞等待直到有可用连接出现，或者等到超时

即，只要 `waitVacantCount()` 函数正常返回则代表当前一定能获取一个可用连接。

```go
// waitVacantConn waits for a vacant connection in pool if waiting
// is enabled and pool size is limited, otherwise returns instantly.
// If ctx expires before that, an error is returned.
//
// If there were no vacant connection in the pool right away it returns the time spent waiting
// for that connection to appear in the pool.
func (p *Pool) waitVacantConn(ctx context.Context) (waited time.Duration, err error) {
	if !p.Wait || p.MaxActive <= 0 {
		// No wait or no connection limit.
		return 0, nil
	}

	p.lazyInit()

	...

	if ctx == nil {
		<-p.ch
	} else {
		select {
		case <-p.ch:
			// Additionally check that context hasn't expired while we were waiting,
			// because `select` picks a random `case` if several of them are "ready".
			select {
			case <-ctx.Done():
				return 0, ctx.Err()
			default:
			}
		case <-ctx.Done():
			return 0, ctx.Err()
		}
	}

	...

	return 0, nil
}
```

整个函数的核心就是从 `p.ch` 这个 channel 中获取一个类型为 `struct{}` 的 token 对象;

显然这里使用一个 *bounded channel* 作为限流器。

猜想基于这个 channel 的整套限流机制应该是：

- channel 里 token 对象的个数代表还有多少连接可以分配
- 消耗连接 quota 对应地直接从 channel 中拿出 token 对象；回收连接则往 channel 再寸一个 token 对象

接下来我们考虑一下 channel 的初始化：因为 `p.ch` 只能被初始化一遍，并且要考虑到这个初始化必须满足 goroutine-safe。

这个 channel 的初始化流程则是在 `p.lazyInit()` 中完成的：

```go
func (p *Pool) lazyInit() {
	// Fast path.
	if atomic.LoadUint32(&p.chInitialized) == 1 {
		return
	}
	// Slow path.
	p.mu.Lock()
	if p.chInitialized == 0 {
		p.ch = make(chan struct{}, p.MaxActive)
		if p.closed {
			close(p.ch)
		} else {
			for i := 0; i < p.MaxActive; i++ {
				p.ch <- struct{}{}
			}
		}
		atomic.StoreUint32(&p.chInitialized, 1)
	}
	p.mu.Unlock()
}
```

`p.chInitialized` 是一个 `uint32` 类型的 flag。

这里很明显可以看出这是一个典型的 _double-checked locking pattern_，常用于 singleton 模式。

这里是因为希望这个 channel 的初始化是 lazy 的。

可以看到这个 channel 的 buffer-size 是 `p.MaxActive` 即配置的连接池大小；并且创建 buffer 后会直接往 channel 中塞满 `p.MaxActive` 个 token 对象。

前面分析 `Pool.GetCountext()` 代码时我们忽略了一种case：拿到 token 后通过 `dial()` 创建一个底层连接失败；这个时候我们需要往 channel 中补偿一个 token 对象。

```go
c, err := p.dial(ctx)
if err != nil {
	c = nil
	p.mu.Lock()
	p.active--
	if p.ch != nil && !p.closed {
		p.ch <- struct{}{}    // <-- 补偿
	}
	p.mu.Unlock()
	return errorConn{err}, err
}
```

## 回收连接

回收连接部份的逻辑仍然是在 `Pool.put()` 中：

```go
func (p *Pool) put(pc *poolConn, forceClose bool) error {
	p.mu.Lock()
	...
	if pc != nil {
		p.mu.Unlock()
		pc.c.Close()
		p.mu.Lock()
		p.active--
	}

	if p.ch != nil && !p.closed {
		p.ch <- struct{}{}
	}
	p.mu.Unlock()
	return nil
}
```

回收连接中对应的逻辑比较简单：只要 `idleList` 中某个连接被清理了，就往 channel 中塞一个 token 对象进行补偿。

## 总结

Redigo 使用 bounded channel 实现了 resource counting 机制；并且满足

$$ p.active + \text{len}(p.ch)=p.MaxActive $$

对于其他语言来说，可以使用 condition_variable 甚至 Semaphore 来实现 resource counting。
