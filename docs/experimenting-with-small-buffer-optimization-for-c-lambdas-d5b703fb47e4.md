# C++ Lambdas 的小缓冲区优化实验

> 原文：<https://medium.com/hackernoon/experimenting-with-small-buffer-optimization-for-c-lambdas-d5b703fb47e4>

# TL；速度三角形定位法(dead reckoning)

我们实现了`SmallFun`，它是`std::function`的替代方案，实现了*固定大小的捕获优化*(一种小缓冲区优化的形式)。虽然`SmallFun`比`std::function`稍逊一筹，但在一些基准测试中，它比快**3-5 倍。**

你可以[在 GitHub](https://github.com/LoopPerfect/smallfunction) 上查看代码。

![](img/e395fa0c102f946ca69d6ba89e72098d.png)

Photo by [Pascal Richier](https://unsplash.com/photos/ECju13NcBzg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 背景

`std::function`是一种用闭包存储 lambdas(也称为捕获)的便捷方式，同时提供了一个统一的接口。如果你来自面向对象的世界，那么将它们理解为[策略模式](https://en.wikipedia.org/wiki/Strategy_pattern)的概括可能会有所帮助。

在`std::function`和 lambdas 之前，我们将创建一个手工制作的仿函数对象，如下所示:

这个库比较了`std::function`，手工制作的`Functor`和`SmallFun`。我们发现`SmallFun`比`std::function`表现得更好，因为它不那么通用。

# std::函数错过的机会

`std::function`使用 [PImpl 模式](http://en.cppreference.com/w/cpp/language/pimpl)为给定签名的所有仿函数提供统一接口。

例如，这两个实例`f`和`g`具有相同的大小，尽管它们具有不同的捕获:

这是因为`std::function`将捕获存储在*堆*中。这统一了所有实例的大小，但也是优化的机会！

# 怎么会？

我们可以将函数对象(包括它的虚拟表)放入*栈*中预先分配的位置，而不是在*堆*上动态分配内存。

这就是我们如何实现`SmallFun`，它的用法很像`std::function`:

# 基准

# 测试

为了测试我们分配和调用函子的速度，我们将把所有实例保存在一个 vector 中，并在一个循环中执行它们。结果被保存到另一个向量中，以确保优化器不会优化掉我们正在测试的内容。

# SmallFun 实现细节

要实现`SmallFun`，需要结合三种 C++模式: [type-erasure](https://akrzemi1.wordpress.com/2013/11/18/type-erasure-part-i/) 、 [PImpl](http://en.cppreference.com/w/cpp/language/pimpl) 和 [placement-new](https://stackoverflow.com/a/222578/1256041) 。

## 类型擦除

类型擦除将许多实现统一到一个接口中。在我们的例子中，每个 lambda(或 functor)都有一个自定义的调用操作符和析构函数。我们需要为 API 消费者将要使用的任何类型自动生成一个实现。

这将是我们的公共接口:

对于任何具有给定签名的可调用类型:

现在我们可以这样使用它:

这相当麻烦而且容易出错。下一步将是容器。

## PImpl

PImpl 分离、隐藏、管理实际实现的生命周期，并公开有限的公共 API。

一个简单的实现如下所示:

这或多或少就是`std::function`的实现方式。

那么我们如何移除堆分配呢？

## 位置-新建

Placement-new 在给定的地址分配内存。例如:

## 把所有的放在一起

现在，我们只需要做一些小的更改来删除堆分配:

正如你可能注意到的，如果`Model<...>`的尺寸大于`SIZE`，就会发生不好的事情！一个断言只会在运行时捕捉到这一点，但为时已晚……幸运的是，这可以在编译时使用`enable_if_t`捕捉到。

但是首先，复制构造函数怎么样？

## 复制构造函数

与`std::function`的实现不同，我们不能只是复制或移动一个`std::shared_ptr`。我们也不能只按位复制内存，因为 lambda 可能管理一个由于副作用而只能释放一次的资源。因此，我们需要让模型能够为给定的内存位置复制构造自己。

我们只需要补充一点:

# 进一步说明

*   正如我们看到的，我们可以在编译时验证一个 Lambda 是否适合我们的内存。如果没有，我们可以提供一个回退到堆分配的方法。
*   一个更通用的`SmallFun`实现需要一个通用分配器。
*   我们注意到我们不能仅仅通过逐位复制内存来复制内存。然而，使用类型特征，我们可以检查底层数据类型是否是 POD，然后按位复制。

# 既然你在这里…

我们创建了 [Buckaroo](https://github.com/LoopPerfect/buckaroo) 来使集成 C++库变得更容易。如果你想尝试一下，最好从[文档](http://buckaroo.readthedocs.io/en/latest/)开始。你可以在 [Buckaroo.pm](https://buckaroo.pm/) 上浏览现有套餐，或者在[愿望清单](https://github.com/LoopPerfect/buckaroo-wishlist)上申请更多套餐。

[](https://hackernoon.com/approaches-to-c-dependency-management-or-why-we-built-buckaroo-26049d4646e7) [## C++依赖管理的方法，或者我们为什么要建立 Buckaroo

### C++是一种不寻常的语言，因为它还没有一个占主导地位的包管理器(我们正在努力！).结果是…

hackernoon.com](https://hackernoon.com/approaches-to-c-dependency-management-or-why-we-built-buckaroo-26049d4646e7) [](https://hackernoon.com/error-handling-in-c-or-why-you-should-use-eithers-in-favor-of-exceptions-and-error-codes-f0640912eb45) [## C++中的错误处理或:为什么你应该使用这两者之一来支持异常和错误代码

### TL；速度三角形定位法(dead reckoning)

hackernoon.com](https://hackernoon.com/error-handling-in-c-or-why-you-should-use-eithers-in-favor-of-exceptions-and-error-codes-f0640912eb45)