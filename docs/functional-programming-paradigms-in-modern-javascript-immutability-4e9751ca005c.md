# 现代 JavaScript 中的函数式编程范式:不变性

> 原文：<https://medium.com/hackernoon/functional-programming-paradigms-in-modern-javascript-immutability-4e9751ca005c>

![](img/964c52b05131c7dde3db468d6a489295.png)

这是实用函数式编程范例系列文章的第二章。如果你还没有阅读前面的章节你可以从 [*这里*](https://hackernoon.com/functional-programming-paradigms-in-modern-javascript-pure-functions-797d9abbee1) *。*

本文将集中讨论每个函数式程序员都必须深入理解的另一个基本主题— **不变性**。更确切地说——是现代 JavaScript 开发中的应用。

**不变性**是什么意思？你可以在维基百科上看到的定义是，*不可变对象是一个在它被创建后不能被修改的对象*。或者换句话说，某个变量不能改变它的值。

我第一次遇到不变性时，我不止一次地阅读定义，以确保我理解正确。*我怎样才能用从不改变值的变量编程？对我来说，这是非常违反直觉的。*

如今，我们应该越来越关注开发者的体验。我的意思是，代码应该以这样一种方式编写，它将很容易被下一个开发人员理解。这正是**不变性**发挥作用的地方。

我读的函数式编程越多，我就越明白它是*的实际目标。仔细想想，人们编写程序已经有很长时间了，其中很大一部分是以非功能性的方式编写的。*

如果你是一名 web 开发人员，你可能会遇到更多没有使用 FP 实践的程序。那么*为什么*你应该花时间去学习这个深奥的编程范例呢？

> 函数式编程的目标是让我们少思考，多写描述性代码。

您可能遇到过这样的情况，您沿着 JavaScript 文件的行跟踪一个变量，拼命地试图找到它变成`undefined`的确切行。

这就是我说 FP 让我们少思考的意思。当一个变量被定义时，它被赋予一个值，这个值永远不会改变。句号。

这也会降低你的独立变量的寿命。这是自变量声明以来使用的行数。

就像我们在上一章讨论的纯函数一样——你不可能让所有的变量都不变，这没关系。

函数式编程不应该放在代码质量之前。FP 的目的是帮助我们写出更好更干净的代码，所以如果你改变了一个变量，你的代码会更干净更容易理解，那就这样吧。

在本文的其余部分，我们将研究如何在用 JavaScript 编写的现代程序中实现这一点。

# 你一直在使用不变性

当我们谈论不变性时，我们过于强调事物的可变方面。我们不应该重新分配价值观。而事实上，我们应该更加关注我们的**函数**，并且在编写它们的时候考虑到不变性。

这一系列文章的目标是指出函数式编程模式是如何被使用的，事实上，你在日常的 JavaScript 编码中已经遇到了不可变性，但是你可能还没有注意到它。让我们来看看语言中的一些内置函数。

如您所见，这些函数不修改函数的调用者。这些函数中的每一个都返回一个全新的值，而不修改调用它们的对象。在语言的许多其他功能中也可以观察到同样的行为。

第一次遇到这种情况时，我其实有点沮丧。举例来说，如果它实际上没有修改我*调用*它的数组，那么在数组上调用那个函数有什么意义呢？！我总是忘记这一点，这是我的程序中一大堆错误的唯一目的。老实说，我花了更多的时间才理解这种方法的好处。

# 声明式 vs 迭代式

作为程序员，你要编写的一些最基本的算法包括循环数据和以某种方式修改数据。也许你想过滤它或修改值。

我们被教导的正统方法是使用循环和变异对象。这没有什么不对，大多数人都是这样做的，而且他们会这样做很长一段时间。这种做法被认为是**势在必行**。通过这样做，我们给了程序明确的指令，告诉它如何做我们需要的事情。

另一方面，函数式编程意味着**声明性的**。这是什么意思？你还记得内置函数总是返回一个新的实例吗？这允许我们将它们与其他这样的函数链接起来，以便用更少的代码完成我们通常用循环做的事情。

对我来说，这是我停止写循环的时刻。说实话，我都不记得上次用 JavaScript 写循环是什么时候了。使用这种模式帮助我写出了更短、更干净、结构更好的代码。

当然，这是你在任何地方都会看到的最基本的例子。没有人在编写数字相乘的程序，但重点是要明白 FP 和不变性能为你节省多少工作量。

实际上，您可能会遍历一组数据，进行一些条件检查，然后修改值，最后进行计算。通过给上面的函数一些适当的命名，让我向您展示一下与循环相比，这是多么容易理解。

如果您只需在 filter、map 和 reduce 函数中编写逻辑，就可以用更少的代码完成这项工作，但是为了展示它有多冗长，我将逻辑抽象成了函数。由于这些函数总是返回一个新的对象，所以它们可以一个接一个地链接起来，以产生如此强大的结果。

每次写循环的时候都想一想一年后你会不会理解你写的东西。现在看看这段代码——不管你什么时候看，它做什么以及以什么顺序做都很清楚。

# 更实际的东西？

如果你仍然不相信不变性有多么强大，让我们看看反应。更确切地说，是与 Redux 的集成。如果您不熟悉 Redux，您可能想先阅读一下。

Redux 应用程序的唯一来源是商店。这是一个包含应用程序当前状态的对象，你的组件将从中获取信息。但你的应用程序的状态一直在变。用户在你的页面上采取行动，这需要你不断地修改你的用户界面。

然而，Redux 咒语是永远不要改变状态！相反，在你的 reducers 中，你应该总是返回一个新的对象来代替之前的状态。

在本例中，我们向应用程序状态添加了一个天气数据列表，这样组件就可以列出这些数据。注意，reducer 不只是在状态上设置一个属性，而是返回一个全新的对象。使用`...`操作符展开之前的状态，然后添加新的属性。这样，它们将覆盖当前状态对象中的对象。

为了在不同的情况下显示不同的这一点，看一下从状态中移除一个项目。这个 reducer 使用来自 **lodash** 的`omit`函数从状态中删除一个项目并返回一个全新的对象。

你的第一个想法可能是这很慢，并且不断地创建和改变对象会抑制你的应用程序。不会的——相信我的话。一些真正聪明的家伙已经做了很多工作，以确保这种情况不会发生。

然而，这实现了 Redux 开发中最强大的功能之一——时间旅行调试。因为在任何时候，你的应用程序的状态都可以类似于一个具有特定属性的对象，如果我们将它与另一个遵循相同接口的对象交换，我们就可以完全修改我们应用程序的 UI。

时间旅行调试允许您跟踪应用程序的所有先前状态，并通过单击快速更改您的 UI-它只是交换对象。

# 逮到你了

随着 ES6 的推出，我们有了`const`关键词。有了它，我们可以创建不能被重新赋值的变量。事实上，如果你用 const 实例化一个变量，然后试图给它一个不同的值，你会收到一个错误。

但是如果没有一些奇怪的东西，它就不是 JavaScript 了，不是吗？即使你不能完全改变这个值，你仍然可以改变它。

这是在处理数组时要记住的事情。您不能直接修改变量的内容，但是可以像这样操作它的内容。为了避免不必要的麻烦，让你的团队确定一个可变的约定并坚持下去。

如果你已经读到这里，谢谢你。如果你在这篇文章中发现了一些有趣的东西，请按住鼓掌按钮！

# 下一个

接下来，我们将进入函数组合，并解释一些更复杂的函数编程概念，如 currying 和局部应用。敬请期待！