# JavaScript 提升:快速指南

> 原文：<https://medium.com/hackernoon/hoisting-in-javascript-a-quick-guide-cc4d9597bbd7>

## 提升概念的初学者友好概述

**提升 [JavaScript](https://hackernoon.com/tagged/javascript) 中的**意味着**变量和函数声明被移动到其包含** [**作用域**](/@lenafaure/understand-javascript-variable-scope-with-ease-221a6d41dc43) 的顶部。

这在我的代码中是什么意思？

![](img/4837ad1bcf36b43aeca67b517922bff8.png)

[https://unsplash.com/](https://unsplash.com/)

# 声明在赋值之前

让我们考虑下面的代码片段来理解提升是如何工作的:

也许您希望 console.log 打印出`undefined`，因为`var`语句(声明)在`rockStar = “Mick Jagger”`(赋值)之后。在代码中创建之前，我们给`rockStar`变量*赋值。*

毕竟，合乎逻辑的想法是，JavaScript 程序中编写的所有代码行都是自顶向下读取、解释和执行的。这个假设是有道理的，但是我们刚刚展示的行为告诉我们事实并非如此。

实际上，就在代码被执行之前，它要经过一个编译器:在这个被称为*词法分析的阶段，* [作用域](/@lenafaure/understand-javascript-variable-scope-with-ease-221a6d41dc43)被定义，**变量和函数声明被移到它们作用域**的顶部。如果它们被定义在一个[函数](https://hackernoon.com/tagged/function)内，它们被移动到这个函数的顶部，如果它们在一个函数外，它们被移动到全局范围的顶部。

考虑另一段代码:

这一次，尽管我们声明并赋值了`rockStar`变量，但当我们试图打印变量时，却得到了`undefined`响应。这是为什么呢？

解决这个问题的最好方法是记住所有的声明都是首先处理的，对于变量和函数也是如此。

> ll 声明首先被处理，这同样适用于变量和函数。

让我们通过像 JavaScript 一样阅读代码来解码它的含义。

当您看到`var rockStar = “Mick Jagger”`时，您会将其视为一条语句，但 JavaScript 却不这么认为。它实际上将其解释为两种截然不同的陈述:

在我们的例子中，JavaScript 首先解释代码，发现`rockStar`变量存在，并将其移动到作用域的顶部:这就是所谓的**提升**。

但是第二个语句，即赋值语句，被**留在原地**。然后，JavaScript 执行代码，就像它是这样写的:

换句话说，**声明在赋值**之前:只有变量和函数声明被提升，而赋值则留在原处，就在代码中写它们的地方。

> 声明出现在赋值之前:只有变量和函数声明被提升，而赋值保留在代码中的位置。

# 函数声明会被提升，但函数表达式不会

我们已经看到了*变量*提升的例子，现在让我们关注*函数*的行为。

请参见下面这段代码:

根据我们现在对提升的理解，我们如何重写这段代码，就好像它是 JavaScript 在解释它一样？

首先记住**提升是基于作用域**的，这里`name`变量的作用域是函数`movieStar`内部的*局部作用域*。变量声明将被提升到函数的顶部，而不是全局范围。

然而，我们也在*全局作用域*中声明了的`movieStar`函数，对于变量和函数的声明，提升也是一样:我们的函数声明将被移到顶部，在函数的*执行*之前。

JavaScript 将对代码进行如下解释:

在这个例子中，`movieStar`函数调用有效，因为函数声明被提升，并被解释为在调用之前声明*。*

我们再举一个例子:

这里发生了什么？我赋给变量`movieStar`的函数，一个被称为*函数表达式*的进程，这次没有被挂起:当我调用该函数时，JavaScript 抛出一个 TypeError。

这是因为赋给变量的函数不会被提升。

> 赋给变量的函数不会被提升。

作为演示，如果我现在编写如下代码，它现在会打印出预期的结果:

# 变量前的函数

我们现在知道函数声明和变量声明都是被提升的。但是还有一点需要警惕:**先吊函数，再吊变量。**

> **先吊函数，再吊变量。**

考虑以下代码，其中变量和函数用相同的名称声明:

函数在变量之前被提升，所以当我打印`businessStar`的值时，它输出函数本身。

另一个给变量赋值的例子是:

这一次输出的是变量的值，因为变量被赋予了一个值，这个值覆盖了函数声明。如果你现在试图调用函数`businessStar()`，你将得到一个类型错误:这是因为`businessStar`已经被赋予了字符串“Gary Vaynerchuck”并且不再是一个函数。

# 结论

**提升是 Javascript 将声明移动到顶部的默认行为。**

变量和函数不是在声明后就可以使用，而是实际上在代码中就可以使用，这可能会导致某些行为，如果你对提升一无所知，你就不会想到这些行为。

这就是为什么作为一项规则，尽可能经常地确保您:

→ **在作用域顶部声明所有变量**(在全局作用域顶部或函数作用域顶部)

→如果可以的话，确保你把所有的函数都放在它们作用域的顶部。

想了解更多 JavaScript 基础知识吗？查看我的其他文章:

*   [**轻松理解 JavaScript 变量作用域**](/@lenafaure/understand-javascript-variable-scope-with-ease-221a6d41dc43)
*   [**把头缠在“这个”上**](/@lenafaure/get-your-head-around-this-73c23653b102)
*   [**如何自信地使用 JavaScript 闭包**](/@lenafaure/how-to-use-javascript-closures-with-confidence-85cd1f841a6b)
*   [**掌握 JavaScript**](https://hackernoon.com/grasp-by-value-and-by-reference-in-javascript-7ed75efa1293) 中的“按值”和“按引用”
*   [**JavaScript 中的日期快速手册**](https://hackernoon.com/a-quick-handbook-for-dates-in-javascript-7b71d0ef8e53)
*   [**像老板一样使用 JavaScript 数组**](/@lenafaure/work-with-javascript-arrays-like-a-boss-97207a042e42)

我希望您喜欢这篇关于 JavaScript 提升的介绍。

请随意评论并喜欢这篇文章，以便其他人可以在 Medium 上轻松找到它！

[![](img/3183c9cfe7a8644bc0a59d2e3e57fa27.png)](http://eepurl.com/dvio9L)[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)