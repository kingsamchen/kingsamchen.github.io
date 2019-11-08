---
title: uber automaxprocs 源码分析
categories: PROGRAMMING
date: 2019-11-09 00:43:11
tags: [golang, automaxprocs]
---
最近直播内部的 golang 服务都使用了 uber 出品的 automaxprocs 这个库。

据伟大的 @ice 马总说，这个库解决了一个困扰B站整个golang（container）技术栈一年多的问题。

出于好奇这个库到底做了什么 magic，能够解决这个持续了一年多的 pain in the ass，抽了一点时间，稍微翻了一下库的源代码，记录如下。

# automaxprocs 解决了什么问题

线上容器里的服务通常都对 CPU 资源做了限制，例如默认的 4C。

但是在容器里通过 `lscpu` 仍然能看到宿主机的所有 CPU 核心：

```text
rchitecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                48
On-line CPU(s) list:   0-47
Thread(s) per core:    2
Core(s) per socket:    12
Socket(s):             2
NUMA node(s):          2
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 79
Model name:            Intel(R) Xeon(R) CPU E5-2650 v4 @ 2.20GHz
Stepping:              1
```

这就导致 golang 服务默认会拿宿主机的 CPU 核心数来调用 `runtime.GOMAXPROCS()`，导致 P 数量远远大于可用的 CPU 核心，引起频繁上下文切换，影响高负载情况下的服务性能。
<!-- more -->
automaxprocs 能够正确识别容器允许使用的核心数，合理的设置 go processor，避免这个问题。

# automaxprocs 源码分析

## Grand Tour

包级别的 `init()` 函数（代码位置在 `automaxprocs/automaxprocs.go`）实现了导入这个包即可产生作用：

```go
func init() {
	maxprocs.Set(maxprocs.Logger(log.Printf))
}
```

核心函数就是 `maxprocs.Set()`；这个函数会从当前的 cgroups 里获取设置的 CPU quota，然后转换为合适的 `GOMAXPROCS`。

核心逻辑（略去一些细节）：

```go
type config struct {
	printf        func(string, ...interface{})                      // logger
	procs         func(int) (int, iruntime.CPUQuotaStatus, error)
	minGOMAXPROCS int
}

// within func Set()

cfg := &config{
    procs:         iruntime.CPUQuotaToGOMAXPROCS,
    minGOMAXPROCS: 1,
}

maxProcs, status, err := cfg.procs(cfg.minGOMAXPROCS)

runtime.GOMAXPROCS(maxProcs)
```

可以看出主要的工作都在 `iruntime.CPUQuotaToGOMAXPROCS()` 里完成。

至于 `minGOMAXPROCS` 的作用

```text
maxProcs = max(cpu_quota, minGOMAXPROCS)
```


避免外部 cpu quota 设置的过小。

## 获取进程的 cgroup 信息

核心函数之一的 `parseCGroupSubsystems()` 可以通过解析 `/proc/$pid/cgroup` 文件，返回这个进程的 cgroup subsystem table，对应的数据结构是：

```go
// key 其实就是 subsystem
subsystems := make(map[string]*CGroupSubsys)

type CGroupSubsys struct {
	ID         int
	Subsystems []string
	Name       string
}
```

这里看一下 `/proc/$pid/cgroup` 的样子（从线上找了一个服务）：

```text
10:pids:/kubepods/burstable/pod5627f0a1-010c-11ea-90fe-d0946603da2e/f090bf4f93ec9d1f4f12b56b2ed40268c1e1c3a0bb8a92799dd99caeca80147d
9:perf_event:/kubepods/burstable/pod5627f0a1-010c-11ea-90fe-d0946603da2e/f090bf4f93ec9d1f4f12b56b2ed40268c1e1c3a0bb8a92799dd99caeca80147d
8:net_cls,net_prio:/kubepods/burstable/pod5627f0a1-010c-11ea-90fe-d0946603da2e/f090bf4f93ec9d1f4f12b56b2ed40268c1e1c3a0bb8a92799dd99caeca80147d
7:freezer:/kubepods/burstable/pod5627f0a1-010c-11ea-90fe-d0946603da2e/f090bf4f93ec9d1f4f12b56b2ed40268c1e1c3a0bb8a92799dd99caeca80147d
6:devices:/kubepods/burstable/pod5627f0a1-010c-11ea-90fe-d0946603da2e/f090bf4f93ec9d1f4f12b56b2ed40268c1e1c3a0bb8a92799dd99caeca80147d
5:memory:/kubepods/burstable/pod5627f0a1-010c-11ea-90fe-d0946603da2e/f090bf4f93ec9d1f4f12b56b2ed40268c1e1c3a0bb8a92799dd99caeca80147d
4:blkio:/kubepods/burstable/pod5627f0a1-010c-11ea-90fe-d0946603da2e/f090bf4f93ec9d1f4f12b56b2ed40268c1e1c3a0bb8a92799dd99caeca80147d
--> 3:cpu,cpuacct:/kubepods/burstable/pod5627f0a1-010c-11ea-90fe-d0946603da2e/f090bf4f93ec9d1f4f12b56b2ed40268c1e1c3a0bb8a92799dd99caeca80147d
2:cpuset:/kubepods/burstable/pod5627f0a1-010c-11ea-90fe-d0946603da2e/f090bf4f93ec9d1f4f12b56b2ed40268c1e1c3a0bb8a92799dd99caeca80147d
1:name=systemd:/kubepods/burstable/pod5627f0a1-010c-11ea-90fe-d0946603da2e/f090bf4f93ec9d1f4f12b56b2ed40268c1e1c3a0bb8a92799dd99caeca80147d
```

每行都是一条记录，记录的每个 field 之间用 `:` 分割，从左至右分别是：

- id
- subsystems，多个 subsystem 之间用 `,` 分隔
- pathname

这里的目标是包含 `cpu` 这个 subsystem 的这条记录；其他的记录其实无关紧要。

同时注意一下 `pathname` 这个字段，代表进程所属的 cgroup hierarchy 的路径，并且一个相对于 cgroup hierarchy 的 mount point 的一个相对路径。

BTW：这里能看到一条记录可能有多个 subsystem，所以前面的 table 最后会出现多个 subsystem key 指向的其实是同一个 `CGroupSubsys` 实例。

## 获取进程的 mountinfo

类似的，核心函数 `parseMountInfo()` 会打开进程的 `mountinfo` 文件，然后将每一行记录解析成对应的 `MountInfo` 结构

```go
type MountPoint struct {
	MountID        int
	ParentID       int
	DeviceID       string
	Root           string
	MountPoint     string
	Options        []string
	OptionalFields []string
	FSType         string
	MountSource    string
	SuperOptions   []string
}
```

看一下一个示例 `mountinfo` 文件内容

```text
root@data-guru-129126-b9bf4d97-rrd65:/# cat /proc/24/mountinfo | grep cgroup
1879 1878 0:147 / /sys/fs/cgroup ro,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,mode=755
1880 1879 0:23 /kubepods/burstable/pod7462dcc7-010a-11ea-90fe-d0946603da2e/85ca96fbb5ee9855ce56da4731bf346786f62b6199a58f66edfb0b7e96b73854 /sys/fs/cgroup/systemd ro,nosuid,nodev,noexec,relatime master:10 - cgroup cgroup rw,xattr,release_agent=/lib/systemd/systemd-cgroups-agent,name=systemd
1881 1879 0:25 /kubepods/burstable/pod7462dcc7-010a-11ea-90fe-d0946603da2e/85ca96fbb5ee9855ce56da4731bf346786f62b6199a58f66edfb0b7e96b73854 /sys/fs/cgroup/cpuset ro,nosuid,nodev,noexec,relatime master:13 - cgroup cgroup rw,cpuset
--> 1882 1879 0:26 /kubepods/burstable/pod7462dcc7-010a-11ea-90fe-d0946603da2e/85ca96fbb5ee9855ce56da4731bf346786f62b6199a58f66edfb0b7e96b73854 /sys/fs/cgroup/cpu,cpuacct ro,nosuid,nodev,noexec,relatime master:14 - cgroup cgroup rw,cpu,cpuacct
1884 1879 0:27 /kubepods/burstable/pod7462dcc7-010a-11ea-90fe-d0946603da2e/85ca96fbb5ee9855ce56da4731bf346786f62b6199a58f66edfb0b7e96b73854 /sys/fs/cgroup/blkio ro,nosuid,nodev,noexec,relatime master:15 - cgroup cgroup rw,blkio
1885 1879 0:28 /kubepods/burstable/pod7462dcc7-010a-11ea-90fe-d0946603da2e/85ca96fbb5ee9855ce56da4731bf346786f62b6199a58f66edfb0b7e96b73854 /sys/fs/cgroup/memory ro,nosuid,nodev,noexec,relatime master:16 - cgroup cgroup rw,memory
1886 1879 0:29 /kubepods/burstable/pod7462dcc7-010a-11ea-90fe-d0946603da2e/85ca96fbb5ee9855ce56da4731bf346786f62b6199a58f66edfb0b7e96b73854 /sys/fs/cgroup/devices ro,nosuid,nodev,noexec,relatime master:17 - cgroup cgroup rw,devices
1887 1879 0:30 /kubepods/burstable/pod7462dcc7-010a-11ea-90fe-d0946603da2e/85ca96fbb5ee9855ce56da4731bf346786f62b6199a58f66edfb0b7e96b73854 /sys/fs/cgroup/freezer ro,nosuid,nodev,noexec,relatime master:18 - cgroup cgroup rw,freezer
1888 1879 0:31 /kubepods/burstable/pod7462dcc7-010a-11ea-90fe-d0946603da2e/85ca96fbb5ee9855ce56da4731bf346786f62b6199a58f66edfb0b7e96b73854 /sys/fs/cgroup/net_cls,net_prio ro,nosuid,nodev,noexec,relatime master:19 - cgroup cgroup rw,net_cls,net_prio
1889 1879 0:32 /kubepods/burstable/pod7462dcc7-010a-11ea-90fe-d0946603da2e/85ca96fbb5ee9855ce56da4731bf346786f62b6199a58f66edfb0b7e96b73854 /sys/fs/cgroup/perf_event ro,nosuid,nodev,noexec,relatime master:20 - cgroup cgroup rw,perf_event
1890 1879 0:33 /kubepods/burstable/pod7462dcc7-010a-11ea-90fe-d0946603da2e/85ca96fbb5ee9855ce56da4731bf346786f62b6199a58f66edfb0b7e96b73854 /sys/fs/cgroup/pids ro,nosuid,nodev,noexec,relatime master:21 - cgroup cgroup rw,pids
```

每条记录的字段用空格分隔，字段 `-` 表示后面都是 options

共有三个字段需要我们关心：

- 索引为3的字段；组成当前挂载点根路径的文件系统的路径，对应 `MountInfo.Root`
- 索引为4的字段；当前挂载点相对于进程根目录的路径，对应 `MountInfo.MountPoint`
- `-` 字段之后的第一个字段，代表 filesystem type，对应 `MountInfo.FsType`；我们其实只需要 `cgroup`。
- 上面 fstype 字段之后的第二个字段，是 subsystems，subsystem之间用,分割；这里我们其实需要的是包含 `cpu` 的这个 subsystem

## 找到目标 cgroup path

有了前两步之后，就可以找到进程对应的 `cpu` 这个 subsystem 的 CGroup path。

```go
// CGroup represents the data structure for a Linux control group.
type CGroup struct {
	path string
}
```

这部分操作在 lambda 函数 `newMountPoint()` 中。

总结起来就是：

1. 在 mountinfo 文件中找到 `fstype = cgroup && subsystems.contains(cpu)` 的记录，分离出 `root` 和 `mount-point`。
2. 在 cgroup 文件中找到 `subsystems.contains(cpu)` 的记录，分理出 pathname
3. `cgroupPath = Join(mount_point, relative(root, pathname))`
`relative()` 函数返回 pathname 相对于 root 的相对路径

BTW：实践中发现 `root` 和 `pathname` 基本一致，这样返回的相对路径就是 `.`；最后组合的最终路径都是 `/sys/fs/cgroup/cpu`

不过考虑到不同发行版甚至不同版本的 docker / k8s 行为可能存在不一致，所以最具有移植性还是上面的做法。

## 计算 cpu 配额

有了前面的目录路径之后，该目录下的：

- `cfs.cpu_period_us` 文件记录了调度周期，单位是 us；默认值一般是 100'000，即 100 ms
- `cfs.cpu_quota_us` 记录了每个调度周期进程允许使用 cpu 的量，单位也是 us。
值为 -1 表示无限制；对于 4C 的容器，这个值一般是 400'000

> Aside：这两个值限制的是进程使用 cpu 的时间。
上述设置下表示：每 100ms 的调度周期内，该进程可以使用 400ms 的 cpu 时间，所以看起来的效果是可以使用4个CPU核心
更详细的内容请参考 Linux kernel 的文档：[CFS Bandwidth Control](https://www.kernel.org/doc/Documentation/scheduler/sched-bwc.txt)

quota 和 period 的比值就是 docker 为容器设置的 CPU 核数配置。

这个值也是 automaxprocs 为 `runtime.GOMAXPROCS()` 设置的值。

这部分逻辑对应库函数：`CGoups.CPUQuota()`

# 总结

容器技术（docker）通过 Linux kernel 提供的 cgroups 机制来实现资源隔离和限制，但是这种限制有时候会出现反直觉的结果。

上面的分析过程看，虽然这个库做的事情比较简单，但是要注意的是，我们是通过逆向工程（由果推因）来分析的这个问题。

如果需要从正面解决（执因索果），那么就需要对 1）容器实现细节 2）linux 内核中 cgroups 的实现细节 有很深的了解。

这恐怕也是过了一年多才找到解决方案，而且最后还是直接使用别人的solution的原因。

# 彩蛋

## 0x0

事实上，automaxprocs 仅针对于使用 CFS 调度策略的实例。

CFS 调度测类只限制进程的运行配额，不设置 processor affinity。所以在 4C 的限制下，理论上 G-P-M 调度模型下的 M 可以运行在任意物理核心上

查看 `/sys/fs/cgroup/cpuset/cpuset.cpus` 这个文件可以发现没有做任何物理核心上的限制。

docker 创建容器时可以使用 `--cpus=x` 来实现。

## 0x1

对于**只**使用 cpuset 策略的容器来说，其实没必要使用这个库。

因为 cpuset 直接设置了容器的 processor affinity，然后神奇的是，golang 的 `runtime.NumCores()` 获取的核心数是考虑过 processor affinity 的。

```go
// @ /usr/local/go/src/runtime/os_linux.go
func getproccount() int32 {
	// This buffer is huge (8 kB) but we are on the system stack
	// and there should be plenty of space (64 kB).
	// Also this is a leaf, so we're not holding up the memory for long.
	// See golang.org/issue/11823.
	// The suggested behavior here is to keep trying with ever-larger
	// buffers, but we don't have a dynamic memory allocator at the
	// moment, so that's a bit tricky and seems like overkill.
	const maxCPUs = 64 * 1024
	var buf [maxCPUs / 8]byte
	r := sched_getaffinity(0, unsafe.Sizeof(buf), &buf[0])
	if r < 0 {
		return 1
	}
	n := int32(0)
	for _, v := range buf[:r] {
		for v != 0 {
			n += int32(v & 1)
			v >>= 1
		}
	}
	if n == 0 {
		n = 1
	}
	return n
}
```

`sched_getaffinity()` 其实是一个 [linux syscall](http://man7.org/linux/man-pages/man2/sched_setaffinity.2.html)

注：虽然 `runtime.NumCores()` 是根据亲缘性获取的，但是这个值第一次初始化之后与就不再变化了，即使后面 processor affinity 在运行过程中被更改了。。。

所以有人提了一个相关的 issue：[runtime: NumCPU does not change when process affinity changes](https://github.com/golang/go/issues/11609)
