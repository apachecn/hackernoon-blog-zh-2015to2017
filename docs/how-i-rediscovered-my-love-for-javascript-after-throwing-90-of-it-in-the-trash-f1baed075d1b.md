# 在把 90%的 JavaScript 扔进垃圾桶后，我如何重新发现我对它的热爱。

> 原文：<https://medium.com/hackernoon/how-i-rediscovered-my-love-for-javascript-after-throwing-90-of-it-in-the-trash-f1baed075d1b>

![](img/8d22b44e30e64a81133c1a7942bc73f4.png)

[Photo by Alexas_Fotos](https://pixabay.com/en/garbage-can-garbage-bucket-green-1111449/)

# 我与 JavaScript 的关系

我的 JavaScript 之旅始于 1997 年的 Netscape Navigator 3。那时候你不能用它做很多事情。JavaScript 最酷的用途是创建鼠标悬停。在当时，这是一些高科技💩。能够通过悬停来改变页面上的内容。震惊了。这都是 DHTML 之前的东西，所以你甚至不能隐藏/显示 DOM 元素。它仍然是如此神奇。

此时，JavsScript 的发展非常缓慢，主要用于表单验证。JavaScript 没有太大的进步或兴趣，完全不像今天。这是一个额外的、附加的、不错的东西，但它绝对不是主要的吸引力，你仍然必须确保你的应用程序可以在禁用 JavaScript 的情况下运行。

然后框架大举进入市场。jQuery，Knockout，Angular，React，Vue 等。起初他们进展缓慢。现在我们每天都有新的框架。

我们也看到**JavaScript 本身的进化正在加速**。我们花了很长时间才得到 ES6。人们已经不再谈论 ES7，转而关注 ES8！

我们还有无限多的选择，如 TypeScript、CoffeeScript、ClojureScript、ELM 等。

这可能会让人不知所措，而且不可能跟上所有的事情。

# 错误的道路

当 JavaScript 刚开始成熟时，面向对象编程(OOP)开始悄悄进入这种语言……*我喜欢它*。

我开始使用 JavaScript 和所有不同的方法来创建一个类。我终于可以继承遗产了。我开始思考，JavaScript 开始成为真正的语言。

> 直到多年以后，我才发现 JavaScript 中的 OOP 是一个可怕的错误。

我试着把我从 C#中学到的东西强加到 JavaScript 中，起初事情看起来很有希望，但是它的复杂性令人困惑。

因为 JavaScript 的原型继承不像 C#那样工作，我会发现自己每天都在编写`console.log(this)`。*我现在是谁？如果你没有把每件事都做对，那就是一场噩梦。私有方法和值必须以下划线为前缀，甚至更糟的是用闭包包装。*

面向对象的 JavaScript 不仅带来了面向对象的所有问题，还增加了许多新问题。

# 然后我发现了函数式编程

**刚开始没得到**。我会看到代码并理解代码，但我不明白*为什么*。最终，**我强迫自己去学**。我在 EDX 为**免费上了[函数式编程入门课程](https://www.edx.org/course/introduction-functional-programming-delftx-fp101x-0)，目的是将这些技术应用到 JavaScript 中。**

> 函数式编程给了我一个新的视角，它让我以一种完全不同的方式看待编程。

一开始很奇怪，我花了一段时间才习惯。一切都是倒退的。不可改变。外国的。

慢慢地，我会尝试用函数的方式来解决代码。因为我不熟悉，所以花了更长的时间，而且更像是一次学术练习，但是我开始掌握它，并且越来越好。然后我得了*为什么*。

我的代码变得简单且可重用。慢慢地，通用语言特性开始从我的代码中消失。我的代码看起来像一种完全不同的语言。这还是我写的 JavaScript 吗？

# 不再有“var”

我所有的`var`都换成了`const`。当我的代码变成**不可变**，我的函数变成**纯**，`var`完全消失了。

我记得看着我的代码，惊叹于没有看到一个`var`甚至`let`，一切都变成了`const`。我的兴趣水平开始上升。

# “for”循环

循环是最先消失的东西之一。我开始用`filter`、`map` 和`reduce`替换我的大部分`for`循环。需要额外东西的循环被递归或者像 [lazy.js](http://danieltao.com/lazy.js/) 这样的库所取代。在你开始嘀咕`break`、[这个词之前，先看看这个](https://hackernoon.com/rethinking-javascript-break-is-the-goto-of-loops-51b27b1c85f8)。

[](https://hackernoon.com/rethinking-javascript-death-of-the-for-loop-c431564c84a8) [## 重新思考 JavaScript:For 循环的死亡

### JavaScript 的 for 循环为我们提供了很好的服务，但是它现在已经过时了，应该被更新的函数所取代…

hackernoon.com](https://hackernoon.com/rethinking-javascript-death-of-the-for-loop-c431564c84a8) 

`for`循环现在在我的代码库中已经完全灭绝了。如果你碰巧发现了一个，把它指出来，这样我就可以杀了它。

# “如果”语句

声明很快就成了下一个被抛弃的东西。我不再编写嵌套在 if-else 中的大块代码。(这也是使用 OOP 的良好实践)。逻辑被提取成函数。在这之后，将`if`转换成简单的`ternary`操作符就变得显而易见了。

[](https://hackernoon.com/rethinking-javascript-the-if-statement-b158a61cd6cb) [## 重新思考 JavaScript:if 语句

### 功能性思维打开了我对编程的思维。它让我对代码有了更深的理解…

hackernoon.com](https://hackernoon.com/rethinking-javascript-the-if-statement-b158a61cd6cb) 

现在几乎不可能在我的代码中找到一个`if`语句。为了其他程序员的可读性，我很少使用。

# RIP '开关'

随着`if`和`for`的消失，我消除的下一个特征是`switch`。这些不经常使用，但我想要一个功能的替代品。

[](https://hackernoon.com/rethinking-javascript-eliminate-the-switch-statement-for-better-code-5c81c044716d) [## 重新思考 JavaScript:删除 switch 语句以获得更好的代码

### 在我的前 3 篇文章中，我说服你删除 if 语句，取消 for 循环，并且不要使用 break。现在我要走了…

hackernoon.com](https://hackernoon.com/rethinking-javascript-eliminate-the-switch-statement-for-better-code-5c81c044716d) 

我也很喜欢 Ramda 的 [cond operator](http://ramdajs.com/docs/#cond) 作为 switch 的替代品。

# 不再有“这个”

没错，你没听错！`this`已经*完全*消失了。如果你到现在还没有和我在一起，你现在应该和我在一起了。

> 函数式 JavaScript 将允许你不用'`this’`'编写整个应用程序。

现在，只有数据和函数，我们完全不需要`this`。我开始把一个对象看作可变的状态+函数。我不需要可变状态或函数附加到对象上，所以我把它们断开了。

我甚至写了一篇文章，讨论将方法从它们的对象中分离出来的一些好处。

[](https://hackernoon.com/functional-javascript-decoupling-methods-from-their-objects-aa3ca13d7ae8) [## 函数式 JavaScript:从对象中分离方法

### 在我的项目中，我最后总是做的一件事是将方法从它们的对象中分离出来。映射、过滤和减少不是…

hackernoon.com](https://hackernoon.com/functional-javascript-decoupling-methods-from-their-objects-aa3ca13d7ae8) 

# 面向对象的设计是不必要的

回顾过去，我现在意识到 OOP 增加了不必要的复杂性。我发现我可以在没有可变状态的情况下执行相同的任务。

代码变轻了，因为我不再传递这些沉重的东西。只有数据和函数。这些函数现在变得更加可重用，因为它们不再与一个对象相关联。

我不再需要担心传统继承带来的所有问题，而 JavaScript 在这方面做得很差。

JavaScript 缺少私有、公共、内部或受保护的访问修饰符不再是问题。创建访问修饰符是为了解决 OOP 带来的问题。函数式 JavaScript 中不再存在的问题。

# 摘要

我的代码现在看起来完全不同了。它由许多**纯函数**的集合组成，组织成 **ES6 模块**。我用这些函数组成更复杂的函数。大多数函数都是单行的 **lambda 表达式，可以立即返回值**。

我现在将软件输入视为数据流，并对这些数据流进行反应式编程。

我对函数式编程的理解给了我更多的选择来解决常见的问题。

我还了解到**函数式编程是包容性的**，可以根据你的需要在现有项目*中使用*。C#的 LINQ 是面向对象语言中功能设计的一个很好的例子。

函数式编程可以很美。

[](https://hackernoon.com/the-beauty-in-partial-application-currying-and-function-composition-d885bdf0d574) [## 局部应用美、曲线美和功能组合美。

### 故事时间

hackernoon.com](https://hackernoon.com/the-beauty-in-partial-application-currying-and-function-composition-d885bdf0d574) 

我会回应每一个问题和评论，所以不要害羞！

我知道这是一件小事，但当我在媒体和 Twitter 上收到这些后续通知时，我感到非常高兴。或者你觉得我满嘴屁话，在下面的评论里告诉我。

干杯！

[](https://medium.com/@joelthoms/latest) [## 乔尔·托马斯写的最新故事

### 阅读乔尔·托马斯在 Medium 上写的最新故事。计算机科学家和技术传播者，拥有 20 多年的…

medium.com](https://medium.com/@joelthoms/latest)