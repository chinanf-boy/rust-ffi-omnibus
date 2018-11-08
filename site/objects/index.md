---
layout: default
examples: ../examples/objects
title: 对象
---

# 其他语言使用Rust对象

让我们创建一个Rust对象,告诉我们 每个美国邮政编码 中有多少人. 我们希望能够在其他语言中使用 此逻辑,但我们只需要 在FFI边界上 传递 整数或字符串 等简单原语. 该对象将具有 可变和不可变的方法. 因为我们无法查看对象内部,所以这通常被称为*不透明的物体*或者*不透明的指针*. 

{%example src/lib.rs%}

该`struct`以Rust正常的方式定义的. 一`extern`为 对象的每个函数 创建函数. C 没有 内置的 命名空间概念,因此 为每个函数添加 包名或类型名称 是正常的. 对于这个例子,我们使用`zip_code_database`. 遵循正常的 C 规范,始终将 指向对象的指针 作为 第一个参数. 

要创建一个新的对象实例,我们 `box`了 对象的构造函数. 这会将 结构 放在堆上,它将具有稳定的内存地址. 使用[`Box::into_raw`][into_raw] 将 该地址转换为 原始指针. 

该指针指向 Rust分配的内存; Rust分配的内存**必须**由Rust解除分配. 我们用[`Box::from_raw`][from_raw]将 指针 转回`Box<ZipCodeDatabase>`当释放对象时. 与其他功能不同,我们*能*允许`NULL`通过,但在这种情况下 根本不做任何事. 这对 客户程序员来说 非常好. 

要从 原始指针 创建 引用,可以使用简洁语法`&*`,表示应该 取消引用指针 然后重新引用. 创建 可变引用 类似,但使用`&mut *`. 像其他指针一样,您必须确保指针不是`NULL`. 

注意一个`*const T`可由`*mut T`转换,并且没有什么能阻止 客户端代码 永远不会调用 释放函数,也不会多次调用它. 内存管理和安全保障 完全掌握在程序员手中. 

[into_raw]: https://doc.rust-lang.org/std/boxed/struct.Box.html#method.into_raw

[from_raw]: https://doc.rust-lang.org/std/boxed/struct.Box.html#method.from_raw

## C

{%example src/main.c%}

创建一个虚拟结构,以提供少量的类型安全性. 

该`const`修饰符适用于适当的函数,甚至
虽然 const-correctness 在 C 中 比 在Rust中 更具流畅

## Ruby

{%example src/main.rb%}

为了包装原始函数,我们创建了一个继承自[`AutoPointer`][autopointer]的小类. `AutoPointer`将确保在释放对象时,释放底层资源. 为此,用户必须定义`self.release`方法. 

不幸的是,因为我们继承了`AutoPointer`,我们无法重新定义 初始化程序. 为了更好地将 概念组合 在一起,我们将 FFI方法 绑定 在嵌套模块中. 我们为绑定方法提供了 更短的名称,使客户端可以只调用`ZipCodeDatabase::Binding.new`. 

[autopointer]: http://www.rubydoc.info/github/ffi/ffi/FFI/AutoPointer

## Python

{%example src/main.py%}

我们创建一个空结构 来表示我们的类型. 这只会是
与`POINTER`方法结合使用，创建一个新
类型 作为指向现有指针的指针 

为确保正确清理内存,我们使用了*上下文经理*. 这通过我们的 class 绑定`__enter__`和`__exit__`方法. 我们使用`with`声明开始新的上下文. 当上下文结束时,`__exit__`方法将自动调用,防止内存泄漏. 

## Haskell

{%example src/main.hs%}

我们首先定义一个空类型 来引用 不透明对象. 在定义导入的函数时,我们使用`Ptr`的类型构造 新类型, 将作为从Rust返回的 指针的类型. 我们也用`IO`因为 分配,释放和填充对象 都是具有副作用的函数. 

由于分配理论上 可能会失败,我们会检查`NULL`并从构造函数返回一个`Maybe`. 这可能有点过头了,因为当前分配器失败时,Rust会中止该过程. 

为了确保自动释放 分配的内存,我们使用`ForeignPtr`类型. 这需要一个原始的`Ptr`以及 在解除分配包装指针时 调用的函数. 

使用包装指针时,`withForeignPtr`用于 展开 包装指针 在传递回FFI函数之前. 

## Node.js

{%example src/main.js%}

导入函数时,我们只需声明一个`pointer`返回或接受类型. 

为了使访问函数更清晰,我们创建了 一个简单的类 来 维护 我们的指针,并 参数 传递给更低级的函数. 这也让我们有机会用惯用的 JavaScript驼峰大小写 来重命名这些函数. 

为了确保清理资源,我们使用了`try`阻止,并在`finally`调用 释放方法. 

## C\#

{%example src/main.cs%}

由于调用 原生函数的能力 将更加分散,我们创建了一个`Native`用于保存所有定义的类. 

为了确保自动释放分配的内存,我们创建了一个[`SafeHandle`][safehandle]类的子类. 这需要 实现 `IsInvalid`和`ReleaseHandle`. 由于我们的Rust函数接受释放一个`NULL`指针,我们可以说 每个指针都是有效的. 

我们可以使用我们的 安全包装 `ZipCodeDatabaseHandle`作为FFI类型的函数,除了 释放函数. 这些 来自包装器的实际指针将自动 编组到 包装器, . 

我们也允许`ZipCodeDatabase`参与`IDisposable`协议,转发到 安全包装器. 

[safehandle]: https://msdn.microsoft.com/en-us/library/system.runtime.interopservices.safehandle(v=vs.110).aspx

## Julia

{% example src/main.jl %}

与其他语言一样，我们将控制指针隐藏在新数据类型后面。 填充数据库的方法称为`populate!`，遵循Julia修改值方法的惯例，会加上`!`的后缀。

目前尚未就Julia应如何处理原生资源达成共识。 虽然用于分配`ZipCodeDatabase`的内部构造函数模式，在这里是合适的，但我们也可以想到许多方法让Julia随后释放它。在这个例子中，我们展示了释放对象的两种方法：

- (1)用`do`语法的map构造函数，以及
- (2)用于手动释放对象的close重载。

内部构造函数`ZipCodeDatabase(f)`负责创建和释放对象。 使用do语法，用户代码变得类似使用Python语法`with` 。或者，程序员可以使用其他构造，并在不再需要时，调用`close`方法。
