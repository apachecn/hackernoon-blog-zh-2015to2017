# C++中的错误处理或:为什么你应该使用这两者之一来支持异常和错误代码

> 原文：<https://medium.com/hackernoon/error-handling-in-c-or-why-you-should-use-eithers-in-favor-of-exceptions-and-error-codes-f0640912eb45>

# TL；速度三角形定位法(dead reckoning)

考虑使用任一类型来处理错误，因为它们将错误提升到类型系统中，并且具有与错误代码相同的性能特征。

## 任一实现

*   [loop perfect/never](https://github.com/loopperfect/neither)
*   [标准::预期](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4109.pdf)
*   [助推::预期](https://ned14.github.io/boost.outcome/md_doc_md_02-tutorial_a.html)
*   [beark/ftl](https://github.com/beark/ftl)

![](img/7557ee5345ea8aa7ba96b9fb3a2f6034.png)

# 介绍

编程语言设计总是一个权衡的问题。在 C++的情况下，设计者优化了两件事:*运行时效率*和*高级抽象*。这在许多方面给了 C++程序员巨大的灵活性，其中之一就是错误处理。

# 异常和尝试捕捉

传统上，Try-catch 被认为是 C++中最常见的错误处理方法。

Catching a divide-by-zero error

## 异常开销

try-catch 语言特性不是零成本的，确切的价格由编译器实现决定。在成功分支和失败分支中，实现者可以在增加代码大小和增加运行时开销之间进行选择。

在大多数 C++实现中，已经做出了一个有趣的选择:`try`块中的代码运行起来和其他任何代码一样快。然而，分派到`catch`块要慢几个数量级。这种损失随着调用堆栈的深度而线性增长。

异常对您的项目是否有意义将取决于抛出异常的频率。如果错误率超过 1%，那么开销可能会比其他方法大。([来源](https://youtu.be/XVofgKH-uu4?t=1h2m24s))

异常不是所有平台都支持的，`throw`不容易被 c 理解的方法

## 人类工程学

异常很容易使用，也很容易推理。您可以在代码的任何地方`throw`和`catch`异常，异常甚至可以是任意类型。

最大的缺点是处理异常不是由类型系统强制执行的。例如，与 Java 不同，Java 中的异常必须由调用者捕获，捕获 C++异常是可选的。这意味着在代码审查期间发现所有未处理的异常将是一项挑战，需要对所有调用的函数有深入的了解。

## 但是`noexcept` 和 throw 呢？

一个常见的误解是用`noexcept`或`throw`注释函数会有所帮助。

不幸的是，`noexcept`和`throw`只是规定在抛出一个未提及的异常的情况下调用`std::terminate`。*这不会在编译时强制执行任何异常处理。*

比如这些会编译抛出一个运行时错误！

noexcept will not save you!

# 错误代码

错误代码是古老的，到处都在使用。为了简单起见，让我们假设错误代码只是整数，但是它们可以被实现为类型安全的枚举或者甚至是复杂的对象。对于这次讨论来说，这并不重要。

有 3 种常见的错误代码实现形式。

## 1.作为返回值的错误代码

这种模式在许多 C APIs 中都可以找到，因为它很容易实现，而且除了错误处理本身之外，没有性能开销。

可以非常武断地遵循这种模式，并且很容易在代码审查中验证所有的情况都被处理了。使用错误代码编写一个 C 友好的 API 是很容易的。

不幸的是，它有一些缺点:

*   功能组合很难。返回值被错误代码占用，所以结果必须是 out 变量，这使得函数不纯。
*   Out 参数强制执行对优化器不友好的内存布局。
*   将错误处理从计算中分离出来是很困难的。
*   推迟错误处理需要程序员通过调用图来处理错误代码。

## 2.作为输出参数的错误代码

交换 out-parameter 和返回值的语义没有显著的优势，除了可能稍微干净一点的 API。在可以省略错误代码的情况下，API 的使用得到了简化，功能组合变得更加容易。

这种方法可以在`boost::asio`中找到(事实上`boost::asio`甚至让它成为可选的，如果没有提供 out-parameter，就退回到抛出异常)。

## 3.错误单例

错误单件有完全不同的人体工程学。它们大多出现在实现系统全局状态机的低级库中，比如驱动程序。一个突出的例子是 OpenGL。

使用错误单例如下所示:

在这个范例中，驱动程序的状态必须在运行时通过一个单独的函数来查询。这似乎给了您更多的自由，因为您可以在最合适的时候查询错误，使您能够更好地分离关注点。这允许用户编写类似于基于异常的代码，但没有自动堆栈展开的成本。

API 消费者的好处:

*   随着时间的推移，错误处理可以减少到最低限度
*   错误处理分支越少，性能越好
*   不需要外部参数，这增加了功能的组合性
*   发现错误时，可以手动执行终结

但是有一些重要的警告:

*   按照设计，单例具有共享状态，因此编写线程安全的代码非常困难
*   没有计算流水线的捷径，因为没有栈展开发生
*   不清楚哪些 api 调用会引发哪些错误。程序员必须检查文档。
*   错误的严重性以及如何从错误中恢复可能不清楚

# 那么其他人呢？

任一类型是接受两种不同类型之一的单个值的容器。一个简单的实现可能如下所示:

A simple Either type in C++

为了对包装后的值进行计算，an 可以提供一些有用的方法:`leftMap`、`rightMap`和`join`。

*   `leftMap`将`leftValue`转换为一个新值(如果存在)，保持右值不变。
*   `rightMap`将`rightValue`转换为一个新值(如果存在),左值保持不变。
*   `join`对任一方的两端进行转换，这两种转换产生相同的类型。这允许要么被统一和展开。

这在代码中更容易理解！

现在，我们能够将异常提升到类型系统中:

## 那么，通过这个简单的改变，我们获得了什么呢？

我们不再需要支付异常的开销，并且我们已经将异常类型编码到函数签名中。这记录了源代码中的错误，现在编译器将确保我们正确处理这些类型。

这是一件大事，它说明了 C++语言是多么强大。

## 那么有什么弊端呢？

首先，您需要向项目中添加一个任一类型。这里最好不要多此一举，幸好还有[很多](https://github.com/loopperfect/neither) [开源](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4109.pdf) [实现](https://ned14.github.io/boost.outcome/md_doc_md_02-tutorial_a.html) [可用](https://github.com/beark/ftl)。

但是性能呢？乍一看，似乎每次调用`leftMap`和`rightMap`都会给可执行文件增加一个分支。实际上，编译器足够聪明，可以将这些优化掉！

看看[这个编译器资源管理器项目](https://godbolt.org/g/5f6mT9)；各种映射调用的分支会显示出来。

例如，您可能已经注意到以下标识:

```
e.leftMap(f).leftMap(g) == e.leftmap([](auto x){ return g(f(x)); })
```

事实证明编译器也是如此。它组合两个 lambdas 来内联整个表达式。在优化步骤之后，所有的抽象都被折叠。一旦编译完成，错误代码实现和基于两者之一的实现之间就没有明显的区别了。

# 结论

考虑使用任一类型来处理错误。它们将错误提升到类型系统中，使它们比异常更安全，同时产生与错误代码相同的性能特征。

# 资源

## 任一实现

*   [完美循环/都不是](https://github.com/loopperfect/neither)
*   [std::预计](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4109.pdf)
*   [助推::预期](https://ned14.github.io/boost.outcome/md_doc_md_02-tutorial_a.html)
*   [beark/ftl](https://github.com/beark/ftl)

## 基准测试错误代码与异常或其他

[Mongrel Monads, Dirty, Dirty, Dirty — Niall Douglas [ACCU 2017]](https://youtu.be/XVofgKH-uu4?t=1h)

## 返回值与输出参数

[2013 Keynote: Chandler Carruth: Optimizing the Emergent Structures of C++](https://youtu.be/eR34r7HOU14?t=38m)

## 一些代码示例

*   gist:[https://gist . github . com/nikhedonia/db 401285d 9 f 3816 e 2 a 74d 78 c 68 DD 4c 6 c](https://gist.github.com/nikhedonia/db401285d9f3816e2a74d78c68dd4c6c)
*   组装:[https://godbolt.org/g/5f6mT9](https://godbolt.org/g/5f6mT9)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)