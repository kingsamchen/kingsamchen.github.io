---
title: "hashids: generate short unique ids from integers"
categories: PROGRAMMING
date: 2023-08-07 22:01:25
tags: [hashids, unique id]
toc: true
mathjax: true
---
## 什么是 hashids

hashids 是一种可以将一个或多个 integer(s) 编码为字符串的算法，并且支持自定义字母表和盐。

例如可以将

- `347` 搭配 `salt="this is my salt"` 编码为 `yr8`
- `[27, 986]` 搭配相同的盐编码为 `3kTMd`

总结一下特性就是

1. Generate short, unique, non-sequential ids from **non-negative numbers** $(n\geqslant0)$
2. Allow **custom alphabet and custom salt**; so ids are unique only to you
3. Incremental input is mangled to stay unguessable

FYI：他们最近换了个品牌叫 sqids https://sqids.org/。前端的人真能折腾...

## 具体算法

注：这部分算法是根据 hashids-python 记录的

### 0x0 Initialize Separator-Alphabet & Content-Alphabet & Guard-Alphabet

默认字母表：base62，即 a-zA-Z0-9

最终生成的字符串使用的字符必须全都来自于 alphabet，但是这个 alphabet 会被拆成两个**核心**子表（因为最终是三个表）：

1. separators 表（下面简称 SA）：用于分隔实际转换生成的字符
生成方式是从基表 `'cfhistuCFHISTU'` 中去掉初始 alphabet 中不存在的字符，因为前面讲了最终生成的字符串的所有字符必须来自 alphabet

    💡 基表选用这部分字符的目的之一是为了保证生成的最终字符串不包含 SHIT 等脏词

2. 内容字母表（下面简称 CA）：一开始的 alphabet 去掉 separators 表字符之后的字符表
需要同时确保这个表**没有重复字符**
3. 长度上需要满足 $|SA|+|CA| \geqslant 16$ 否则给定的 alphabet 不合格
4. 将 ***SA*** 结合 ***salt*** 进行 shuffle；到这步 ***SA*** 的初始化就完成了
5. 为了保证 ***SA*** 中的字符能够比较“均匀”的混入正文字符中，***SA*** 的长度和 ***CA*** 长度相差不能太大：需要保证

    $$
    \frac{|CA|}{|SA|} \leqslant 3.5
    $$

    即，单个 separator 字符不能“搭配”超过3.5个正文字符。

    变形一下公式可以换算得到 *minimum-separators* 的个数，这个数必须 ≤ 前面 ***SA*** 的大小

6. 如果 5. 不满足要求那么需要对 ***SA*** 和 ***CA*** 进行微调整：因为 ***CA*** 多了 ***SA*** 少了所以需要匀一下：

    ```python
    separators += alphabet[:number_of_missing_separators]
    alphabet = alphabet[number_of_missing_separators:]
    len_alphabet = len(alphabet)
    ```

    因为 ***SA*** 前面已经 shuffle 过了，所以即使这里做了挪动，也不需要再次进行 shuffle；只需要更新一下长度即可

7. 现在可以对 ***CA*** 也结合 salt 做一次 shuffle 了；到这步 ***CA*** 的初始化也大致算完成了
8. 最后需要处理 guard alphabet（后面简称 ***GA***），这个表是从 ***SA*** 或者 ***CA*** “匀”出来的。首先根据公式计算需要的 guards 数量

    $$
    K=\left \lceil \frac{|CA|}{12} \right \rceil
    $$

9. 再看 $|CA| \lt 3$ 是否成立，如果成立那么从 ***SA*** 头部匀给 ***GA***，否则则从 ***CA*** 头部匀给 ***GA***

    ```python
    num_guards = _index_from_ratio(len_alphabet, RATIO_GUARDS)
    if len_alphabet < 3:
        guards = separators[:num_guards]
        separators = separators[num_guards:]
    else:
        guards = alphabet[:num_guards]
        alphabet = alphabet[num_guards:]
    ```

    考虑到来源，***GA*** 就不再需要 shuffle 了


至此，初始化工作最重要的三个字母表就都确定了

### 0x1 Reorder/Shuffle Alphabet with Salt

Reorder 只会在提供了 Salt 的情况下进行。

本质是 swap characters as per salt，即利用 salt 数据来在每一轮迭代产生一个下表 `j`，来和遍历下标 `i` 产生交换。

伪码描述如下

```
fn shuffle(str, salt) -> string {
  if len(salt) == 0 {
    return str
  }

  salt_idx, sum = 0, 0
  for iq = len(str) - 1 ... 1 {
    n = ascii(salt[salt_idx])
    sum += n
    j = (salt_index + n + sum) % i
    str[i] <-> str[j]
    salt_idx = (salt_idx + 1) % len(salt)
  }
  return str
}
```

### 0x2 Encode

Encode 的输入是一个 array of non-negative integers；这个要求是 hashids 算法一开始就要求的。

1. 首先计算输入序列的 value-hash：

    `i` 是 `x` 对应的下标

    ```
    value_hash = 0
    for i, value in values {
      value_hash += value % (i + 100)
    }
    ```

2. 确定 lottery character，这里会用到 ***CA***

    ```cpp
    lottery = CA[value_hash % len(CA)]
    ```

    并且 lottery 是最终生成内容 `encoded` 的第一个字符

3. 关键一步：逐一迭代输入序列的每个元素，并且每轮迭代
    1. 利用 lottery, salt 和上一轮的 CA 生成 CA_Salt
    2. 利用 CA 和 CA_Salt 对 CA 进行 reorder/shuffle；这里的 reorder 是强制的，因为 CA_Salt 不会为空
    3. 结合这轮的 value 和 CA 做一个 hash 算出本轮的内容字符，append 到 `encoded`

        ```
        fn hash(number, alphabet) -> string {
          string hashed
          while true {
            hashed = alphabet[number % len(alphabet)] + hashed
            number /= len(alphabet)
            if number == 0 {
              return hashed
            }
          }
        }
        ```

    4. 利用 hash 结果来选择本轮的 separator 字符，也 append 到 `encoded` 上

    ```
    for i, value in values {
      ca_salt = concat(lottery, salt, ca)[:len(ca)]  // salt may be empty
      ca = shuffle(ca, ca_salt)
      hashed = hash(value, ca)
      encoded.append(hashed)
      value %= ascii(hashed[0]) + i
      encoded.append(sa[value % len(sa)])
    }

    encoded.pop_back()  // remove last sep
    ```


如果不要求 minimum length 的话 encode 操作到这里就结束了。

### 0x3 Specified Min Length

如果一开始指定了 encode 要保证的最小长度，那么还需要额外考虑 `encoded` 长度不够的问题。

额外的长度由 Guard Alphabet 提供。

1. 先试试往头部加一个 guard

    ```
    guard_idx = (value_hash + ascii(encoded[0])) % len(GA)
    encoded.prepend(GA[guard_idx])
    ```

2. 如果不够的话再往尾部加一个 guard

    ```
    guard_idx = (value_hash + ascii(encoded[2])) % len(GA)
    encoded.append(GA[guard_idx])
    ```

3. 如果还不够最小长度那就只能继续花式增加，但是这一步反而是从 ***CA*** 而不是 ***GA*** 取字母了。

    不过要注意这里的 CA 是前面 encode 过程中用剩下的 ***CA***，不是原始未经修改的 ***CA***

    ```
    mid = len(ca) / 2
    while len(encoded) < min_length {
      ca = shuffle(ca, ca)
      encoded = ca[mid:] + encoded + ca[:mid]
      excess = len(encoded) - min_length
      if excess > 0 {  // trim a little
        idx = excess / 2
        encoded = encoded[idx:idx+min_length]
      }
    }
    ```


### 0x3 Decode

💡 Decode 必须用相同的 hashid 对象操作，以保证 CA/SA/GA/Salt 一致

1. 先去掉保证长度中额外加的 guard 字符和补充 CA

    做法是直接对 `encoded` 以 GA 中任意字符为 delim 做 split，并且切出来的空串也要保留，即

    ```
    split("1abc1", "123") -> ('', 'abc', '')
    ```

    这样保证长度中的四种情况都可以用如下策略处理

    - split 如果只有一个 part，那么 `parts[0]` 就是原始 `encoded`
    - 如果有2个或3个part，那么 `parts[1]` 就是原始 `encoded`

        并且理论上最多只有3个parts


    到这里可以发现前面 ensure length 的时候本质上是以原始 encoded 为中心往两边扩张

2. 如果选中的 encoded 是空那么说明有问题，需要专门考虑一下
3. `encoded[0]` 是 lottery，并且移除
4. 用同样的 split 处理方法以 ***SA*** 为 delim 进行切分
5. 对每个 part 做 unhash 操作
    1. 用和 encode 一样的操作拿到编码时的 CA

        ```
          ca_salt = concat(lottery, salt, ca)[:len(ca)]  // salt may be empty
          ca = shuffle(ca, ca_salt)
        ```

    2. 执行 unhash 算法解出这个 part 对应的原始值

        ```
        fn unhash(hashed, ca) -> int {
          num = 0
          for c in hashed {
            pos = ca.index(c)
            num *= len(ca)
            num += pos
          }
          return num
        }
        ```

6. 最后做一个 sanity check，即对 decode 的数值做一次 encode 比较是否和输入一样

### 0x4 Custom Alphabet

alphabet 的作用和参与过程前面讲了，这里就总结一下自定义 alphabet 的要求：

- 不能有重复字符
- 长度不能小于16个字符

## Rework with C++

拿 C++ 重新写了一遍算法实现，并且是 bear performance with mind 的态度

代码在 https://github.com/kingsamchen/Eureka/tree/master/hashids/hashids
