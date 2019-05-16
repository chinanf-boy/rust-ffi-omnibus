---
layout: default
examples: ../examples/string_return/
title: 字符串返回值
---

# Rust 函数：返回 已分配字符串

与[返回一个对象][objects]同样的原因，通过 FFI 返回一个分配的字符串很复杂: Rust 分配器 可以与 FFI 边界另一侧(语言)的分配器不同。它与[传递一个字符串参数][string-arguments]相同，也有处理 NUL 终止字符串的限制。

{%example src/lib.rs%}

这里我们使用一对方法[`into_raw`][into_raw]和[`from_raw`][from_raw]。先会将一个`CString`转换成一个原始指针，这样也许可以跨过 FFI 边界。字符串的所有权，传递给了调用者，但调用者必须将字符串返回给 Rust，才能正确释放内存。

[objects]: ../objects
[string-arguments]: ../string_arguments
[into_raw]: https://doc.rust-lang.org/std/ffi/struct.CString.html#method.into_raw
[from_raw]: https://doc.rust-lang.org/std/ffi/struct.CString.html#method.from_raw

## C

{%example src/main.c%}

在 C 版本，这没什么好玩的: `char *`是返回的，可以打印，然后传回去，释放.

## Ruby

{%example src/main.rb%}

因为 字符串已分配，所以我们需要在超出范围时，确保释放分配。 像一个[对象][objects]，我们的`FFI::AutoPointer`子类，会为我们自动释放指针。

我们定义`to_s`函数，使用 UTF-8 编码，将原始字符串延迟转换为一个 Ruby 字符串，并记住结果。Rust 生成的任何字符串，都是有效的 UTF-8。

## Python

{%example src/main.py%}

我们必须使用`c_void_p`代替`c_char_p`，作为类型的返回值，因为原`c_char_p`，将自动转换为 Python 字符串。这个字符串将被 Python 不正确地释放，而不是由 Rust 释放。

我们换成`c_void_p`，不要`c_char_p`，获取值，并将原始字节编码为 UTF-8 字符串。

## Haskell

{%example src/main.hs%}

在调用 FFI 方法之后，我们检查字符串是否是`NULL`。 如果没有，我们使用`peekCString` 将其转换为 Haskell 字符串，并让 Rust 字符串 `free`。

## Node.js

{%example src/main.js%}

该字符串作为`char *`返回，我们可以在将它传回去之前，通过调用`readCString`转换为 JavaScript 字符串。

## C\

{%example src/main.cs%}

我们遵循与 Object 示例相似的模式: Rust 字符串是包含在`SafeHandle`子类，和一个包装类`ThemeSong`确保 Handle 是正确`Dispose`的。

不幸的是，没有简单的方法将，指针读作一个 UTF-8 字符串。 C#有 ANSI 字符串和"Unicode"字符串 (实际上是 UCS-2) 的情况，但没有 UTF-8。 我们需要自己写。

## Julia

{% example src/main.jl %}

我们使用 `Cstring` 数据类型表示为一个 NUL-终止 字符。而不是用 Julia 空间握住已分配的字符，这个例子用`unsafe_string`建立了字符串的一个副本，由 Julia 自身管理，并在之后传回这个 Rust 字符串。该[objects][julia-objects]章节，提供了一个资源保持存活的例子。

[julia-objects]: ../objects#julia
