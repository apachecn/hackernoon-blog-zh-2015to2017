# 理解 JavaScript:原型和继承

> 原文：<https://medium.com/hackernoon/understanding-javascript-prototype-and-inheritance-d55a9a23bde2>

![](img/a6082752fbe2a29628ba393d693f8dac.png)

由于数量惊人的库、工具和各种各样的东西使你的开发变得更容易，许多程序员在没有深入了解某些东西如何工作的情况下就开始构建应用程序。JavaScript 就是这种行为的典型代表。虽然它是最复杂和传播最广的语言之一，但许多开发人员被吸引到使用更高级的工具和抽象掉语言的“不好的部分”。

虽然您仍然能够构建令人惊叹的应用程序，但是进入 JavaScript maelstrom 对您非常有益。理解“奇怪的部分”是普通普通程序员和高级开发人员的区别，虽然 JS 生态系统是不断变化的，但基础是所有其他工具构建的基础。理解这些会给你一个更广阔的视野，并改变你看待开发过程的方式。

JavaScript 中的继承已经讨论了很长时间。我通过参加 bootcamp-y 课程开始学习 JS，该课程旨在为我们提供日常编码挑战中所需的实用知识。我们被告知糟糕的 JavaScript 语言是如何缺乏面向对象模式的，以及我们是如何竭尽全力让它们工作的。教师们快速浏览了*原型*以及我们如何用它来模仿班级行为，而没有深入了解它是如何工作的(因为他们也不理解它)。

所以我有一种感觉，JS 是一种残缺的语言，因为它缺乏其他语言的能力来创建开箱即用的类，所以我们必须做各种疯狂的恶作剧来使它适应我们认为正确的东西。然后我开始想，如果这么难学，导致不合逻辑的行为，有这么多的障碍，也许*原型*不是正确的方法。在读了 Kyle Simpson 关于*原型*的书之后，我得出了一个结论:很多人把他们自己无法学习新概念的原因归咎于 JavaScript。JS 有它的细节，我将介绍原型继承是如何工作的，它有什么问题。

# 什么是原型？

JavaScript 中几乎所有的对象都有*原型*属性。通过使用它，更具体地说是*原型链*，我们可以*模仿*继承。原型是对另一个对象的引用，每当 JS 在当前对象上找不到您要找的属性时，就会用到它。简单地说，每当你调用一个对象上的属性，而它并不存在，JavaScript 就会转到 prototype 对象并在那里寻找它。如果找到它，它将使用它，如果没有，它将去该对象的属性，并在那里寻找。这可以在返回 *undefined 之前一直冒泡到 Object.prototype。这是原型链的本质，也是 JavaScript 继承背后的行为。*

第一次遇到原型时，上面的代码可能会让人不知所措，让我们来分解一下。我们从第 20 行开始——通过第 9 行的 *new* 关键字，我们使用 Dog 构造函数创建了一个新对象。这为我们提供了一个具有 name 属性的对象和一个绑定到其原型的 makeSound 函数。当我们调用 makeSound 时，它在当前对象(dog)的上下文中执行，我们得到正确的输出。

当我们调用 sleep()时，它显然不存在于 Dog 上，所以它沿着原型链上升到 Animal。它在那里找到它并调用它。在最后一行，我们调用了 missing()函数，它没有在任何地方定义。它将沿着原型链一直向上到达 Object.prototype，因为在那里找不到它，所以它将抛出一个错误。

正如你所看到的，我们还没有在任何地方使用过 class 这个词，我们还没有定义一个扩展基类的类。这与其说是继承，不如说是委托。每个对象都有这个原型属性，该属性指向另一个对象，如果在当前对象上找不到我们要找的属性，那么这个对象应该被委派责任。这并没有什么奇特之处，目标只是在它不能处理任务时将责任委托给它的上级。

# 遮蔽

如果我们再次透过继承的棱镜看，我们知道我们经常需要重写属性和方法。在原型继承中，这被称为**隐藏**。

我们在这里做的是在 Dog 的原型上创建一个同名的属性，所以当我们调用它时，它会在那里找到它，并停止原型链冒泡。如您所见，我们没有在任何地方使用 override 关键字，我们只是在 Dog 对象上声明属性，这样 JavaScript 就不会在原型链中开始寻找它。

# **继承问题**

如果你读过我以前的文章，我想告诉你的是，JavaScript 总是有它自己的特点，不能正确理解这些特点会让你非常头疼。在某些时候，你可能需要在一个属性上使用*隐藏*，但是在属性内部调用同名的“父”函数。虽然在大多数其他语言中，你可以使用 *super()* ，但这里的事情有点复杂。如果您使用这种方法，并在被隐藏的函数内部调用 *this.sleep()* ，您将最终调用同一个函数，结果将是一个递归。因此，我们必须想出另一个办法来解决这个问题。

```
Dog.prototype.sleep = function() {       
  Animal.prototype.sleep();
}
```

这听起来是一个不错的方法，而且几乎是，但是如果你执行它，你会发现你不会得到你期望的结果，因为上下文绑定是不正确的。

```
Dog.prototype.sleep = function() {
  Animal.prototype.sleep.call(this);
}
```

然而，通过像这样执行它，我们使用当前函数的上下文绑定来调用 Animal 上的函数。换句话说，我们使用当前狗的数据来调用动物的睡眠功能，我无法解释得比这更简单。

# 继承的概念

对我来说，最难理解的事情之一是原型关键字的含义。我试图在这个词和遗传过程之间建立一些心理联系，直到我意识到它们真的没有联系。我们所做的只是滥用原型行为委托来模仿继承，而事实上我们并没有继承。为了给你另一个例子来说明我们对基于类的层次结构的概念与 JS 原型的不同，我向你展示了 ***构造函数*** 属性。

这是 Object.prototype 上的一个属性，它返回创建我们调用它的对象的构造函数。不是函数名，而是函数本身。如果我们将 *dog* 变量的构造函数属性登录到控制台，你认为会发生什么？你大概期望它返回狗的功能，但是你会很惊讶。

```
console.log(dog.constructor); // [Function: Animal]
```

等等什么？这是返回动物函数，因为它在对象创建时被附加到狗属性。这再一次表明，这些方法和属性的实现并没有考虑到面向对象的设计。

# ES6 类

从 ES6 开始，你可以说 JavaScript 中的继承层次问题已经解决了。我们得到了 *class* 关键字以及所有其他随之而来的好处。我们现在可以定义类，扩展它们，使用构造函数和 *super* 关键字来访问父方法，而没有上面代码的丑陋。

如果你已经熟悉了类，这看起来会好很多。它去掉了不必要的重复 prototype 关键字、Object.create()的使用以及调用“父”函数时必须使用的黑魔法。你也有了正确的语法，理解发生了什么就容易多了。ES6 代码和上面的原型代码或多或少做着完全相同的事情，但是新标准的代码更容易理解。

现在你需要记住的是，这个语法(大部分)只是原型的美化版本。它使具有其他语言背景的人更容易理解，从而真正简化了开发过程。但是需要考虑的是，由于同样的原因——ES6 类是高于原型的语法糖——仍然存在一些问题。

因为原型方法更像是委托而不是真正的继承，“父”对象作为引用被传递。在其他语言中，在创建一个对象时，您会得到其功能的副本，包括父类。因此，在 JS 中，子对象可以访问“父”函数，即使它们是在子对象之后创建的。这是由于 prototype 只是一个引用，而不是一个具体的对象。这是一条热线，当对象缺少某个特定的属性时，它们使用这条热线来找到出路。

# 结论

在 ES6 引入“真正的”类之前，整个继承的概念都被搞乱了，而且(通常)是以错误的方式教授的。原型更多的是一种委托工具，它们的行为不像类。现在有了现代方法，我们几乎可以创建正常的类层次结构，并为它们使用可理解的语法，但要警惕可能出现的问题。对于内部发生的事情，这个语法只是一个美丽的外表。

如果你对更多的 JS 相关内容感兴趣，你可以从 [***这里***](https://buttondown.email/kondov) 订阅我的时事通讯，或者你可以看看同一系列的其他文章:

[](https://hackernoon.com/understanding-javascript-scope-1d4a74adcdf5) [## 理解 JavaScript:范围

### 由于数量惊人的库、工具和各种各样的东西使你的开发更容易，很多…

hackernoon.com](https://hackernoon.com/understanding-javascript-scope-1d4a74adcdf5) [](https://hackernoon.com/understanding-javascript-prototype-and-inheritance-d55a9a23bde2) [## 理解 JavaScript:原型和继承

### 由于数量惊人的库、工具和各种各样的东西使你的开发更容易，很多…

hackernoon.com](https://hackernoon.com/understanding-javascript-prototype-and-inheritance-d55a9a23bde2) [](https://hackernoon.com/understanding-javascript-the-this-keyword-4de325d77f68) [## 理解 JavaScript:这个关键字

### 由于数量惊人的库、工具和各种各样的东西使你的开发更容易，很多…

hackernoon.com](https://hackernoon.com/understanding-javascript-the-this-keyword-4de325d77f68) [](https://hackernoon.com/understanding-javascript-new-keyword-ec67c8caaa74) [## 理解 JavaScript:新关键字

### 由于数量惊人的库、工具和各种各样的东西使你的开发更容易，很多…

hackernoon.com](https://hackernoon.com/understanding-javascript-new-keyword-ec67c8caaa74) [](https://hackernoon.com/understanding-js-coercion-ff5684475bfc) [## 理解 JS:强制

### 由于数量惊人的库、工具和各种各样的东西使你的开发更容易，很多…

hackernoon.com](https://hackernoon.com/understanding-js-coercion-ff5684475bfc) [](https://hackernoon.com/understanding-js-the-event-loop-959beae3ac40) [## 理解 JS:事件循环

### 由于数量惊人的库、工具和各种各样的东西使你的开发更容易，很多…

hackernoon.com](https://hackernoon.com/understanding-js-the-event-loop-959beae3ac40)