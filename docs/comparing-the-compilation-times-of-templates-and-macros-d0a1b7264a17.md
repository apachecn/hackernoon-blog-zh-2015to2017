# 比较 C++模板和宏的编译时间

> 原文：<https://medium.com/hackernoon/comparing-the-compilation-times-of-templates-and-macros-d0a1b7264a17>

# TL；速度三角形定位法(dead reckoning)

对于我们的例子，模板比生成的代码编译得更快。通过一些技巧，它们对于增量构建也更快。

![](img/e9528ba71b393fec00b452ced1c201c2.png)

# 介绍

模板是 C++的一个极端特征。一方面，人们喜欢挤出额外的性能并给他们的项目添加语法糖的能力。另一方面，一些人认为它们编译缓慢，二进制文件膨胀，给出难以理解的错误信息。

本文将着眼于模板的一个常见用例，并确定使用代码生成而不是模板是否可以提高编译时间。

# 简单向量类

我们将看一个简单的 vector 类，它的大小是在编译时确定的。该类的用法可能如下所示:

实现非常简单:我们有一个常规的 C++ `struct`,其中一个类型参数表示向量的长度:

然而，我们也可以使用预处理器来实现这个类！

这两种实现为我们提供了等效的功能。然而，对于给定的大小`N`，第一个使用模板生成一个 vector 类，第二个使用宏。

**问题是:哪个编译得更快？**

为了比较这两种方法，我们编写了一小段代码，使用长度从 0 到 256 的向量类。我们还测试了另一种方法，我们采用了宏实现，但是在测试之前运行了预处理器。这就相当于每节课都是手写的！

我们将每个版本编译了 100 次，并测量了花费的时间。结果如下:

([source-code](https://github.com/nikhedonia/template-instatiation-benchmark))

## 结论

结果表明，对于这个例子，*编译模板比等效的宏版本*更快！最重要的是，模板更容易维护，因为代码不会重复，编译器可以给出更好的错误消息。

如果你考虑模板是如何工作的，那么这是很有意义的。实例化一个模板只是用具体的值或类型替换它的模板参数。对于代码生成，我们必须解析 C++并为每个宏结果从头开始构建 AST。

**但是增量构建呢？**

这个测试忽略了增量构建。使用代码生成的一个好处(据说！)的一个优点是，每个 vector 类的实现都可以放在自己的翻译单元中，这意味着每次使用它时都不必重新编译。

但是，我们可以使用模板达到同样的效果！C++ 11 引入了`extern template`结构，它告诉编译器模板是在另一个翻译单元中编译的:

`extern template`类似于模板的前向声明。

我们可以使用这种结构将最常见的模板实例化放到它们自己的翻译单元中，从而显著减少增量构建时间。

vector.cpp

我们`extern template`在 vector 头中的常见情况，以防止消费者编译他们自己的版本。

vector.hpp

然后我们运行增量构建(`vector.cpp`已经编译)。结果不言自明:

下一次当你试图提高你的增量构建时间的时候，考虑向前声明你的模板！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)