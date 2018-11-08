---
layout: default
examples: ../examples/string_return/
title: 字符串返回值
---
# 返回 已分配字符串 的 Rust函数

由于同样的原因,通过FFI 返回分配的字符串 很复杂[而返回一个对象][objects]: Rust分配器 可以与 FFI边界另一侧的分配器不同. 它也有与[传递一个字符串参数][string-arguments]处理 NUL终止字符串 相同的限制. 

{%example src/lib.rs%}

这里我们使用一对方法[`into_raw`][into_raw]和[`from_raw`][from_raw]. 这些转换一个`CString`到 可以通过FFI边界的 原始指针. 字符串的所有权 将传递 给调用者,但 调用者 必须将 字符串返回给Rust 才能正确释放内存. 

[objects]: ../objects

[string-arguments]: ../string_arguments

[into_raw]: https://doc.rust-lang.org/std/ffi/struct.CString.html#method.into_raw

[from_raw]: https://doc.rust-lang.org/std/ffi/struct.CString.html#method.from_raw

## C

{%example src/main.c%}

对于C版本来说没什么好玩的: `char *`是返回的,可以打印,然后转回来释放. 

## Ruby

{%example src/main.rb%}

因为 字符串已分配,所以我们需要确保 在超出范围时 解除分配. 像一个[对象][objects],我们的`FFI::AutoPointer`子类会为我们自动释放指针. 

我们定义`to_s`使用 UTF-8编码 将 原始字符串 延迟转换为 Ruby字符串 并记住结果. Rust生成的 任何字符串 都是有效的 UTF-8. 

## Python

{%example src/main.py%}

我们必须使用`c_void_p`代替`c_char_p`作为类型的返回值,因为原`c_char_p`将自动转换为Python字符串. 这个字符串将被 Python不正确地释放,而不是Rust. 

我们投了`c_void_p`到了`c_char_p`,获取值,并将 原始字节编码为UTF-8字符串. 

## Haskell

{%example src/main.hs%}

在调用FFI方法之后,我们检查字符串是否是`NULL`. 如果没有,我们使用`peekCString` 将 其转换为Haskell字符串 并释放 Rust字符串. 

## Node.js

{%example src/main.js%}

该字符串作为`char *`返回,我们可以通过调用`readCString`转换为JavaScript字符串,在将它传回去之前. 

## C\#

{%example src/main.cs%}

我们遵循与 对象示例 类似的模式: Rust字符串是包含在`SafeHandle`子类和一个包装类`ThemeSong`,确保控制正确放置. 

不幸的是,没有简单的方法将 指针 读作 UTF-8字符串. C#有 ANSI字符串和"Unicode"字符串 (实际上是UCS-2) 的情况,但没有UTF-8. 我们需要自己写. 

## Julia

{% example src/main.jl %}

我们使用 `Cstring` 数据类型 表示为一个 NUL-终止 字符。
而不是用Julia空间已分配的字符表示, 这个例子
用`unsafe_string`建立了字符的副本 , 由Julia自身管理, 并在之后传回Rust字符串。该
[objects][julia-objects]部分提供了一个
在Julia语言,资源仍然存在的例子。

[julia-objects]: ../objects#julia
