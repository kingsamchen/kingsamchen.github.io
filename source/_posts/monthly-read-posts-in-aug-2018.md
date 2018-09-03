---
title: Monthly Read Posts in Aug 2018
categories: PROGRAMMING
date: 2018-09-03 13:14:04
tags: [raft, membership protocol, tcp flow control, GIL, variadic template, parallelism, lock-free]
---
## Programming Languages

[CppCon 2015: Fedor Pikus “The Unexceptional Exceptions"](https://www.youtube.com/watch?v=fOV7I-nmVXw)

Handling exceptions is just as to handle errors using error code, exception is just a tool.

The key point is to maintain the program state in a well defiend state.

Turn error handling into resource management and use RAII to automate it; use explicit try...catch only when absolute necessary.

Bonus tip: avoid uses of `pthread_cancel`.

---

[Pros and cons of functional programming](https://itnext.io/pros-and-cons-of-functional-programming-32cdf527e1c2)

Pure function: avoid shared state; immutable structures; function composition.

Declarative: instead of answers the question 'how to do' in imperative style, it answers the question 'what to do'; imperative relies on instructions while declarative relies more on expressions.

Cons of FP: not suitable for graph algorithms; major shift on mind patterns.

---

[Understanding Shell Script's idiom: 2>&1](https://www.brianstorti.com/understanding-shell-script-idiom-redirect/)

General usage:

```shell
cat foo.txt > result.log 2>&1
```

1. fd for stdout and stderr are 1 and 2 respectively
2. `a > b` is a shortcut for `a 1> b` and 1 here is fd value for stdout
3. you can use `&fd` to reference a fd value
4. therefore, using `2>&1` would redirect stderr to stdout, and `1>&2` would do the opposite.

<!-- more -->

However, I still have doubt about why place `2>&1` at the end ? that doesn't make consistency.

---

[Pointers to arrays in C](https://eli.thegreenplace.net/2010/01/11/pointers-to-arrays-in-c)

**Core observation**: While an array name may decay into a pointer, the address of the array does not decay into a pointer to a pointer.

---

[CppCon 2015: Fedor Pikus PART 1 “Live Lock-Free or Deadlock (Practical Lock-free Programming)"](https://www.youtube.com/watch?v=lVBvHbJsg5Y)

[CppCon 2015: Fedor Pikus PART 2 “Live Lock-Free or Deadlock (Practical Lock-free Programming) ”](https://www.youtube.com/watch?v=1obZeHnAwz4)

A lengthy and complex talk which covers multiple complicated topics.

What memory barriers (acquire-release, mainly, do and how it helps to fix tradition DCLP.

Generic lock-free queue is very hard to write and not always faster than a well-written non-thread-safe queue with a spin-lock; and a specialized lock-free queue is often faster.

Thread-safe data structures require special interfaces.

---

[Parallel STL for Newbies: Reduce and Independent Modifications](http://ithare.com/parallel-programming-for-parallel-noobs-reduce-and-independent-modifications/)

Some caveats of using `std::reduce()` explained:
1. be careful when using `std::reduce()` on floating pointers
2. be careful using with non-commutative data types
3. avoid multiple passes (performance penalty)

---

CppCon 2015: John Lakos “Value Semantics: It ain't about the syntax! [1](https://www.youtube.com/watch?v=W3xI1HJUy7Q) [2](https://www.youtube.com/watch?v=0EvSxHxFknM)

Explaining value semantics on a rather high level of perspective.

The author uses the concept _salient attributes_ to model values of a type.

Definition of salient attributes: The documented set of (observable) named attributes of a type T that must respectively “have” (refer to) the same value in order for two instances of T to “have” (refer to) the same value.

The topic of this talk is great, but the talk itself is rather verbose; too  many concret examples are described while so many of which are not important that being worthy.

---

[Strongly typed constructors](https://www.fluentcpp.com/2016/12/05/named-constructors/)

The key point of the post is: how to construct an object from several data if these data are in the same type?

that is, if we have a class `Circle` defined as such:

```cpp
class Circle
{
public:
    explicit Circle(double radius) : radius_(radius) {}
    explicit Circle(double diameter) : radius_(diameter / 2) {} // This doesn't compile !!
};
```

Actually I prefer using two dedicated static generation functions:

```cpp
class Circle {
public:
    static Circle FromRadius(double r);
    static Circle FromDiameter(double d);

};
```

Because define types for both `Radius` and `Diameter` can be tedious, unless we can implmement typed-typedef like which in Golang.

---

[CppCon 2015: Peter Sommerlad “Variadic Templates in C++11 / C++14 - An Introduction”](https://www.youtube.com/watch?v=R1G3P5SRXCw)

Key point for writing variadic function template: using recursion in the definition

That is:
- base case with zero arguments (matches in the end)
- recursive case with 1 explicit argument and a tail consisting of a variadic list of arguments.

Caveats: Recursion needs a base code even if never called. exists for compilation.

Thus, put base case ahead of recursive one, because of overload name lookup.

Variadic class template is less useful than its function template cousin.

SFINAE tricks can be sometimes helpful.

---

[Python's GIL implemented in pure Python](https://rushter.com/blog/python-gil-thread-scheduling/)

The post presents GIL logic using python _pseduo-code_

extractions:
1. threads each runs a event-loop and before running acquire the GIL
2. the running thread checks if other threads are waiting after execution of some instructions.
3. if there are awaiting threads, the running thread drops the GIL and let other threads have chance to run
4. after the drop, it immediately request for GIL, making itself waiting on GIL

The synchronization among threads are just plain mutex + condition

## Network

[TCP Flow Control](https://www.brianstorti.com/tcp-flow-control/)

A quite awesome post for explaining TCP flow control mechanim.

Content is easy to follow

## Algorithms

[Weighted random generation in Python](https://eli.thegreenplace.net/2010/01/22/weighted-random-generation-in-python)

Introduction to a few weighted random selection algorithms.

This post is quite amazing and algorithms are quite elegant.

## Distributed Systems

[Raft: Consensus made simple(r)](https://www.brianstorti.com/raft/)

An excellent introduction post to Raft.

---

[SWIM: The scalable membership protocol](https://www.brianstorti.com/swim/)

A novel membership protocol.

## Misc

[Zombie Processes are Eating your Memory](https://randomascii.wordpress.com/2018/02/11/zombie-processes-are-eating-your-memory/)

When you create or open a process, its your responsiblity to close the handle once you complete your job, and its vital...

And by the way, if you write C++ code, RAII is always your best friend for automatic resource management.

---

[What We Talk About When We Talk About Performance](https://randomascii.wordpress.com/2018/02/04/what-we-talk-about-when-we-talk-about-performance/)

How to describe your performance improvement correctly.

An easy and clear way is to use speedup ratio, such as 1.5x faster or 2x faster.

---

[Python Application Layouts: A Reference]( https://realpython.com/python-application-layouts/)

Efficient ways to structure your python project layouts.

A classic layout for a project with multiple sub-packages

```
helloworld/
│
├── bin/
│
├── docs/
│   ├── hello.md
│   └── world.md
│
├── helloworld/
│   ├── __init__.py
│   ├── runner.py
│   ├── hello/
│   │   ├── __init__.py
│   │   ├── hello.py
│   │   └── helpers.py
│   │
│   └── world/
│       ├── __init__.py
│       ├── helpers.py
│       └── world.py
│
├── data/
│   ├── input.csv
│   └── output.xlsx
│
├── tests/
│   ├── hello
│   │   ├── helpers_tests.py
│   │   └── hello_tests.py
│   │
│   └── world/
│       ├── helpers_tests.py
│       └── world_tests.py
│
├── .gitignore
├── LICENSE
└── README.md
```