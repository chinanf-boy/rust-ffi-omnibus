---
layout: default
examples: ../examples/tuples
title: 元组
---
# 接受并返回元组的Rust函数

C没有元组的概念,但最接近的模拟是一个普通的结构. 您需要为 每种独特的类型组合 创建单独的结构. 在这里,我们创建一个表示两个32位无符号整数的结构. 

{%example src/lib.rs%}

`#[repr(C)]`用于通知编译器,它应该像 C编译器那样 排列结构的字段.在结构和相应的元组之间,这两个转换实现 使用[`std::convert::From`][from]提供符合人体工程学的转换. 

[from]: http://doc.rust-lang.org/std/convert/trait.From.html

## C

{%example src/main.c%}

由于我们符合C兼容的习语,因此实现是直截了当的. 我们定义一个`struct` 使用与 Rust结构的类型 和 顺序匹配的字段,然后创建一个实例并调用该方法. 

## Ruby

{%example src/main.rb%}

为了镜像结构定义,我们创建了一个子类`FFI::Struct`并使用`layout`指定字段名称和类型. 

附加功能时,我们使用`by_value`表示结构将直接传递,而不需要通过指针进行间接传递. 

## Python

{%example src/main.py%}

为了镜像结构定义,我们创建了一个`ctypes.Structure`子类, 并使用`_fields_`指定字段名称和类型. 

## Haskell

不幸的是,Haskell目前不支持 传递或返回任意结构. 始终需要指针间接. 

## Node.js

{%example src/main.js%}

该[`ref-struct`][ref-struct]允许我们构建可以传递给 FFI函数 的结构类型. 

[ref-struct]: https://www.npmjs.com/package/ref-struct

## C\#

{%example src/main.cs%}

为了镜像 元组结构定义,我们创建了一个`struct`,使用`StructLayout`属性并将 布局定义为顺序. 我们还提供了一些 implicit-隐式转换 运算符,使类型之间的转换相当无缝. 

## Julia

{% example src/main.jl %}

使用完全相同的字段布局定义的Julia结构类型，已与C的数据数组兼容。 由于所有字段都是[`isbits`][julia-isbits]，因此Tuple类型也是如此。这是因为，它存储每个内联内容，并将按值传递给原生函数。


[julia-isbits]: https://docs.julialang.org/en/v1/base/base/#Base.isbits
