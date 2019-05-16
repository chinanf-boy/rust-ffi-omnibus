---
layout: default
examples: ../examples/objects
title: 对象
---

# 其他语言使用 Rust 对象

让我们创建一个 Rust 对象，用来告诉我们，每个美国邮政编码有多少人。我们希望能够在其他语言中使用此逻辑，但我们只需要 在 FFI 边界上，传递整数或字符串等简单原始类型。而该对象会具有可变和不可变的方法。因为我们无法查看对象内部，所以，这通常被称为*不透明的物体*或者*不透明的指针*。

{%example src/lib.rs%}

该`struct`以 Rust 正常的方式定义的。为对象的每个函数，创建一个`extern`函数. C 没有内置命名空间的概念，因此，为每个函数添加，一个包名或类型名称前缀，是很正常的。 对于这个例子，我们使用`zip_code_database`。遵循正常的 C 规范，始终将，指向对象的指针 作为 第一个参数。

要创建一个新的对象实例，我们 `box`化对象的构造函数。(`box`)会将结构放在堆上，这就有了一个稳定的内存地址。使用[`Box::into_raw`][into_raw] 将该地址转换为一个原始指针。

该指针指向 Rust 分配的内存; Rust 分配的内存**必须**由 Rust 解分配。当释放对象时，我们用[`Box::from_raw`][from_raw]将指针转回`Box<ZipCodeDatabase>`。与其他函数不同，我们*能*允许`NULL`的传递，但在这种情况下，只是不做事。这对客户端程序员来说，是好事。

要从一个原始指针，创建一个引用，可以使用简洁语法`&*`，表示，应解引用指针，然后重新引用。 创建一个可变引用是类似的，但使用`&mut *`。像其他指针一样，您必须确保指针不是`NULL`。

注意一个`*const T`可与`*mut T`，自由相互转换，并且即便永远不调用释放函数，或是多次调用，也阻止不了客户端代码。内存管理和安全保障，完全掌握在程序员手中。

[into_raw]: https://doc.rust-lang.org/std/boxed/struct.Box.html#method.into_raw
[from_raw]: https://doc.rust-lang.org/std/boxed/struct.Box.html#method.from_raw

## C

{%example src/main.c%}

创建一个虚拟结构，以提供少量的类型安全性。

该`const`修饰符给到了适当的函数，甚至即便，const-正确性 在 C 中 比 在 Rust 中，更不稳定。

## Ruby

{%example src/main.rb%}

为了包装原始函数，我们创建了一个继承自[`AutoPointer`][autopointer]的小类。`AutoPointer`将确保在释放对象时，释放底层资源。 为此，用户必须定义`self.release`方法。

不幸的是，因为我们继承了`AutoPointer`，我们无法重新定义初始化程序。为了更好地将概念，组合 一起，我们将 FFI 方法 绑定在嵌套模块中。我们为绑定方法提供了 更短的名称，使客户端可以只调用`ZipCodeDatabase::Binding.new`。

[autopointer]: http://www.rubydoc.info/github/ffi/ffi/FFI/AutoPointer

## Python

{%example src/main.py%}

我们创建一个空结构(structure)来表示我们的类型。这只会与`POINTER`方法结合使用，该方法会创建一个新类型，作为指向现有指针的指针。

为确保正确清理内存，我们使用了一个*context manager*。这通过`__enter__`和`__exit__`方法，绑住我们的 class。我们使用`with`声明，开始新的上下文。当上下文结束时，`__exit__`方法将自动调用，防止内存泄漏。

## Haskell

{%example src/main.hs%}

我们首先定义一个空类型，来引用不透明对象。在定义导入的函数时，我们使用`Ptr`的类型构造新类型， 将作为从 Rust 返回的指针的类型。我们也用到`IO`了，因为 分配，释放和 populating 对象，都是具有副作用的函数。

由于分配，理论上可能会失败，我们会检查`NULL`，并从构造函数返回一个`Maybe`。这可能有点过头了，因为其实当分配器失败时，Rust 会中止该过程。

为了确保自动释放，分配的内存，我们使用`ForeignPtr`类型。 这需要一个原始的`Ptr`，以及 在解分配包装指针时，调用的函数。

使用包装指针时，`withForeignPtr`用于在传递回 FFI 函数之前，展开包装指针。

## Node.js

{%example src/main.js%}

导入函数时，我们只需声明一个`pointer`，作为返回或接受的类型。

为了使访问函数更清晰，我们创建了一个简单的类，来维护我们的指针，并 抽象传递给更底层的函数。这也让我们有机会，用习惯的 JavaScript 驼峰大小写，来重命名这些函数。

为了确保清理资源，我们使用了`try`去看，并在`finally`调用释放方法。

## C\

{%example src/main.cs%}

由于调用原生函数的能力更加分散，因此我们创建了一个`Native`类，用于保存所有定义。

为了确保自动释放分配的内存，我们创建了一个[`SafeHandle`][safehandle]类的子类。这需要 实现 `IsInvalid`和`ReleaseHandle`。由于我们的 Rust 函数能释放一个`NULL`指针，我们可以说，每个指针都是有效的。

除了，释放函数，我们可以使用我们的安全包装器 `ZipCodeDatabaseHandle`，作为 FFI 函数的类型。这些实际指针将自动编排到包装器，反之亦然。

我们也允许`ZipCodeDatabase`参与`IDisposable`协议，转发到安全包装器。

[safehandle]: https://msdn.microsoft.com/en-us/library/system.runtime.interopservices.safehandle(v=vs.110).aspx

## Julia

{% example src/main.jl %}

与其他语言一样，我们将控制指针，隐藏在新数据类型后面。 人口数据库的方法，称为`populate!`，遵循 Julia 修改值方法的惯例，会加上`!`的后缀。

目前尚未就 Julia 应如何处理原生资源达成共识。虽然，分配`ZipCodeDatabase`的内部构造函数模式，在这里是合适的，但我们也可以想到许多方法，让 Julia 随后释放它。在这个例子中，我们展示了释放对象的两种方法：

- (1)`do`语法的一个映射构造函数，以及
- (2)用于手动释放对象的 `close` 重载。

内部构造函数`ZipCodeDatabase(f)`，同时负责创建和释放对象。 使用 `do` 语法，用户代码变得类似 Python 语法`with`。或者，程序员可以使用其他构造，并在不再需要时，调用`close`方法。
