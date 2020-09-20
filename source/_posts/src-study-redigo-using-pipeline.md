---
title: Redigo 源码学习：Pipeline
categories: PROGRAMMING
date: 2020-09-20 21:02:04
tags: [golang, redigo, redis, pipeline]
toc: true
---
## 如何使用 pipeline

先来回顾一下如何在 redigo 中使用 pipeline

```go
c := pool.Get()
defer c.Close()

c.Send("SET", "foo", "bar")
c.Send("GET", "foo")
c.Flush()
c.Receive() // reply from SET
v, err = c.Receive() // reply from GET
```

核心是 Send — Flush — Receive 三个步骤。
<!-- more -->
调用 `Receive()` 的次数必须对应使用 `Send()` 发送命令的次数

## 如何支持 pipeline

从连接池中获取的 `activeConn` 对象实现的 `Send()` `Flush()` 和 `Receive()` 都会将调用转到实际的实现者 `ac.pc.c` 即真实的底层连接对象上。

所以和 pipelining 有关的功能性逻辑只需要关注 `Conn` 的实现函数即可。

### 0x0. Send

```go
func (c *conn) Send(cmd string, args ...interface{}) error {
	c.mu.Lock()
	c.pending += 1		// <- (1)
	c.mu.Unlock()
	if c.writeTimeout != 0 {
		c.conn.SetWriteDeadline(time.Now().Add(c.writeTimeout))
	}
	if err := c.writeCommand(cmd, args); err != nil {		// <- (2)
		return c.fatal(err)
	}
	return nil
}
```

整个 `Send()` 的逻辑非常直接：

1. 首先自增 `c.pending` 计数（先留意这个计数，具体作用后面讲）
2. 如果连接配置了写超时那么设置写超时
3. 使用 `writeCommand()` 往连接里写命令

函数 `writeCommand()` 的作用是将输入的 Redis 请求转换为符合 RESP 协议格式的数据流并写入 socket；这个函数在不使用 pipelining 时也会调用，这里不展开分析。

### 0x1. Flush

```go
// conn is the low-level implementation of Conn
type conn struct {
	...

	// Read
	readTimeout time.Duration
	br          *bufio.Reader

	// Write
	writeTimeout time.Duration
	bw           *bufio.Writer

	...
}

func (c *conn) Flush() error {
	if c.writeTimeout != 0 {
		c.conn.SetWriteDeadline(time.Now().Add(c.writeTimeout))
	}
	if err := c.bw.Flush(); err != nil {    // <- here
		return c.fatal(err)
	}
	return nil
}
```

`Flush()` 的核心操作比 `Send()` 更简单：调用底层充当写缓冲的 `bufio.Writer` 的 `Flush()` 强制刷新缓冲区。

### 0x2. Receive

`conn.Receive()` 内部会调用 `conn.ReceiveWithTimeout()` 并传入配置的读超时时间，所以我们只需要看后者的实现。

注：这里可以发现 Send — Receive 两边的实现时不对称的；Receive 时可以手动指定读超时，而 Send 只能只用设置的写超时。

```go
func (c *conn) ReceiveWithTimeout(timeout time.Duration) (reply interface{}, err error) {
	...

	if reply, err = c.readReply(); err != nil {
		return nil, c.fatal(err)
	}

	// When using pub/sub, the number of receives can be greater than the
	// number of sends. To enable normal use of the connection after
	// unsubscribing from all channels, we do not decrement pending to a
	// negative value.
	//
	// The pending field is decremented after the reply is read to handle the
	// case where Receive is called before Send.
	c.mu.Lock()
	if c.pending > 0 {
		c.pending -= 1
	}
	c.mu.Unlock()

	if err, ok := reply.(Error); ok {
		return nil, err
	}

	return
}
```

1. 调用 `c.readReply()` 尝试从底层连接中读取出一个 reply。
和前面的 `c.writeCommand()` 类似，readReply() 是一个通用函数；不管是否处于 pipelining，它都负责从底层连接读取一个 Redis Reply。
2. `c.pending` 计数自减

这里其实可以很明显看出 `c.pending` 代表的就是 in flight 的请求个数。

在不使用 pub/sub 的情况下，有多少个 Request 就有多少个 Reply。

## 为何 pipeline 更多时候更高效

通过上面的源码分析我们已经知道，当启用 pipeline 时，可以不用等待获取上一个 Request 的 Reply 我们就可以继续发送后续的 Request，直到把所有的 Request 都发送完毕。

这里的发送实际上是将命令写入应用层缓冲区（不管这个缓冲区是否被 runtime 隐藏，实际上都有这么一个东西）

这样在大多数时候，底层 runtime 通过一次 `write(2)` 系统调用就可以将缓冲区中的一批数据写入 socket 的发送缓冲区，并传输到对端；这一批数据可能包含多个 Request 数据包。

因此不光节省了系统调用次数，也减少了发送这些 Request 数据包需要的网络传输次数。

对于读端也类似。

这才有我们常说的使用 pipeline 可以减少 RTT 的说法。

## 出错中止 pipelining 的处理

```go
// NewConn returns a new Redigo connection for the given net connection.
func NewConn(netConn net.Conn, readTimeout, writeTimeout time.Duration) Conn {
	return &conn{
		conn:         netConn,
		bw:           bufio.NewWriter(netConn),
		br:           bufio.NewReader(netConn),
		readTimeout:  readTimeout,
		writeTimeout: writeTimeout,
	}
}
```

`conn` 是 TCP socket，Redigo 使用 bufio 作为它的应用层缓冲区。

根据前面的分析我们知道，pipeline 的 `Send()` 实际上是将命令写入 `br`，这可以通过 `conn.writeBytes()` 确认

```go
func (c *conn) writeBytes(p []byte) error {
	c.writeLen('$', len(p))
	c.bw.Write(p)
	_, err := c.bw.WriteString("\r\n")
	return err
}
```

这就意味着即使还没调用 `Flush()` 也有一部分 Request 数据包已经被底层连接发送出去；并且我们可以假设 Redis Server 也收到了这部分数据，甚至可能已经发送了对应的 Reply。

那么假设在这种情况下，后续有个 `Send()` 失败，整体操作无法进行，需要向上汇报错误。

而因为 `defer` 的存在，我们的连接会回归连接池。

这里就涉及到一个问题：如何终止这种底层已经完成了一部份通信的连接？

这部分逻辑在 `activeConn.Close()` 中实现：

核心只有一句，即调用底层的 `conn.Do("")`。

这里的空命令看起来是会触发 `Do()` 的某个特殊逻辑或者提前退出，我们看一下代码验证一下。

### 0x0. 暗藏玄机的 conn.Do

和 `activeConn` 类似，`conn.Do()` 内部会调用 `conn.DoWithTimeout()`，所以摘录一下这个函数的实现，尽可能略去无关的内容：

```go
func (c *conn) DoWithTimeout(readTimeout time.Duration, cmd string, args ...interface{}) (interface{}, error) {
	c.mu.Lock()
	pending := c.pending		// <- (a)
	c.pending = 0
	c.mu.Unlock()

	if cmd == "" && pending == 0 {	// <- (b)
		return nil, nil
	}

	...

	if cmd != "" {
		if err := c.writeCommand(cmd, args); err != nil {
			return nil, c.fatal(err)
		}
	}

	if err := c.bw.Flush(); err != nil {	//	<- (c)
		return nil, c.fatal(err)
	}

	...

	if cmd == "" {
		reply := make([]interface{}, pending)
		for i := range reply {
			r, e := c.readReply()			// <- (d)
			if e != nil {
				return nil, c.fatal(e)
			}
			reply[i] = r
		}
		return reply, nil
	}

	...
}
```

前面提到 `c.pending` 代表 in-flight 的请求个数，所以这里

1. 拿到还没有被 ACK 的请求数量
2. 利用 `bw.Flush()` 确保底下发送缓冲区都数据都已经发出去了
3. 把每个 in-flight 请求的 reply 获取到
4. 返回结果，或者返回错误；但是这个时候最外层并不关心这个错误。

这样一来那些之前进行到一半的请求都被处理掉了，连接也可以被放回连接池。
