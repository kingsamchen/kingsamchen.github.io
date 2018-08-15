---
title: Dealing With Multiple Types As a Category
categories: PROGRAMMING
date: 2018-08-15 21:57:49
tags: [c++, SFINAE, enum, tag dispatching]
---
Macro `ENSURE()` from KBase can 'capture' variables by outputing their content to the internal stringtream, provided the type of captured variables has overloaded `operator<<()`.

However, for variables of enum-class types, which disallow implicit conversion to underlying integer, there is no easy way to enable `ENSURE()` to capture them, because every enum-class is a type, and it is not realistic to define an overloaded `operator<<()` for each of them.

We need to handle all enum-class types as a category.

`std::is_enum<T>` can identify a type if it is an enum at compile-time (it is one of type-traits utils), and in conjunction with some SFINAE tricks, we can categorize types at compile-time and handle them with different policies.

```cpp
struct general_category_tag {};
struct wide_string_category_tag {};
struct enum_category_tag {};

template<typename T, typename = void>
struct map_type_to_category
    : general_category_tag
{};

template<typename T>
struct map_type_to_category<T, std::enable_if_t<std::is_enum<T>::value>>
    : enum_category_tag
{};

template<typename T>
struct map_type_to_category<T, std::enable_if_t<std::is_same<T, std::wstring>::value ||
                                                std::is_same<T, kbase::WStringView>::value>>
    : wide_string_category_tag
{};
```

Now that we can decide which category a type should fall into, we then simply use tag dispatching to locate target handler.

```cpp
template<typename T>
Guarantor& CaptureVar(const char* name, const T& value)
{
    HandleCapturedVar(name, value, internal::map_type_to_category<T>{});
    return *this;
}

template<typename T>
void HandleCapturedVar(const char* name, const T& value, internal::general_category_tag)
{
    RecordCapturedVar(name, value);
}

template<typename T>
void HandleCapturedVar(const char* name, const T& value, internal::wide_string_category_tag)
{
    RecordCapturedVar(name, WideToUTF8(value));
}

template<typename T>
void HandleCapturedVar(const char* name, T value, internal::enum_category_tag)
{
    RecordCapturedVar(name, enum_cast(value));
}

template<typename T>
void RecordCapturedVar(const char* name, const T& value)
{
    exception_desc_ << "    " << name << " = " << value << "\n";
}
```

This design also has great extensibility.