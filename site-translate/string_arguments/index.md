---
layout: default
examples: ../examples/string_arguments/
title: 字符串 参数
---
# Rust函数使用字符串参数

让我们从一些更复杂的东西开始,接受 字符串 作为参数. 在Rust中,字符串由一组`u8`切片组成,并保证是有效的UTF-8,允许`NUL`字符串内部的字节. 在 C中,字符串只是指向`char`的指针,和被一个`NUL`字节终止 (带整数值`0`) . 需要做一些工作 才能在这两种表示之间进行转换. 

{%example src/lib.rs%}

获取 Rust字符串切片 (`&str`) 需要几个步骤: 

1.  我们必须确保 C指针不是`NULL`因为 Rust引用不允许使用`NULL`. 

2.  使用[`std::ffi::CStr`][cstr]包装指针. `CStr`将根据终止`NUL`计算字符串的长度. 这需要一个`unsafe`区块,因为我们将取消引用一个原始指针,Rust编译器 无法验证 该指针满足所有安全保证,因此程序员必须这样做. 

3.  确保C字符串是 有效的UTF-8 并将其转换为 Rust字符串切片. 

4.  使用字符串切片. 

在这个例子中,如果我们的任何先决条件失败,我们只是简单地中止程序. 每个用例必须评估什么是适当的故障模式,但是 大声和尽早失败 是一个很好的初始位置. 

[cstr]: http://doc.rust-lang.org/std/ffi/struct.CStr.html

[to_str]: https://doc.rust-lang.org/nightly/std/ffi/struct.CStr.html#method.to_str

## 所有权 和 生命周期

在这个例子中,Rust代码确实如此 **不** 拥有字符串切片,编译器只允许字符串与`CStr`实例生命一样. 程序员应该确保这个寿命足够短. 

## C

{%example src/main.c%}

C代码 声明 函数接受 指向常量字符串 的指针,因为Rust函数不会修改它. 然后,您可以使用正常的 C字符串常量 调用该函数. 

## Ruby

{%example src/main.rb%}

FFI 自动将 Ruby字符串 转换为 适当的C字符串. 

## Python

{%example src/main.py%}

Python字符串 必须编码为 UTF-8 才能通过 FFI边界. 

## Haskell

{%example src/main.hs%}

该`Foreign.C.String`模块 支持 将 Haskell的字符串描述 转换为 C的压缩字节描述. 我们可以创建一个带`newCString`的函数,然后传递`CString`到我们的外部函数. 

## Node.js

{%example src/main.js%}

该`ffi`包自动将 JavaScript字符串 转换为 适当的C字符串. 

## C\#

{%example src/main.cs%}

原生字符串 会自动编组为 与C兼容的字符串. 

## Julia

{% example src/main.jl %}

Julia strings (`AbstractString`的基础类型) 自动转换为 C strings。 这个来自Julia 的 `Cstring` 类型与 Rust 的`CStr`类型兼容, 因为它也假设一个`NUL`的终结符字节
且不允许 `NUL` 字节 被嵌入 字符串.
