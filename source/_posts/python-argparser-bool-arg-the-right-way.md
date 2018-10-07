---
title: Using Boolean Switch with Python Argparser the Right Way
categories: PROGRAMMING
date: 2018-10-07 13:45:20
tags: [python, argparser, bool argument]
---
## The Context

之前拿 Python 写了一个 CMake 的 build driver，因为要控制一些编译参数，所以使用了如下代码：

```python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument('--build-test', dest='build_test', type=bool, default=True)
args = parser.parse_args()

no_build_unittest = not args.build_test
```

后来有一次偶然发现，无论指定 `--build-test=False` 还是 `--build-test=false` 还是 `--build-test=0`，`args.build_test` 的值永远是 `True`。

然后一脸懵逼。

万幸有万能的 Stackoverflow，搜了一下发现有人遇到了类似的问题。

问题的根源在于，即使你指定了 `type=bool`，你的输入仍然是 `string`，而 argparse 没有做 string 到 boolean 的语义化转换，仅简单地判断了一下字符串是否为空，所以前面的参数指定全都被认为是 `True`。

## The Solution

解决的方案是将参数类型指定为 `lambda` 然后自己做转换：

```diff
-parser.add_argument('--build-test', dest='build_test', type=bool, default=True)
+parser.add_argument('--build-test', dest='build_test',
+                    type=lambda opt: bool(strtobool(opt)), default=True)
```

Argparse 这个点的实现个人感觉不好，因为违反了直觉。

另，我在饭否上吐槽这个问题的时候，F叔提到了 [docopt](https://github.com/docopt/docopt) 作为替代品。但是我看了一下简介之后对这种使用描述生成的方式并不感冒（虽然看起来很酷），第一眼看着觉着 verbose，而且总感觉这种比较 fancy 的做法在一些 edge cases 上可能会更痛苦（想起之前折腾各种 build system 的痛苦 -''-）。

不懂之后会不会出现真香的反转，XD
