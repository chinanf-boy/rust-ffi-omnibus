---
layout: default
examples: ../examples/integers/
title: 整数
---

## 传递和返回整数

整数是 FFI 的 "你好世界！" ，因为它们通常
更容易越过边界.让我们创建一个库关于
两个无符号的 32 位整数.

{% example src/lib.rs %}

用`cargo build`编译它，这将产生一个库在
`target/debug/`.确切的文件名取决于您的平台：

| 平台    | 模式         |
| ------- | ------------ |
| Windows | `*.dll`      |
| OS X    | `lib*.dylib` |
| Linux   | `lib*.so`    |

## C

{%example src/main.c%}

我们首先使用 正确的参数 声明一个`extern`函数
和返回类型.然后可以 编译并链接到
Rust 库 使用`gcc --std=c11 -o c-example src/main.c -L target/debug/-lintegers`.

如 基本部分 所述，这可以在 mac OS X 和 Linux 上 运行
使用

`LD_LIBRARY_PATH=target/debug/ ./c-example`

，而在 Windows 上
将`target\debug\integers.dll`复制到当前目录和
运行`.\c-example`.

## Ruby

{%example src/main.rb%}

这可以使用

`LD_LIBRARY_PATH=target/debug/ ruby ./src/main.rb`

## Python

{%example src/main.py%}

如基本部分所述，这可以在 mac OS X 和 Linux 上运行
使用
`LD_LIBRARY_PATH=target/debug/python src/main.py`，然后打开
Windows 通过将`target\debug\integers.dll`复制到当前
目录并运行`py src\main.py`.

## Haskell

{%example src/main.hs%}

我们必须启用`ForeignFunctionInterface`语言扩展 和
在包含 之前导入相关的低级类型
`foreign import`声明.这包括调用约定
（`ccall`），符号名称（`"addition"`），相应的 Haskell
(`addition`）名字和函数的类型.这个功能是
实际上是纯粹的，所以我们不在类型中包含`IO`，而是一个
显然不纯的函数会想要返回一个`IO`值
这表明它有副作用.

这可以使用编译
`ghc src/main.hs target/debug/libintegers.so -o haskell-example`.

## Node.js

{%example src/main.js%}

`Library`函数指定要 链接的动态库的名称，
以及 导出函数的签名列表（以
`function_name：[return_type，[argument_types]]`的形式）.然后是这些函数
可用作`Library`返回的对象的方法.
这可以使用

`LD_LIBRARY_PATH=target/debug node src/main.js`
运行.

## C\

{%example src/main.cs%}

我们使用 Platform Invoke 功能来访问动态库中的函数
. `DllImport`属性列出了该名称和
可以找到该函数的库.这些函数是
可用作类的静态方法.坚持 C#命名
标准，我们使用`EntryPoint`属性 来使用 大写的名称
对于暴露的功能.

这可以用`mcs -out：csharp-example src/main.cs`编译

和用`LD_LIBRARY_PATH=target/debug mono csharp-example`执行.

## Julia

{% example src/main.jl %}

使用原生 [`ccall`][ccall] 调用外部函数. 如果提前只知道函数名, 我们也可以跳过通过`dlsym`获取函数指针的步骤, 改为传递一个
`(func, lib)` literal 元祖:

```julia
addition(a, b) = ccall(
    (:addition, "libintegers"), # ← must be a constant expression!
    UInt32,
    (UInt32, UInt32),
    a, b)
```

如基本部分所述，这可以在 macOS 和 Linux 上运行
，使用 `LD_LIBRARY_PATH=target/debug/ julia src/main.jl`,和在
Windows 通过将`target\debug\integers.dll` 复制到当前
目录并运行`julia src\main.jl`.

[ccall]: https://docs.julialang.org/en/v1/base/c/#ccall
