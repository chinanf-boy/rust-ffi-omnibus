---
layout: default
examples: ../examples/integers/
title: 整数
---

## 传递和返回整数

整数是 FFI 的 "你好世界！"，因为它们通常更容易越过柏林墙（边界）。让我们创建一个库，是关于
两个无符号的 32 位整数.

{% example src/lib.rs %}

用`cargo build`编译它，这将产生一个库在
`target/debug/`。确切的文件名取决于您的平台：

| 平台    | 模式         |
| ------- | ------------ |
| Windows | `*.dll`      |
| OS X    | `lib*.dylib` |
| Linux   | `lib*.so`    |

## C

{%example src/main.c%}

我们首先使用 正确的参数和返回类型，声明一个`extern`函数
。然后可以编译并链接到 Rust 库，通过使用`gcc --std=c11 -o c-example src/main.c -L target/debug/-lintegers`。

如 基本部分 所述，这可以在 mac OS X 和 Linux 上 运行
使用 `LD_LIBRARY_PATH=target/debug/ ./c-example`，而在 Windows 上将`target\debug\integers.dll`复制到当前目录和
运行`.\c-example`。

## Ruby

{%example src/main.rb%}

这可以使用

`LD_LIBRARY_PATH=target/debug/ ruby ./src/main.rb`

## Python

{%example src/main.py%}

如基本部分所述，这可以在 mac OS X 和 Linux 上运行
使用`LD_LIBRARY_PATH=target/debug/python src/main.py`，然后打开
Windows 通过将`target\debug\integers.dll`复制到当前
目录并运行`py src\main.py`.

## Haskell

{%example src/main.hs%}

我们必须启用`ForeignFunctionInterface`语言扩展和
在包含一个`foreign import`声明之前，导入相关的低级类型
。这包括调用约定
（`ccall`），符号名称（`"addition"`），相应的 Haskell
名字(`addition`），和函数的类型。这个函数是
纯的，所以我们不用在类型中包含`IO`，而一个显式不纯的函数，会想要返回一个`IO`值，用来表明它有副作用。

这可以使用`ghc src/main.hs target/debug/libintegers.so -o haskell-example`编译。

## Node.js

{%example src/main.js%}

`Library`函数指定要链接的动态库的名称，
以及导出函数的签名列表（以
`function_name：[return_type，[argument_types]]`的形式）。然后是这些函数方法会在，`Library`返回对象中。

这可以使用`LD_LIBRARY_PATH=target/debug node src/main.js`
运行.

## C\

{%example src/main.cs%}

我们使用 Platform Invoke 功能来访问动态库中的函数
。`DllImport`属性列出了该库名称，和可以找到的函数名。这些函数是可用作类的静态方法。坚持 C#命名标准，我们使用`EntryPoint`属性给暴露的函数，用上大写的名称。

这可以用`mcs -out：csharp-example src/main.cs`编译和用`LD_LIBRARY_PATH=target/debug mono csharp-example`执行。

## Julia

{% example src/main.jl %}

使用原语 [`ccall`][ccall] 调用外部函数。如果早只知道函数名, 我们也可以跳过，`dlsym`获取函数指针的步骤, 改为传递一个
`(func, lib)` 字面量元组:

```julia
addition(a, b) = ccall(
    (:addition, "libintegers"), # ← must be a constant expression!
    UInt32,
    (UInt32, UInt32),
    a, b)
```

如基本部分所述，这可以在 macOS 和 Linux 上运行
，使用 `LD_LIBRARY_PATH=target/debug/ julia src/main.jl`，和在 Windows 通过将`target\debug\integers.dll` 复制到当前
目录并运行`julia src\main.jl`。

[ccall]: https://docs.julialang.org/en/v1/base/c/#ccall
