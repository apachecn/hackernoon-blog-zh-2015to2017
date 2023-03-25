# 理解 JS:事件循环

> 原文：<https://medium.com/hackernoon/understanding-js-the-event-loop-959beae3ac40>

![](img/9594e39dd50a92d9c5c9534cd7baef3a.png)

由于数量惊人的库、工具和各种各样的东西使你的开发变得更容易，许多程序员在没有深入了解某些东西如何工作的情况下就开始构建应用程序。JavaScript 就是这种行为的典型代表。虽然它是最复杂和传播最广的语言之一，但许多开发人员被吸引到使用更高级的工具和抽象掉语言的“不好的部分”。

虽然您仍然能够构建令人惊叹的应用程序，但是进入 JavaScript maelstrom 对您非常有益。理解“奇怪的部分”是普通普通程序员和高级开发人员的区别，虽然 JS 生态系统是不断变化的，但基础是所有其他工具构建的基础。理解这些会给你一个更广阔的视野，并改变你看待开发过程的方式。

# 事件循环是什么？

你可能听说过 JavaScript 是单线程语言。您甚至可能听说过调用堆栈和事件队列这两个术语。大多数人都知道事件循环允许 JavaScript 使用回调和承诺，但是还有更多。不涉及太多的细节，我们将有一个 JavaScript 代码实际上是如何执行的高级视图。

# 调用堆栈

JavaScript 有一个单独的调用栈，它跟踪我们当前正在执行的函数以及之后将要执行的函数。但是首先，什么是堆栈？堆栈是一种类似数组的数据结构，但有一些限制—您只能在后面添加项，并且只能删除最后一项。另一个例子是一堆盘子——你把它们叠放在一起，任何时候你只能拿走最上面的一个。

当你要执行一个函数时，它会被添加到调用栈中。然后，如果该函数调用另一个函数，则另一个函数将位于调用堆栈中的第一个函数之上。当您在控制台中得到一个错误时，您会得到一条很长的消息，向您显示执行的路径——这是堆栈在那个时刻的样子。但是如果我们提出一个请求或者给某个东西设置一个超时时间呢？理论上，这应该冻结整个浏览器，直到它被执行，以便调用堆栈可以继续？然而在实践中，您知道这不会发生——因为有事件表和事件队列。

# 事件表和事件队列

每次调用 setTimeout 函数或执行一些异步操作时，它都会被添加到事件表中。这是一种数据结构，它知道在某个事件之后应该触发某个功能。一旦事件发生(超时、点击、鼠标移动)，它会发送一个通知。请记住，事件表不会执行函数，也不会自己将函数添加到调用堆栈中。它的唯一目的是跟踪事件并将它们发送到事件队列。

事件队列是一种类似于堆栈的数据结构——同样，您可以将项目添加到后面，但只能从前面删除它们。它存储了函数执行的正确顺序。它从事件表中接收函数调用，但是它需要以某种方式将它们发送到调用堆栈。这就是事件循环出现的地方。

# 事件循环

我们终于到达了臭名昭著的事件循环。这是一个不断运行的过程，检查调用堆栈是否为空。想象它像一个时钟，每次它*滴答*时，它查看调用堆栈，如果它是空的，它查看事件队列。如果事件队列中有正在等待的东西，它将被移动到调用堆栈中。如果不是，那么什么都不会发生。

这里有几个有趣的案例。您认为下面的代码将以什么顺序运行？

```
setTimeout(() => console.log('first'), 0)
console.log('second')
```

有些人认为，因为 set timeout 是用 0(零)调用的，所以应该立即运行。事实上，在这个具体的例子中，你会看到“第二”在“第一”之前打印出来。JavaScript 看到 setTimeout 并说“好吧，我应该把它添加到我的事件表中并继续执行”。然后，它将遍历事件表、事件队列，并等待事件循环到达*滴答*，以便运行。

# 利用

事件循环行为的另一个有趣例子是递归。你见过堆栈溢出错误信息吗？当你进行无限递归的时候，你有时会得到这个结果，但有时你实际上有大量的递归调用要做。有一个简单而又*古怪的*解决方法，它将允许你保留你的代码结构并且仍然进行大量的调用——将你的递归调用包装在 setTimeout 中。

不直接调用 recursion()(假设这是你的方法名)，可以调用 setTimeout(() => recursion()，0)。这将避免堆栈溢出，因为调用将通过事件表和队列，而不是直接堆积在堆栈上。尽量避免使用这种方法，但这是 JavaScript 行为的一个很好的例子。

# 结论

还有更多的事情要做，这只是对循环和它周围的一切的基本解释。尽管我想尽可能简单地说明这一点，但如果不深入整个过程，就无法解释事件循环的作用。还需要记住的是，这个解释是在 V8 JavaScript 引擎的上下文中进行的。它是 Chrome 背后的引擎，也用于 Node。

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