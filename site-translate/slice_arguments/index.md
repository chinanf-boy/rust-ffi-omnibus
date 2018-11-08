---
layout: default
examples: ../examples/slice_arguments
title: 切片 参数
---
# 切片参数的Rust函数

Rust*切片*将 指针的概念 与 一块定量元素的数据 捆绑在一起. 在 C中,数组由相同的部分组成,但没有标准容器将 它们保持在一起. 

{%example src/lib.rs%}

转换数组分为两步: 

1.  确保C指针不是`NULL`作为 Rust引用可能永远不会`NULL`. 

2.  使用[`from_raw_parts`][from_raw_parts]将 指针和长度转换为切片. 这是一种不安全的操作,因为 取消引用 可能会无效内存. 

[from_raw_parts]: http://doc.rust-lang.org/std/slice/fn.from_raw_parts.html

## C

{%example src/main.c%}

C调用是直截了当的,因为我们使 Rust代码与C的功能相匹配. 唯一的复杂因素是确保 定义的 和 函数调用的 元素数量 相同. 

## Ruby

{%example src/main.rb%}

Ruby调用需要 比 以前的示例 更多. 

这一次,我们使用[`MemoryPointer`][memorypointer] 分配空间来存储我们的整数. 创建后,我们使用将值复制到`write_array_of_uint32`其中.

[memorypointer]: https://github.com/ffi/ffi/wiki/Pointers#memorypointer

## Python

{%example src/main.py%}

Python调用需要 比 以前的示例 更多的工作. 

这次,我们创建一个新类型来存储 我们的整数 并实例化类型. 

## Haskell

{%example src/main.hs%}

对于这个例子,我们可以使用`withArrayLen`函数,将内容为`Storable` (即可序列化为 C可以理解的字节序列) 的Haskell数组 和 生成这些值的打包数组,然后将 其与数组的长度一起传递给 回调函数. 在这种情况下,它将 数组的长度作为`Int`类型传递,之后我们转换成 预期的`CUInt`通过`fromIntegral`函数. 

## Node.js

{%example src/main.js%}

我们需要使用[`ref`][ref]和[`ref-array`][ref-array]用于将 node.js内存缓冲区 包装成 类似于数组对象,这些对象可以通过 JavaScript轻松操作. 该`u32array`类型 (使用来自`ref.types`的原始构造) 然后可以在函数签名中使用. 

[ref]: https://www.npmjs.com/package/ref

[ref-array]: https://www.npmjs.com/package/ref-array

## C\#

{%example src/main.cs%}

传递数组很复杂,因为我们需要传递 指向数据的指针 以及 数组的长度. 与前面的例子不同,我们引入了非惯用语`snake_case`作为私人方法. 然后我们可以添加一个公共方法,它包装 私有方法 并提供 预期的接口. 

C代码使用了`size_t`,一种大小 根据平台而变化 的类型. 为了反映这一点,我们使用了一个`UIntPtr`. 

## Julia

{% example src/main.jl %}

传递需要指针和数组的长度的数组。
该数组被隐式转换为指向第一个元素的指针，
类型为`Ptr{UInt32}`。`Csize`类型是Julia的对`size_t`的等价物
。我们还强制执行函数参数类型
`Array{UInt32}`，这是原生函数有效兼容的特定数组类型。

这个例子有点复杂，因为在Julia中有两个
的原始指针类型。 [`Ptr{T}`](julia-Ptr)是一个原始
内存地址为`T`类型的值，而[`Ref{T}`](julia-Ref)
是一个托管指针，指向Julia垃圾收集器不会放手的数据
只要这个`Ref`还引用。这些类型将是
在任何一种情况下都转换为`ccall`中的C兼容指针类型。

虽然, 在传递指向原生函数的参数时，[首选][julia-refptr]使用`Ref`
，甚至可
通过可变指针传递它们(可让原生函数
修改指向的对象），C风格的数组是一个例外
规则，应该用原始的'Ptr`和其长度传递。

[julia-Ptr]: https://docs.julialang.org/en/v1/base/c/#Core.Ptr
[julia-Ref]: https://docs.julialang.org/en/v1/base/c/#Core.Ref
[julia-refptr]: https://docs.julialang.org/en/v1/manual/calling-c-and-fortran-code/#When-to-use-T,-Ptr{T}-and-Ref{T}-1
