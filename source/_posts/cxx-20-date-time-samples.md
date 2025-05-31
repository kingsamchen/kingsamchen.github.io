---
title: C++20 Calendar Datetime Quick Intro
categories: PROGRAMMING
date: 2025-05-31 15:09:32
tags: [cpp, datetime, chrono]
---

> 💡 标准库是以 Howard Hinnant 的 date library 为原型，考虑到编译器的支持，以下用 date library 为例子

## 核心类型 year_month_day

可以把 `year_month_day` 当作 field-based 的概念上的 time point，并且它的 resolution 是 days

它的 synopsis

```cpp
class year_month_day
{
    date::year  y_;  // exposition only
    date::month m_;  // exposition only
    date::day   d_;  // exposition only

public:
    year_month_day() = default;
    constexpr year_month_day(const date::year& y, const date::month& m, const date::day& d) noexcept;
    constexpr year_month_day(const year_month_day_last& ymdl) noexcept;
    constexpr year_month_day(const sys_days& dp) noexcept;
    constexpr explicit year_month_day(const local_days& dp) noexcept;

    constexpr year_month_day& operator+=(const months& m) noexcept;
    constexpr year_month_day& operator-=(const months& m) noexcept;
    constexpr year_month_day& operator+=(const years& y) noexcept;
    constexpr year_month_day& operator-=(const years& y) noexcept;

    constexpr date::year year()   const noexcept;
    constexpr date::month month() const noexcept;
    constexpr date::day day()     const noexcept;

		// to sys_days and local_days
    constexpr          operator sys_days() const noexcept;
    constexpr explicit operator local_days() const noexcept;
    constexpr bool ok() const noexcept;
};
```

为了和标准库 chrono 交互，提供了 `operator sys_days()` 的转换

```cpp
using days = std::chrono::duration
    <int, std::ratio_multiply<std::ratio<24>, std::chrono::hours::period>>;
using sys_days = std::chrono::time_point<std::chrono::system_clock, days>;
```

### to sys_days

```cpp
TEST_CASE("Year month day object") {
    auto chloe_dob = date::year_month_day(date::year(2025), date::month(5), date::day(22));
    // The construction of year_month_day object may be invalid.
    REQUIRE(chloe_dob.ok());
    auto chloe_dob_tp = date::sys_days(chloe_dob);
    auto me_dob = date::year_month_day(date::year(1992), date::month(7), date::day(26));
    auto me_dob_tp = date::sys_days(me_dob);
    CHECK_EQ((chloe_dob_tp - me_dob_tp).count(), 11988);
}
```

注意： year_month_day ↔ sys_days 的转换都是 implicit

### invalid state

`ok()` 函数的存在是因为构建 year_month_day 对象可能不合法，为了耦合具体的错误处理方法，比如抛出异常，所以用 `ok()` 判断

```cpp
TEST_CASE("validness of year_month_day") {
    CHECK(date::year_month_day(date::year(2024), date::month(2), date::day(29)).ok());
    CHECK_FALSE(date::year_month_day(date::year(2025), date::month(2), date::day(30)).ok());
    CHECK_FALSE(date::year_month_day(date::year(2025), date::month(13), date::day(1)).ok());
}
```

### adding years / months

通过 overload operators 那部分可以看到，`year_month_day` 可以直接加减 `years` 和 `months` 类型；不过要注意结果对象可能是不合法的：

```cpp
TEST_CASE("Add to year_month_day") {
    SUBCASE("add years") {
        auto ymd = date::year(2024) / date::month(2) / date::day(1);
        REQUIRE(ymd.ok());
        ymd += date::years(1);
        CHECK(ymd.ok());
        CHECK_EQ(static_cast<int>(ymd.year()), 2025);
    }

    SUBCASE("add years but invalid") {
        auto ymd = date::year(2024) / date::month(2) / date::day(29);
        REQUIRE(ymd.ok());
        ymd += date::years(1);
        CHECK_FALSE(ymd.ok());
    }

    SUBCASE("add months") {
        auto ymd = date::year(2024) / date::month(2) / date::day(1);
        REQUIRE(ymd.ok());
        ymd += date::months(1);
        CHECK(ymd.ok());
        CHECK_EQ(static_cast<unsigned int>(ymd.month()), 3);
    }

    SUBCASE("add months but invalid") {
        auto ymd = date::year(2024) / date::month(1) / date::day(30);
        REQUIRE(ymd.ok());
        ymd += date::months(1);
        CHECK_FALSE(ymd.ok());
    }
}
```

### adding days

`year_month_day` 自身不提供对 `days` 的加减，但是我们可以借用 `sys_days` 作为中介来完成：

```cpp
TEST_CASE("Add days to year_month_day") {
    auto ymd = date::year(2024) / date::month(2) / date::day(29);
    REQUIRE(ymd.ok());
    ymd = date::sys_days(ymd) + date::days(2);
    CHECK_EQ(ymd, date::year(2024) / date::month(3) / date::day(2));
}
```

adding days 可以很好的避免结果日期可能是某个不正确的值的情况

## Last Day of the Month

利用的是提供的 `year_month_day_last` 这个类型，常用实践中可以直接构造 year_month_day_last 也可以用 `operator/` 搭配常量 `last` 来方便构造

```cpp
TEST_CASE("The last day of the month") {
    SUBCASE("last day of feb in a leap year is 29") {
        auto eom = date::year_month_day_last(date::year(2024),
                                             date::month_day_last(date::month(2)));
        REQUIRE(eom.ok());
        CHECK_EQ(static_cast<unsigned>(eom.day()), 29);
    }

    SUBCASE("compose last day with more intuitive operator overload") {
        auto eom = date::year(2025) / date::May / date::last;
        REQUIRE(eom.ok());
        CHECK_EQ(static_cast<unsigned>(eom.day()), 31);
    }
}
```

`date::day` 内部的成员是 unsigned，所以提供的 explicit conversion operator 也是 unsigned