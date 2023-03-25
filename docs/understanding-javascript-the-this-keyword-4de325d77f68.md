# 理解 JavaScript:这个关键字

> 原文：<https://medium.com/hackernoon/understanding-javascript-the-this-keyword-4de325d77f68>

![](img/0583900272135e3efed3c87ca9f57fbb.png)

[Photo credit](http://www.tedxhsg.org/program-2/#program)

由于数量惊人的库、工具和各种各样的东西使你的开发变得更容易，许多程序员在没有深入了解某些东西如何工作的情况下就开始构建应用程序。JavaScript 就是这种行为的典型代表。虽然它是最复杂和传播最广的语言之一，但许多开发人员被吸引到使用更高级的工具和抽象掉语言的“不好的部分”。

虽然您仍然能够构建令人惊叹的应用程序，但是进入 JavaScript maelstrom 对您非常有益。理解“奇怪的部分”是普通普通程序员和高级开发人员的区别，虽然 JS 生态系统是不断变化的，但基础是所有其他工具构建的基础。理解这些会给你一个更广阔的视野，并改变你看待开发过程的方式。

另一个引起大量讨论的话题是**这个**关键字。更具体地说，这是有时意想不到的行为。大约一年前，我的一个同事让我看一下她的 JS 代码，因为她有奇怪的错误。作为一名刚开始接触 JavaScript 的 Java 开发人员，她犯了一个错误，认为自己在真正学习之前就能理解这门语言——就像许多人一样。

有多少其他进入 JS 的开发者对**这个**有问题，我不知道，但我预计这个数字会很高。事实上，这可能是你必须理解的最简单的 JavaScript 概念。如果要我用自己的话来解释，我可能会说出这样的话:

> this 关键字不涉及使用它的函数或它的作用域。它指的是一个函数正在其上执行的对象，并且完全依赖于该函数的调用点。

让我们创建一个非常基本的例子来说明这是怎么回事:

```
function testThisKeyword() {
    console.log(this) // [object Window]
}testThisKeyword()
```

让我给你另一个更能质疑你对 JavaScript 理解的问题:

```
function testThisKeyword() { 
    console.log(this.name) // Alex
}var name = 'Alex'
testThisKeyword()
```

什么？那到底是怎么输入名字变量的呢？它甚至不是在函数的范围内创建的！这是开发人员感到非常难过的原因之一。它只是不像你期望的那样工作。与普遍的看法相反，地下并没有黑魔法。这里发生的事情是，name 变量是在全局对象中声明的，因此它可以作为它的一个属性被访问。如果我们看看函数的调用点，我们会发现我们并没有在一个对象上隐式调用它，所以这将引用全局对象。

**这个**关键字在每个函数的范围内自动定义，它的绑定以三种方式发生——**默认**、**隐式**和**显式**。独立函数调用的例子已经向您展示了**默认绑定**是如何工作的。需要记住的是，如果没有指定另一个绑定，这就是后备绑定。让我们更详细地了解一下**隐式**和**显式**绑定。

# 隐式结合

```
const person = {
  name: 'Alex',
  greet() {
    console.log('Hey my name is ' + this.name)
  }
}person.greet() // Hey my name is Alex
```

为了理解它的值，你需要看看它在哪里被调用。**不是使用它的函数，而是调用点。在本例中，所有者或包含对象是 person 对象。我喜欢用的一个技巧是看函数调用的左边。站在点之前的对象就是 this 关键字将要绑定的对象。**

需要记住的是，如果您开始将函数作为引用传递，这种绑定可能会“丢失”。即使你引用的函数是一个对象的属性，如果你单独调用它，它也不会有你期望的绑定，而是使用默认绑定。这就是为什么对于**这个**唯一重要的是函数的**调用点**。一个更好更深入的例子可以在 Kyle Simpson 关于这个和对象原型的书中找到，所以我在这里就不赘述了。

# 显式绑定

在隐式绑定的例子中，我们必须使用一个所有者对象来实现我们想要的绑定。但是如果我们不想引用对象上的函数，或者我们不能改变它，该怎么办呢？有一个解决方案可以明确地告诉一个函数它应该为此使用什么对象——使用诸如**调用、应用**和**绑定**之类的函数。使用显式绑定是如此普遍，以至于这两个函数都可以从函数原型中获得，但是它们之间存在差异。

**调用**函数允许你传入**这个**关键字应该绑定到的对象。它还允许您将函数的参数作为附加参数传递。

```
function greet() {
    console.log( this.name );
}

var person = {
    name: 'Alex'
};

greet.call( person, arg1, arg2, arg3, ... ); // Alex
```

**应用**函数类似于**调用**，不同之处在于函数参数是作为数组或类数组对象传递的。

```
function greet() {
    console.log( this.name );
}

var person = {
    name: 'Alex'
};

greet.apply( person, [args]); // Alex
```

**绑定**函数与前两个略有不同。它**创建一个新的**函数，该函数将调用原来的函数，而**这个**绑定到传入的任何内容。

```
function greet() {
    console.log( this.name );
}

var person = {
    name: 'Alex'
};

var greetPerson = greet.bind( person );
greetPerson(); // Alex
```

在文章的前面，我告诉过你这些是关键字的主要绑定。我可能骗了你，因为当你使用 **new** 关键字时，也发生了绑定。然而，我打算写一篇关于这个的小文章，所以我现在跳过解释。

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