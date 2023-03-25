# 函数式 Javascript 快速介绍

> 原文：<https://medium.com/hackernoon/a-quick-introduction-to-functional-javascript-7e6fe520e7fa>

**函数式编程**是目前最热门的趋势之一，有很多很好的理由来解释为什么人们会想在他们的代码中使用它。在这里，我不打算详细介绍函数式编程的所有概念和思想，但是我将尝试提供一个演示指南，说明如何在日常情况下使用函数式编程，包括 **Javascript** 。

![](img/e310ab235b2c243c9819f2b798a1d651.png)

> 函数式编程是一种编程范式，它将计算视为数学函数的求值，并避免改变状态和可变数据。

## 重新定义功能

在我们深入 Javascript 的函数式编程范式的本质之前，理解什么是**高阶函数**很重要，它为什么有用，以及这个定义的含义。高阶函数可以接受一个函数作为参数，或者返回一个函数作为结果。你应该永远记住**函数是值**，这意味着你可以传递它们，就像对待变量一样。

例如，在 Javascript 中，你可以这样做:

Using a function as a value

使用上面展示的技术，您可以使您的代码更加可重用，同时获得更多的通用性。我们都遇到过这样的情况，我们希望可以将一个函数传递给另一个函数来执行任务，但是我们必须编写一些代码来解决这个问题，对吗？使用函数式编程，您将不再需要任何变通方法，并且您可以使您的代码更加清晰易读。

[](https://hackernoon.com/why-functional-programming-matters-c647f56a7691) [## 为什么函数式编程很重要

### 为什么在软件开发人员面试中测试函数式编程技能对你的企业有好处

hackernoon.com](https://hackernoon.com/why-functional-programming-matters-c647f56a7691) 

唯一的问题是*正确的*函数代码的特点是**没有副作用**，这意味着函数应该完全依赖它们的参数作为输入，并且它们不应该以任何方式影响它们的环境，这意味着你不应该使用或改变函数本身之外的任何东西。然而，这具有一些重要的含义，例如，如果输入相同，函数将总是返回相同的输出；如果函数调用的结果没有被使用，则可以将其删除，而不会导致代码中的任何其他更改。

## 使用数组原型的内置方法

`[Array.prototype](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/prototype)`是您开始使用 Javascript 进行函数式编程的起点。它包含了许多有用的方法，用于将转换应用到数组中，这在大多数现代应用程序中是一个非常常见的用例场景。

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/prototype) [## 数组.原型

### Array.prototype 属性表示数组构造函数的原型，并允许您添加新属性…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/prototype) 

从`[Array.prototype.sort()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)`开始似乎是一个好主意，因为这是一个非常简单的转换，不出所料，您可以用它来对数组进行排序。`.sort()`只接受一个参数，一个用于比较两个元素的函数，如果第一个元素在第二个元素之前，则返回一个小于零的值，如果相反，则返回一个大于零的值。

排序听起来非常简单，直到您遇到需要比较比通常的数字数组更复杂的东西的情况。对于这个例子，我们将有一个对象数组，这些对象的重量或者以磅( *lbs* )或者以千克( *kg* )为单位，我们需要根据重量对它们进行升序排序。让我们看看这个会是什么样子:

An example of using functional programming to sort two arrays

在上面的例子中，你可以清楚地看到使用高阶函数可以节省你的空间和时间，并使你的代码更容易阅读和重用。如果你不使用`.sort()`来写这个，你将不得不写两个循环并重复大部分的逻辑，导致更长、臃肿和更难理解的代码。

排序并不是你在数组中唯一经常做的事情。根据我的经验，基于属性过滤数组是很常见的，还有什么比过滤数组更好的方法呢？过滤并不困难，因为你只需要传递一个函数作为参数，它将为任何需要过滤掉的项目返回`false`，否则它将返回`true`。简单吧？让我们在实践中看看:

A simple example of filtering

虽然`.filter()`返回数组中匹配某个条件的所有元素，但是您也可以使用`[Array.prototype.find()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find)`只获取数组中匹配某个条件的第一个元素，或者使用`[Array.prototype.findIndex()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex)`获取数组中第一个匹配元素的索引。类似地，您可以使用`[Array.prototype.some()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)`来测试是否至少有一个元素匹配所提供的条件，并使用`[Array.prototype.every()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every)`来查看数组中的所有元素是否都匹配条件。这些方法在某些应用中特别有用，所以让我们来看一个结合使用这些方法的例子:

Using functional programming to structure data

在这一点上，一切都应该开始走到一起。在上面的例子中，您可以清楚地看到高阶函数是如何为您省去大量重复且难以理解的代码的。即使在这个非常简单的例子中，您也可以看到代码是多么清晰易读，与您不使用函数式编程范例时所编写的代码形成对比。

从上一个例子的复杂逻辑中退一步，我们有时想仅仅**将一个数组转换成一个具有更多或更少字段的不同的数组**，而不改变数据。这就是`[Array.prototype.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)`的用武之地，它允许我们转换数组中的对象。`.map()`和之前方法的区别在于，它作为参数使用的高阶函数应该返回一个对象，这个对象可以是你想要的任何东西。让我用一个简单的例子来说明:

Mapping the objects of an array

在上面的例子中，通过将`.map()`应用到我们的数组，我们很容易得到一个数组，它的对象只包含想要的属性。在这种情况下，我们只需要对象的`name`和`surname`字段的字符串表示，因此我们使用简单的映射从一个对象数组创建了一个字符串数组。映射比你想象的更常见，它可能是每个 web 开发人员的武器库中一个非常强大的工具，所以如果这篇文章有一个收获的话，那就是你应该学会如何使用`.map()`。

最后，但绝对不是最不重要的，你应该注意的是**通用数组变换**也就是`[Array.prototype.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)`。`.reduce()`与上面提到的所有其他方法略有不同，因为它使用一个高阶函数作为参数，以及一个**累加器**。起初这听起来可能有点混乱，所以一个例子应该可以帮助您理解`.reduce()`背后的基本思想:

Reducing arrays to sums

对于曾经需要对数组的值求和的人来说，上面的例子应该不会特别令人困惑。我们首先定义一个可重用的高阶函数，用于对每个数组的值求和。然后，我们实际上使用这个函数对第一个数组的值求和，然后，使用第一个数组的和的累计值作为我们的初始值，我们将这个和提供给第二个数组求和函数，这样第二个数组中的所有值都将被加到这个和上，而不是零。

[](/javascript-scene/reduce-composing-software-fe22f0c39a1d) [## 减少(合成软件)

### 注意:这是学习函数式编程和组合软件的“组合软件”系列的一部分…

medium.com](/javascript-scene/reduce-composing-software-fe22f0c39a1d) 

当然，`.reduce()`可以做的不仅仅是将数组中的值相加。大多数**不适合任何其他方法的复杂转换**可以用`.reduce()`和一个数组或对象累加器轻松实现。一个实际的例子是将一个包含标题和一些标签的文章数组转换成一个包含文章计数和文章数组的标签数组。让我们看看这个会是什么样子:

Using reduce() to apply a complex transformation

上面的例子可能看起来比实际情况稍微复杂一些，所以让我们把它分解一下。首先，我们希望得到一个数组作为最终结果，因此累加器的起始值将是`[]`。然后，我们希望数组中的每个对象都包含标签的名称、计数和实际的文章列表。我们还知道每个标签在数组中只能出现一次，所以我们必须像以前一样使用`.some()`、`.find()`和`.findIndex()`来转换现有标签的对象，而不是添加一个新的，来检查它是否存在。

这里棘手的部分是我们不能定义一个函数来检查是否每个标签都存在(否则我们需要 7 个不同的函数)，所以我们在当前标签的循环中定义我们的高阶函数。这样我们可以重用它，避免重写代码。注意，这也可以通过 currying 来实现，但是我不会在本文中解释这种技术。

[](https://hackernoon.com/currying-in-the-real-world-b9627d74a554) [## 真实世界中的谄媚

### 当我开始学习函数式编程时，我学到了很多有趣的概念。但是我想知道我在哪里…

hackernoon.com](https://hackernoon.com/currying-in-the-real-world-b9627d74a554) 

在累加器数组中获得标签的对象后，我们只需要增加它的计数并将当前文章添加到它的文章数组中。最后，我们返回累加器，这就是全部内容。代码很短，仔细阅读后很容易理解，而相应的非功能性代码会非常混乱，而且明显更长。

## 最后

函数式编程是目前最热门的趋势之一，这是有充分理由的。它允许我们编写更干净、更精简、更有效的代码，而不必担心副作用和状态变化。Javascript 的`[Array.prototype](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/prototype)`方法在许多日常情况下非常有用，允许我们对数组进行简单和复杂的转换，而无需重复。