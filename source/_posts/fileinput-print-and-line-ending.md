---
title: Python fileinput 换行编码问题
categories: PROGRAMMING
date: 2020-09-08 11:54:27
tags: [python, line ending, fileinput]
---
昨晚抽了个时间想修一下 anvil 的这个 [issue](https://github.com/kingsamchen/anvil/issues/2)。

根据之前的代码实现，我有 99.99% 的把握换行符被替换成 `CRLF` 是使用了 `fileinput` 原地修改了文件导致的。

那段修改的代码的一个等价实现如下：

```python
import shutil
import fileinput
import sys

def poc():
    orig_name = 'orig.txt'
    new_name = 'new.txt'

    shutil.copy(orig_name, new_name)

    with fileinput.FileInput(new_name, inplace=True) as f:
        for line in f:
            line = '-> ' + line
            print(line, end='')
```

`orig.txt` 是一个模板文件，它会被拷贝到指定目录变成一个新文件 `new.txt`，并修改文件内容。
<!-- more -->
上述代码使用 `fileinput` 并且通过指定 `inplace=True` 来完成原地编辑；`print()` 会直接往 stdio 输出 `fileinput` 内部做了重定向将 stdout 的数据写到磁盘上。

`end=''` 是避免默认地往每行加一个换行。

一切看起来是那么的标准，因为实现基本是直接从网上抄的，比如 [Python fileinput 使用总结](https://laike9m.com/blog/python-fileinput-shi-yong-zong-jie,23/)。

但是实际中发现个问题：原文件的 EOL 是 `LF`，但是新文件的却是 `CRLF`。系统是 Windows，环境是 Python 3.6。

百思不得其解，拿 _fileinput force line ending_ 作为关键字在网上搜了好久也没有解决问题；比如最基本的用 `mode=rb` 以二进制打开文件，最后问题依旧。

睡了一觉后早上起来一琢磨这问题是不是由 `print()` 导致的？

经过一番搜索发现问题果然是 `print()` 导致的....

SO 上有两个类似的问题：

1. [Prevent Python print()'s automatic newline conversion to CRLF on Windows](https://stackoverflow.com/questions/49709309/prevent-python-prints-automatic-newline-conversion-to-crlf-on-windows)
2. [Print LF with Python 3 to Windows stdout](https://stackoverflow.com/questions/34960955/print-lf-with-python-3-to-windows-stdout)

### 原因

`print()` 内部使用的 Text I/O Wrapper 卡死了 newline 跟着系统默认，导致最后往 stdout 输出的时候自动把 `LF` 转为了 `CRLF`

### 解决方案

(1) 中的解决方案虽然多种多样并且允许你直接使用 `print()` 来输出 `newline='\n'` 但是大体上还是太不清真了。

一个看起来比较干净的方案是：

```python
with fileinput.FileInput(new_name, inplace=True, mode='rb') as f:
    for line in f:
        line = b'-> ' + line
        sys.stdout.buffer.write(line)
```

绕开 `print()` 直接网 `stdout.buffer` 写。

因为这里已经是二进制数据了，所以需要用 `rb` 打开文件并且字符串操作都要转换为 bytes。

所以总结一下就是：Stop Using Print() for fileinput data modification!

### Rant 1

实际上我解决这个 issue 的 [PR](https://github.com/kingsamchen/anvil/commit/4257edac56d4a15438d72c2c652d2e61aa00ebc5) 没有用这个方法，而是直接放弃了 fileinput 用最传统的文件 I/O 解决了。

谁知道 `fileinput` 还有哪些不知道的坑呢...

### Rant 2

fileinput in-place rewrite 依赖 stdout 这个真是坑啊 🙄
