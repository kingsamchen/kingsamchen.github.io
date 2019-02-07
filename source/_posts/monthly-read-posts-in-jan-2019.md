---
title: Monthly Read Posts in Jan 2019
categories: PROGRAMMING
date: 2019-02-06 19:32:23
tags: [c++, performance, spinlock, allocator, linux, ptrace]
---
## Programming Languages

[Fun with(out) keyword explicit](https://arne-mertz.de/2015/03/fun-without-keyword-explicit/)

The old saying goes: _When writing a conversion operator or a constructor that can be called with one argument, make it explicit by default._

By the way, according to comments from the author, the buggy compiler he used was C++ Builder, from Embarcadero, sigh.

## Engineering

[Simple and Clean Code vs. Performance](https://arne-mertz.de/2015/03/simple-and-clean-code-vs-performance/)

- Performance is not efficiency. // ?? I still can't get this clear in a programming context.
- Improve performance after having profiled your system.
- Make sure use of correct data structures and algorithms
- By default write code for maintainability.
- Know and use your libraries, unless your profiler showed them to have bad performance.

## Concurrency

[Reinventing spinlocks](https://idea.popcount.org/2012-09-12-reinventing-spinlocks/)

when implementing spinlock unlock: a simple atomic store is enough. no need for using  a heavy CAS operation.

## Misc

[Testing Memory Allocators: ptmalloc2 vs tcmalloc vs hoard vs jemalloc While Trying to Simulate Real-World Loads](http://ithare.com/testing-memory-allocators-ptmalloc2-tcmalloc-hoard-jemalloc-while-trying-to-simulate-real-world-loads/)

Three pre-requisites:
- test only new/delete (or malloc/free)
- use realistic statistic distributions; both for allocated sizes and for lifetime of allocated items
- access all memory at least once

all for emulating real-world uses.

conclusion:
- for cpu cycle test, every allocator is not too far from each other. each of them can outperform another one under certain conditions. benchmark is highly recommended when you decide to change your allocator.
- some allocators behave not so good in memory overhead, like Hoard.

---

[lsof: can't identify protocol](https://idea.popcount.org/2012-12-09-lsof-cant-identify-protocol/)

- `lsof` for listing open files; while
- `netstat` for displaying network related information, like connections .etc

`lsof` has issues on locating half-closed sockets.

---

[Linux process states ptrace tutorial part #1](https://idea.popcount.org/2012-12-11-linux-process-states/)

How `ptrace` relates with linux process states.

using `SIGSTOP` and `SIGCONT` to control a process's state and parent process receives `SIGCHLD` when child process state changes.
