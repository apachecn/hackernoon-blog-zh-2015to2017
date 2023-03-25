# C++ 17 之旅:If Constexpr

> 原文：<https://medium.com/hackernoon/a-tour-of-c-17-if-constexpr-3ea62f62ff65>

我们兴奋地看到 **if-constexpr** 已经做成C++ 17 了！在这篇文章中，我们将看看一些 C++ 14 代码，并展示如何使用这种新的语言特性使它更简单、更简洁。

![](img/ba483abc4d33ce105b662aa7b8b02859.png)

# 介绍

在深入研究 **if-constexpr** 之前，快速回顾一下`constexpr`可能是有用的。在 C++ 11 中引入，`constexpr`是一个关键字，它将表达式或函数标记为具有*编译时常量结果*。

当然，这将被编译器优化掉:

好的，所以你可能想知道`constexpr`的目的是什么。毕竟，编译器不是足够聪明来优化这样的函数吗，即使它们没有被标记为`constexpr`？

`constexpr`的真正价值是作为*保证*该函数在编译时是可计算的。这可以防止令人讨厌的副作用随着代码的发展潜入代码中，并且允许编译器做一些聪明的事情:

*   与模板和预处理器宏不同，`constexpr`允许编译时的循环和递归，而没有极端的样板文件。
*   函数可以作为常规函数使用，尽管在内部它们有更大的限制。
*   随着需求的变化，函数可以很容易地转换成常规函数。
*   `constexpr`函数的编译速度比同等的基于模板的解决方案快得多，后者与模板递归的深度成线性比例。

## 编译时斐波那契

例如，看看斐波那契数列的这两个编译时实现:

Compile-time Fibonacci Using Templates

Compile-time Fibonacci Using Constexpr

## 那么什么是 **if-constexpr** ？

简而言之， **if-constexpr** 扩展了 C++语言的编译时子集，以包含 if 语句。更何况如果 **if-constexpr** 的一个分支没有被命中，那么它甚至不会被编译。

有了 if-constexpr 供您使用，您不需要求助于复杂的元编程技术，如模板模式匹配和 SFINAE。

让我们看一些例子。

# 示例 1 —获取第 n 个参数

许多模板元程序在可变类型列表上操作。在 C++ 14 中，获取第 n 种类型的参数列表通常使用复杂的模板来实现:

C++ 17 让这变得更加直观:

# 示例 2——API 垫片

有时你想支持一个替代的 API。C++ 14 提供了一种简单的方法来检查一个对象是否可以以某种方式使用:

然后，在 C++ 14 中实现自定义行为可以这样完成:

等效的 C++17 要简单得多:

这非常方便，因为语义上属于一起的代码不会分散在多个函数中。此外，您甚至可以定义包含 **if-constexpr** 的 lambdas。

# 示例 3 —编译时算法选择

通常，您需要根据类型的属性找到最佳算法。有很多解决方法。例如，STL 使用“类型标签”为一些给定的迭代器选择正确的算法:

然而，一旦您有了更复杂的规则，您可能需要一个更强大的解决方案— [SFINAE](http://en.cppreference.com/w/cpp/language/sfinae) :

C++ 14:

使用C++ 17，你可以用更少的样板文件和更清晰的方式描述这些规则:

这非常实用，因为使用 if 语句比使用各种语言特性更直观。

重构元函数变得和普通代码一样简单。有了 **if-constexpr** ，担心不明确的重载和其他意想不到的复杂情况将成为过去。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)