---
layout: default
examples: ../examples/string_arguments/
title: 字符串 参数
---

# Rust 函数使用字符串参数

让我们开始一些更复杂的东西，接受字符串参数。在 Rust 中，字符串由一组`u8`切片组成，并保证是有效的 UTF-8，允许`NUL`字节，在字符串内部。在 C 中，字符串只是指向一个`char`的指针，用一个`NUL`字节作为终止 (带整数值`0`) 。需要做一些转换工作，才能在处理好这两种表达。

{%example src/lib.rs%}

获取一个 Rust 字符串切片 (`&str`) 需要几个步骤:

1.  我们必须确保 C 指针不是`NULL`，因为 Rust 引用不能`NULL`。(会在 C 语言调用该函数，对应的，在 C 中，字符串只是指向一个`char`的指针，所以，该 Rust 函数的字符串参数，会迎来一个 C 指针。)

2.  使用[`std::ffi::CStr`][cstr]包装指针。`CStr`将根据终止的`NUL`，计算字符串的长度。这需要一个`unsafe`区块，因为我们将解引用一个原始指针，Rust 编译器无法验证该指针，满足所有安全保证，因此程序员必须这样做。（`unsafe`的作用，不是说有多么特殊，只是为了让人们更快，且更好地注意到，存在安全隐患的代码。）

3.  确保 C 字符串是有效的 UTF-8 ，并将其转换为 Rust 字符串切片。

4.  使用字符串切片。

在这个例子中，如果我们的任何先决条件失败，我们只是简单中止程序。每个用例必须评估什么是适当的故障模式，但是 大声和尽早失败 是一个很好的起步。

[cstr]: http://doc.rust-lang.org/std/ffi/struct.CStr.html
[to_str]: https://doc.rust-lang.org/nightly/std/ffi/struct.CStr.html#method.to_str

## 所有权 和 生命周期

在这个例子中，Rust 代码确实 **没有** 拥有字符串切片，编译器只允许字符串与`CStr`实例生命一样。程序员应该确保这个生命周期足够短。

## C

{%example src/main.c%}

C 代码声明函数，接受指向常量字符串的指针，因为 Rust 函数不会改字符串。然后，您可以使用正常的 C 字符串常量，调用该函数。

## Ruby

{%example src/main.rb%}

FFI 自动将 Ruby 字符串，转换为适当的 C 字符串.

## Python

{%example src/main.py%}

Python 字符串，必须编码为 UTF-8，才能通过 FFI 边界.

## Haskell

{%example src/main.hs%}

该`Foreign.C.String`模块支持将 Haskell 的字符串描述，转换为 C 包装字节的描述。我们可以创建一个带`newCString`的函数，然后传递`CString`到我们的外部函数。

## Node.js

{%example src/main.js%}

该`ffi`包自动将 JavaScript 字符串，转换为适当的 C 字符串。

## C\

{%example src/main.cs%}

原生字符串会自动整理为 与 C 兼容的字符串。

## Julia

{% example src/main.jl %}

Julia 字符串 (`AbstractString`的基础类型) 自动转换为 C 字符串。 这个来自 Julia 的 `Cstring` 类型与 Rust 的`CStr`类型兼容， 因为它也假设一个`NUL`终止字节
，且不允许`NUL`字节被嵌入字符串。
