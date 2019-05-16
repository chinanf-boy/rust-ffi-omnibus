---
layout: default
examples: ../examples/integers/
title: 基本
---

# 基本

这里假设您熟悉 Rust 的基础知识 ，以及 您将要调用的语言。 你应该阅读下[官方 FFI 文件][official]，但这里会概括一些基础知识.

## Rust

所有 Rust 示例，都将使用 [cargo] 和[libc 箱子][libc]。 每个示例的`Cargo.toml`，都包含以下样板:

{%example Cargo.toml%}

`crate-type = ["cdylib"]`会创建一个动态链接的库。 可查看 Cargo 文档的[动态或静态库][dyn-stat]，了解更多信息.

`cdylib`是[在 RFC 1510 中引入][rfc1510]，并改善了现有的`dylib`文件，减小其大小，和导出更少符号。 它是[在 Rust 1.10 中实现][rust-1.10]的; 如果您使用的是早期版本，那鼓励您升级，但也可以继续使用`dylib`，带点小的不良影响而已。

一些示例非常小，且不会使用 Rust 标准库中的任何功能。 这有个[已知问题][rust-18807]和链接的失败问题。 唯一的解决方法是包含一个导出，但未使用的函数，该函数使用了标准库中的某些内容。这些函数被叫做`fix_linking_when_not_using_stdlib`，并在任何大型项目中，都可以安全地删除。

## C

所有 C 示例 都将使用 C11 标准 进行编译。

## Ruby

所有 Ruby 示例都将使用 Ruby 2.5 和 [FFI gem][gem]。

## Python

所有 Python 示例都将使用 Python 3.7 和[ctypes 库][ctypes]。

## Haskell

所有 Haskell 示例都将使用 GHC 8.4 的 `ForeignFunctionInterface`语言扩展，且只有`base`GHC 附带的库.

## Node.js

所有 Node.js 示例 都将使用 Node.js 8.12 和[ffi 包][node-ffi]。

## C\

所有 C#示例 都将使用 Mono 5.14 进行编译. 假设此代码将 与 Microsoft CLR 框架一起使用,但这是未经测试的.

## Julia

所有的例子使用 Julia 1.0, 并依赖语言的内置[C 函数调用功能][julia-c]。 也可能运行在 v0.7, 但并未测试.

## 运行示例

运行示例时，您需要确保系统可以找到 Rust 动态(链接)库。

对 mac OS X 和 Linux 上的大多数 shell，可以通过在命令前加上`LD_LIBRARY_PATH=target/debug`来完成。例如，要运行 Python 示例，您可以使用`LD_LIBRARY_PATH=target/debug python src/main.py`.

在 Windows 上，最简单的操作是在运行示例之前, 将 已编译的动态库，复制到当前工作目录中。 你只需要`.dll`文件。 另请注意，在运行 Python 示例时,您可能希望使用`py`代替`python`，特别是如果您安装了多个版本的 Python。

[official]: https://doc.rust-lang.org/book/ffi.html
[cargo]: https://crates.io/
[libc]: http://doc.rust-lang.org/libc/libc/index.html
[dyn-stat]: http://doc.crates.io/manifest.html#building-dynamic-or-static-libraries
[rfc1510]: https://github.com/rust-lang/rfcs/blob/master/text/1510-cdylib.md
[rust-1.10]: https://blog.rust-lang.org/2016/07/07/Rust-1.10.html
[rust-18807]: https://github.com/rust-lang/rust/issues/18807
[gem]: https://github.com/ffi/ffi
[ctypes]: https://docs.python.org/3/library/ctypes.html
[node-ffi]: https://www.npmjs.com/package/node-ffi
[julia-c]: https://docs.julialang.org/en/v1/manual/calling-c-and-fortran-code
