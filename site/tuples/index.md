---
layout: default
examples: ../examples/tuples
title: 元组
---

# Rust 函数：接受并返回元组

C 没有元组的概念，但最接近的模拟是一个普通的结构。 您需要为每种独特的类型组合，创建单独的结构。在这里，我们创建一个结构，它表示两个 32 位无符号整数。

{%example src/lib.rs%}

`#[repr(C)]`用于通知编译器，它应该像 C 编译器那样排列结构的字段。在结构和相应的元组之间的转换实现，都用到了[`std::convert::From`][from] trait，提供符合人体工程学的转换操作。

[from]: http://doc.rust-lang.org/std/convert/trait.From.html

## C

{%example src/main.c%}

由于我们符合 C 兼容的习语，因此实现是直截了当的。我们定义一个`struct`，使用与 Rust 结构的类型和顺序匹配的字段，然后创建一个实例并调用该方法。

## Ruby

{%example src/main.rb%}

为了效仿结构定义，我们创建了`FFI::Struct`的一个子类，并使用`layout`指定字段名称和类型。

函数附加(attach_function)时，我们使用`by_value`表示结构是直接传递的，而不需要通过指针进行间接传递。

## Python

{%example src/main.py%}

为了效仿结构定义，我们创建了一个`ctypes.Structure`子类，并使用`_fields_`指定字段名称和类型。

## Haskell

不幸的是，Haskell 目前不支持，传递或返回任意结构。始终需要指针间接。

## Node.js

{%example src/main.js%}

该[`ref-struct`][ref-struct]允许我们，构建可以传递给 FFI 函数 的结构类型。

[ref-struct]: https://www.npmjs.com/package/ref-struct

## C\

{%example src/main.cs%}

为了效仿 元组结构定义，我们创建了一个`struct`，使用`StructLayout`属性，并将布局定义为顺序`sequential`。 我们还提供了一些隐式转换操作，使类型之间的转换无缝连接。

## Julia

{% example src/main.jl %}

使用完全相同的字段布局定义的 Julia 结构类型，已与 C 的数据编排兼容。由于所有字段都是[`isbits`][julia-isbits]，之后自然是`Tuple`类型。这是因为，它存储每个内联内容，并将按值传递给原生函数。

[julia-isbits]: https://docs.julialang.org/en/v1/base/base/#Base.isbits
