---
layout: default
examples: ../examples/slice_arguments
title: 切片 参数
---

# Rust 函数：切片参数

Rust _切片_ 将**指针的概念**，与**一块定量元素的数据**捆绑在一起。在 C 中，数组由相同的部分组成，但没有标准容器将它们维系在一起。

{%example src/lib.rs%}

转换一个数组分为两步:

1.  确保 C 指针不是`NULL`，因 Rust 引用(可能)永远不会`NULL`。

2.  使用[`from_raw_parts`][from_raw_parts]将指针和长度，转换为一个切片。这是一种不安全的操作，因为可能会，**解引用**了无效内存。

[from_raw_parts]: http://doc.rust-lang.org/std/slice/fn.from_raw_parts.html

## C

{%example src/main.c%}

C 的调用是直截了当的，因为我们使 Rust 代码与 C 的能力相匹配。唯一的复杂因素是确保定义的，和函数调用的元素数量相同。

## Ruby

{%example src/main.rb%}

Ruby 的调用比起以前的示例，需要更多工作。这一次,我们用[`MemoryPointer`][memorypointer] 来分配空间，来存储我们的整数. 创建后,我们使用`write_array_of_uint32`，将值复制进去。

[memorypointer]: https://github.com/ffi/ffi/wiki/Pointers#memorypointer

## Python

{%example src/main.py%}

Python 的调用比起以前的示例，需要更多工作。
这次,我们创建一个新类型来存储我们的整数，并用值(`*numbers`)，实例化这个类型。

## Haskell

{%example src/main.hs%}

对于这个例子，我们可以使用`withArrayLen`函数，它会拿到内容为`Storable` (即可序列化为 C 可以理解的字节序列) 的 Haskell 数组，并生成这些值的打包数组，然后将其与数组的长度，一起传递给回调函数。 在这种情况下，要将数组的长度作为`Int`类型传递，这里，我们会通过`fromIntegral`函数，转换成预期的`CUInt`。

## Node.js

{%example src/main.js%}

我们需要使用[`ref`][ref]和[`ref-array`][ref-array]，用于将 node.js 内存缓冲（buffer），包装成类似数组的对象，这些对象可以通过 JavaScript 轻松操作。该`u32array`类型 (来自`ref.types`的原始构造) 可以之后，在函数签名中使用。

[ref]: https://www.npmjs.com/package/ref
[ref-array]: https://www.npmjs.com/package/ref-array

## C\

{%example src/main.cs%}

传递数组很复杂，因为我们需要传递一个指向数据的指针 以及 数据数组的长度。与前面的例子不同，我们引入了`sum_of_even`函数(不是约定的编写风格)，作为私人方法. 然后我们可以添加一个公共方法，它包装私有方法，并提供预期的接口。

C 代码使用了`size_t`，一种大小根据平台而变化的类型。为了仿造这一点，我们使用了一个`UIntPtr`。

## Julia

{% example src/main.jl %}

传递数组，需要一个指针和数组的长度。该数组被隐式转换为，指向第一个元素的指针，类型为`Ptr{UInt32}`。`Csize`类型是 Julia 的`size_t`等价物。我们还强制让函数参数类型为`Array{UInt32}`，这是原生函数有效兼容的特定数组类型。

这个例子有点复杂，因为在 Julia 中，有两个的原始指针类型。 [`Ptr{T}`](julia-Ptr)是一个`T`类型值的普通内存地址，而[`Ref{T}`](julia-Ref)
是一个托管指针，指向 Julia 垃圾收集器不会放手的数据，只要这个`Ref`还引用。任何情况下，这些类型会在`ccall`，转换为 C 兼容指针类型。

虽然, 在传递指向原生函数的参数时，[首选][julia-refptr]`Ref`
，甚至可通过可变指针传递它们(可让原生函数，修改指向的对象），C 风格的数组是一个例外规则，应该用普通的`Ptr`与长度传递。

[julia-ptr]: https://docs.julialang.org/en/v1/base/c/#Core.Ptr
[julia-ref]: https://docs.julialang.org/en/v1/base/c/#Core.Ref
[julia-refptr]: https://docs.julialang.org/en/v1/manual/calling-c-and-fortran-code/#When-to-use-T,-Ptr{T}-and-Ref{T}-1
