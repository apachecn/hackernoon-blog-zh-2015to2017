# value_ptr —丢失的 C++智能指针

> 原文：<https://medium.com/hackernoon/value-ptr-the-missing-c-smart-pointer-1f515664153e>

![](img/e0559b4c54a94713055167e5e6490ef1.png)

# TL；速度三角形定位法(dead reckoning)

使用`value_ptr`智能指针获取堆资源的值语义。以一些额外的复制为代价，你的代码会更简单，更容易推理。

选择这个备忘单使用的智能指针。

可以在 [GitHub](https://github.com/LoopPerfect/valuable) 上找到`value_ptr`的实现。

# 介绍

模板、lambdas 和词法范围的析构函数等语言特性使 C++程序员能够编写高阶容器来处理对象的生存期和副作用，并以一种可理解的方式在类型系统中记录它们。

有了智能指针，编码所有权语义和管理资源变得前所未有的简单。我们可以在标准库中找到最常见用例的智能指针，但是这些智能指针都没有提供值语义。本文将介绍`value_ptr`，以及一些激励人心的例子。

但是首先，让我们看看 C++ 11 标准库已经提供的功能:

## (哑)原始指针

是的，原始指针仍然可以使用，但是如果智能指针适用，您应该避免使用它们。这是因为原始指针不传达任何关于资源所有权模型的信息。

此外，分配和释放必须由程序员来管理，这可能会导致诸如双重删除或内存泄漏之类的错误。

看一下这段代码:

仅仅三行字，就有如此多的未解之谜:

*   `x`可以是`nullptr`吗，我必须检查吗？
*   `x`有管理吗？我能不能`delete x`？*一定要*我`delete x`？
*   如果我把`foo`前的`y`删除了，`foo`还有效吗？
*   如果`foo`被破坏了，`y`会被删除吗？

如果使用得当，智能指针可以让这些变得清晰。

## 唯一 _ptr

`unique_ptr`通过取得资源的唯一所有权并将其绑定到其词法范围来管理资源的生存期。虽然不能复制，但可以通过`std::move`转移所有权。

在以下情况下使用`unique_ptr`:

*   您希望将堆资源的生存期绑定到词法范围
*   您希望强制资源一次只有一个所有者

## 例子

## 共享 _ptr

通过计算管理下的引用数量，允许多个资源所有者。容器的复制构造函数递增计数器，并在销毁时递减计数器。如果计数器达到零，那么资源将被释放。

在以下情况下使用`shared_ptr`:

*   您希望在多个引用之间共享所有权
*   当资源不再被使用时，您希望自动处置它
*   你没有循环依赖
*   引用计数的开销是可以接受的

## 例子

## 弱 _ptr

我们看到我们可以对多个所有者使用`shared_ptr`，但是如果我们在所有权图中有循环会发生什么呢？在这种情况下，我们会泄漏内存，因为引用计数器永远不会达到零！循环中的主人会让彼此活下去。

这就是`weak_ptr`发挥作用的地方。`weak_ptr`类似于`shared_ptr`，但它不增加参考计数器。如果你用一个`weak_ptr`替换你的所有权图中的循环分支，那么引用计数将正确工作。

## 例子

在以下情况下使用`weak_ptr`:

*   你绝对必须有一个循环所有权图

# 介绍 value_ptr

值语义使您的代码更容易推理，因为与指针不同，所有权必须是严格的分层和排他的。

`value_ptr`允许我们在堆上的可复制资源上实施那些语义。

## 它是如何工作的

*   对堆上的资源拥有独占所有权。
*   当您将一个`value_ptr`分配给另一个`value_ptr`时，一个新的`value_ptr`对象被构建，该对象指向*它自己的前一个`value_ptr`资源的副本*。
*   当`value_ptr`离开其词法范围时，资源被销毁。
*   没有共享内存，所以`value_ptr`本质上是线程安全的。
*   现代的编译器足够聪明，可以删除大部分多余的副本。

## 示例 1 —递归数据类型

像树这样的递归类型必须通过 C++中的指针来实现，所以内存中的布局可以在编译时计算出来。然而，尽管使用了指针，我们可能仍然想要值语义的简单性:

## 示例 PImpl 模式

有时我们希望将类的接口与其实现分开。这可能是为了将代码隐藏在编译后的库后面，或者强制实施恒定的堆栈大小。

由于生存期是与所有者绑定的，所以智能指针在这里是合适的，但是我们想要什么语义呢？与`shared_ptr`和`unique_ptr`不同，`value_ptr`给了我们价值语义。

Header

Translation-unit

## 关于 PImpl 模式的更多资源

*   [PImpl，零规则和斯科特·迈耶斯](http://oliora.github.io/2015/12/29/pimpl-and-rule-of-zero.html)
*   [快速习语](http://www.gotw.ca/gotw/028.htm)
*   [PImpl 你的 C++代码](https://anteru.net/blog/2009/03/14/385/)

# 实施 value_ptr

您可能已经注意到`value_ptr`与`unique_ptr`相似，但是具有不同的复制构造器。虽然复制一个`unique_ptr`是被禁止的，但是复制一个`value_ptr`将会创建一个资源的副本。因此，我们可以通过利用`unique_ptr`和复制功能来实现`value_ptr`。

这就是我们所做的；[看看 GitHub](https://github.com/loopperfect/valuable) ，或者用 [Buckaroo](https://buckaroo.pm/) 试驾一下我们的实现:

```
buckaroo install loopperfect/valuable
```

# 摘要

值语义很容易推理，甚至对于堆对象也很有用。C++标准库没有提供带有值语义的智能指针，但是 C++有允许我们自己滚动的特性。

无法决定使用哪个智能指针？这里有一个快速图表:

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)