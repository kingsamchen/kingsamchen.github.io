---
title: Redigo 源码学习：连接池的设计
categories: PROGRAMMING
date: 2020-08-21 00:24:17
tags: [golang, redigo, pool]
toc: true
mathjax: true
---
## 主要数据结构

暴露给外部用户的 `Pool` 对象

```go
type Pool struct {
	...

	// Maximum number of idle connections in the pool.
	MaxIdle int

	// Maximum number of connections allocated by the pool at a given time.
	// When zero, there is no limit on the number of connections in the pool.
	MaxActive int

	// Close connections after remaining idle for this duration. If the value
	// is zero, then idle connections are not closed. Applications should set
	// the timeout to a value less than the server's timeout.
	IdleTimeout time.Duration

	mu           sync.Mutex    // mu protects the following fields
	active       int           // the number of open connections in the pool
	idle         idleList      // idle connections
}
```

内部维护空闲/待复用的列表数据结构 `idleList`
<!-- more -->
数据结构层面的本质上是一个双向链表

```go
type idleList struct {
	count       int
	front, back *poolConn
}
```

表示某个属于连接池的连接对象 `poolConn`

数据结构上充当一个双向链表的节点。

```go
type poolConn struct {
	c          Conn
	t          time.Time
	created    time.Time
	next, prev *poolConn
}
```

实现外部连接接口 `Conn` 的池化连接封装 `activeConn`

这个连接对象是外部用户拿到的池化连接对象。

```go
type activeConn struct {
	p     *Pool
	pc    *poolConn
	state int
}
```

![](/img/redigo-pool.png)

## Active and Idle 连接

一个 active connection 代表拥有到 Redis Server 的网络通信连接的连接对象。

一个 idle connection 代表拥有到 Redis Server 的网络通信连接，并且等待被应用侧复用的连接对象。

因此，一个 idle connection 本质上也是一个 active connection，因为底层维护了一个到 Redis Server 的网络通信连接。

这也解释了配置上要求必须满足 $max\_idle \leqslant max\_active$，因为 idle connections 实际上是 active connections 的子集。

## 连接管理

一个最常见的使用 Redigo 的例子是：

```go
conn := pool.GetContext(ctx)
defer conn.Close()

// Use with conn
conn.Do(...)
```

`pool.GetContext()` 可以从连接池中请求一个连接；而 `conn.Close()` 则在使用完毕后将连接归还给连接池，支持复用。

### 0x0. 请求分配连接

因为 `pool.Get()` 内部也是调用的 `pool.GetContext()` 所以这里只需要看后者的实现即可。

Redigo 允许配置当前 active connection 达到 MaxActive 时阻塞等待直到有空闲连接；但是这个特点和连接池管理无关，因此下面分析中会跳过

大体流程如下：

1. 如果设置了 `pool.IdleTimeout` 即，空闲连接最大超时时间，则会先清理 `idleList` 中已经超时的空闲连接
这里的清理是从 `idleList` 的尾部开始清理
2. 尝试从 `idleList` 中复用一个连接；复用是先从列表的头部开始检查
如果某个空闲连接不能够被复用，则关闭连接，继续检查下一个
3. 如果 `idleList` 中没有可复用的连接，则说明可能需要新建一个 Active 连接来满足请求；所以这里会继续检查当前 active 连接数是否达到了配置的上限
4. 创建一个底层的 active 连接

注：活动连接内部是以 `activeConn` 结构表示，同时这个结构实现了 `Conn` 接口

---

对应流程 (1) 的实现：

```go
// Prune stale connections at the back of the idle list.
if p.IdleTimeout > 0 {
	n := p.idle.count
	for i := 0; i < n && p.idle.back != nil && p.idle.back.t.Add(p.IdleTimeout).Before(nowFunc()); i++ {
		pc := p.idle.back
		p.idle.popBack()
		p.mu.Unlock()
		pc.c.Close()
		p.mu.Lock()
		p.active--
	}
}
```

这里要牢记 `p.idle` 对应一个链表；并且 `idle.popBack()` 仅仅移除链表节点，对应的连接关闭 `pc.c.Close()` 还是需要手动完成的。

另外这里 `p.active--` 也可以看出一个 idle-conn 其实也是一个 active-conn。

对应 (2) 的实现：

```go
// Get idle connection from the front of idle list.
for p.idle.front != nil {
	pc := p.idle.front
	p.idle.popFront()
	p.mu.Unlock()
	if (p.TestOnBorrow == nil || p.TestOnBorrow(pc.c, pc.t) == nil) &&
		(p.MaxConnLifetime == 0 || nowFunc().Sub(pc.created) < p.MaxConnLifetime) {
		return &activeConn{p: p, pc: pc}, nil
	}
	pc.c.Close()
	p.mu.Lock()
	p.active--
}
```

如果连接能够被复用，则底层状态不变，active 计数也不变，直接返回一个可用连接即可。

这里的 `TestOnBorrow()` 是允许外部提供一个函数在复用一个 idle-conn 前检查这个 conn 是否可用；函数里可以直接使用简单的 ping/poing 检测。

(3)(4) 两步实际上是合在一起的：

```go
// Check for pool closed before dialing a new connection.
if p.closed {
	p.mu.Unlock()
	err := errors.New("redigo: get on closed pool")
	return errorConn{err}, err
}

// Handle limit for p.Wait == false.
if !p.Wait && p.MaxActive > 0 && p.active >= p.MaxActive {
	p.mu.Unlock()
	return errorConn{ErrPoolExhausted}, ErrPoolExhausted
}

p.active++
p.mu.Unlock()
c, err := p.dial(ctx)
if err != nil {
	c = nil
	p.mu.Lock()
	p.active--
	if p.ch != nil && !p.closed {
		p.ch <- struct{}{}
	}
	p.mu.Unlock()
	return errorConn{err}, err
}
return &activeConn{p: p, pc: &poolConn{c: c, created: nowFunc()}}, nil
```

创建新连接前会首先检查当前连接池是否已被关闭。

配置了非等待并且当前活跃连接已达到配置的最大活跃数 `p.MaxActive` 会返回连接池耗尽的错误。

如果一切正常则会用 `p.dial()` 创建一个新的连接；如果这里创建失败，则需要走回退逻辑。

### 0x1. 回收连接

回收连接需要调用 `activeConn.Close()`，这个函数的涉及到连接池管理的只有一句

```go
func (ac *activeConn) Close() error {
	...	// omitted

	ac.p.put(pc, ac.state != 0 || pc.c.Err() != nil)

	return nil
}
```

实际上连接池的回收还是要依赖 `pool.put()` 这个函数，并且 `pc` 作为参数。

这也是为什么 `activeConn` 实例要保存 `p` 和 `pc`。

```go
func (p *Pool) put(pc *poolConn, forceClose bool) error {
	p.mu.Lock()
	if !p.closed && !forceClose {
		pc.t = nowFunc()
		p.idle.pushFront(pc)
		if p.idle.count > p.MaxIdle {
			pc = p.idle.back
			p.idle.popBack()
		} else {
			pc = nil
		}
	}

	if pc != nil {
		p.mu.Unlock()
		pc.c.Close()
		p.mu.Lock()
		p.active--
	}

	...

	p.mu.Unlock()
	return nil
}
```

连接能被复用的一个前提是没有出现硬性错误，即 `forceClose == false`，对应 `ac.state == 0 && pc.c.Err() == nil`。

等待被复用的的连接对象 `poolConn` 会被插入到 `idleList` 的头部；这意味着越靠近头部的连接回收的时间越近。

复用连接的时候也是从 `idleList` 的头部开始，说明设计上是优先考虑最近放入池子的连接。

放入连接后如果 `idle.count > MaxIdle`，即达到配置的最大空闲连接上限，则会从链表的尾部移除一个连接节点；即，空闲连接中优先淘汰旧/老节点。

连接回收如果不涉及超过上限的节点处理，`p.active` 数量保持不变。

## 设计原则

1. 底层连接的关闭属于耗时操作，调用 `poolConn.Conn.Close()` 时尽量避免处在加锁状态；同时涉及底层数据结构的操作时将元素生命周期和连接的关闭分离开
2. 只有保存待复用连接的 idle-list，没有维护正在被使用连接的 active-list
3. idle-list 是一个双端（链）表，越靠近头的连接越“新鲜”；复用连接时选择最近被回收的
4. idle-list 清理过时连接或者达到上限需要 prune 时从尾部开始，优先处理最旧的节点