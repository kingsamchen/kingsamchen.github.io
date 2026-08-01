---
title: "TotW #2: Sorting with Custom Comparators Done Right — When Your Comparator Breaks std::sort"
categories: PROGRAMMING
date: 2026-08-01 16:02:27
tags: [TotW]
---
Suppose we have a vector of `Person`

```cpp
struct Person {
    std::string name;
    int age;
};

std::vector<Person> people;
```

and we want to sort it in the order of the `age` field, it is very easy and intuitive to use `std::sort` with a custom comparator:

```cpp
std::sort(people.begin(), people.end(),
          [](const Person& lhs, const Person& rhs) {
              return lhs.age < rhs.age;
          });
```

However, if now we need to sort in the combination of `<age, name>`, i.e. sort in the order of name if the age is same, how should we implement our comparator?

Before we get into the answer, let us revisit what a comparator must meet for `std::sort()`.

<aside>
⚠️

Invalid comparator would cause std::sort() to seg fault

</aside>

## Strict Weak Ordering

The standard requires a compliant custom comparator must establish ***strict weak ordering***.

For a comparator:

```cpp
bool comp(constT& a, constT& b);
```

`comp(a, b)` means:

> `a` should appear before `b`.
>

The usual example is:

```cpp
return a < b;
```

The strict weak ordering must satisfy following 4 properties:

### 1. irreflexive

Nothing may come before itself:

```cpp
comp(a,a) == false
```

This is why this comparator is **wrong**:

```cpp
return a <= b;
```

because for equal values, `a <= a` is true.

### 2. asymmetric

If `a` comes before `b`, then `b` cannot come before `a`:

```cpp
if (comp(a, b)) {
    comp(b, a) must be false;
}
```

For ordinary `<`:

```cpp
a < b
```

and:

```cpp
b < a
```

cannot both be true.

### 3. transitive

If `a` comes before `b`, and `b` comes before `c`, then `a` must come before `c`:

```cpp
if (comp(a, b) && comp(b, c)) {
    comp(a, c) must be true;
}
```

For example:

```
1 < 2
2 < 3
therefore 1 < 3
```

A cyclic comparator violates this:

```
rock < paper
paper < scissors
scissors < rock
```

There is no consistent sorted order.

### 4. Equivalence must be transitive

Under a comparator, two values are considered *equivalent* when ***neither*** comes before the other:

```cpp
equivalent(a, b) =>
    !comp(a, b) && !comp(b, a);
```

This does not necessarily mean `a == b`.

For example, when sorting people only by age:

```cpp
return lhs.age < rhs.age;
```

these people are equivalent for sorting purposes:

```cpp
Person{"Alice", 30}
Person{"Bob", 30}
```

Even though they are different objects, neither is ordered before the other.

That equivalence relation must also be transitive:

```
a equivalent to b
b equivalent to c
therefore a equivalent to c
```

This fourth rule is the less obvious part of “weak” ordering.

## Common Invalid Comparators

### Using `<=`

```cpp
return lhs.key <= rhs.key;
```

Invalid because:

```cpp
comp(x, x) == true
```

### Returning “not equal”

```cpp
return lhs.key != rhs.key;
```

For different values, both directions are true:

```cpp
comp(a, b) == true
comp(b, a) == true
```

### Comparator depending on changing external state

```cpp
bool reverse = false;

std::sort(v.begin(), v.end(),
          & {
              reverse = !reverse;
              return reverse ? a < b : a > b;
          });
```

The same pair may produce different answers at different times, so the algorithm cannot construct a coherent order.

### Compare floating-point numbers with plain comparison

```cpp
[](double lhs, double rhs) {
    return lhs < rhs;
}

// nan < 1.0   // false
// 1.0 < nan   // false
// nan < 2.0   // false
// 2.0 < nan   // false
```

## WebRTC also Did it Wrong

The WebRTC was using a comparator below https://chromium.googlesource.com/external/webrtc/%2B/branch-heads/m73/rtc_base/network.cc

```cpp
bool CompareNetworks(const Network* a, const Network* b) {
    if (a->prefix_length() == b->prefix_length()) {
        if (a->name() == b->name()) {
            return a->prefix() < b->prefix();
        }
    }

    return a->name() < b->name();
}
```

and this comparator is unfortunate invalid.

The problem is that it does **not define a consistent lexicographical ordering**. In particular, it ***violates the transitivity*** of comparator equivalence

### A concrete counterexample

Consider three networks with the same name:

```
A = { name: "eth0", prefix_length: 24, prefix: 10.0.0.0 }
B = { name: "eth0", prefix_length: 16, prefix: 10.0.0.0 }
C = { name: "eth0", prefix_length: 24, prefix: 10.0.1.0 }
```

we have got

```
A is equivalent to B
B is equivalent to C
A not equivalent to C (A < C)
```

## Do it Again, in a Right Way, in Most Cases

Back to our question in the first place, lets implement the comparator in a right way

```cpp
std::sort(people.begin(), people.end(),
          [](const Person& lhs, const Person& rhs) {
              return lhs.age < rhs.age ||
                     (lhs.age == rhs.age && lhs.id < rhs.id);
          });
```

So, how should we fix the webrtc’s bug?

```cpp
bool CompareNetworks(const Network* a, const Network* b) {
    return a->name() < b->name() ||
           (a->name() == b->name() &&
            (a->prefix_length() < b->prefix_length() ||
             (a->prefix_length() == b->prefix_length() &&
              a->prefix() < b->prefix())));
}
```

But…it is still error-prone and hard to get it right when the number of fields is scaled.

### Using std::tie()

```cpp
std::sort(people.begin(), people.end(),
          [](const Person& lhs, const Person& rhs) {
              return std::tie(lhs.age, lhs.name) <
                     std::tie(rhs.age, rhs.name);
          });
```

`std::tie()` creates a `std::tuple` with each member being a reference to the corresponding argument, and a tuple is well-defined for comparison.

Similarly, we can use `std::tie()` for the WebRTC issue

```cpp
bool CompareNetworks(const Network* a, const Network* b) {
    return std::tie(a->name(),
                    a->prefix_length(),
                    a->prefix())
         < std::tie(b->name(),
                    b->prefix_length(),
                    b->prefix());
}
```

### Using operator<=> since C++ 20

Noted: we swapped the order of the age and name in order to use compiler synthesized version.

```cpp
struct Person {
    int age;
    std::string name;

    auto operator<=>(const Person&) const = default;
};
```

We will elaborate on the spaceship operator in future, after we upgrade our toolchain to gain the support of C++20/23.

## Sorting Floating Points

### Hand-written with NaN Comes at the Last

```cpp
struct FloatLess {
    bool operator()(double lhs, double rhs) const noexcept {
        const bool lhs_nan = std::isnan(lhs);
        const bool rhs_nan = std::isnan(rhs);

        if (lhs_nan || rhs_nan) {
            return !lhs_nan && rhs_nan;
        }

        return lhs < rhs;
    }
};
```

```
-inf < finite values < +inf < NaN
```

### Using C++20

```cpp
struct FloatLess {
    bool operator()(double lhs, double rhs) const noexcept {
        return std::is_lt(std::weak_order(lhs, rhs));
    }
};
```

## References

- CppCon 2023 | A Long Journey of Changing std::sort Implementation at Scale - Danila Kutenin