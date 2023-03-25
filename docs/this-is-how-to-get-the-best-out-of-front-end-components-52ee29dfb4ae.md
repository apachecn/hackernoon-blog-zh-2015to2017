# 前端组件是一个函数🔊

> 原文：<https://medium.com/hackernoon/this-is-how-to-get-the-best-out-of-front-end-components-52ee29dfb4ae>

## 我们应用于函数的原理也可以应用于组件

![](img/31077214b413cc29c304bd8ba794ac24.png)

A picture of [“World”, by Susan Stockwell](http://tagfinearts.blogspot.com.au/2012/08/world-by-susan-stockwell.html). The work comprises of a world map made from discarded computer components.

Listen to the audio version!

一个`function`代表了定义一个操作的基本方法。它可以接受`arguments`并产生一个`result`。

```
result = function(arguments)
```

你可以用函数的形式表达[一切](https://youtu.be/3VQ382QG-y4?t=36m21s)。这是一个心理模型。软件开发的[图](/@fagnerbrack/you-cant-call-yourself-an-explorer-without-a-map-4e19ce51daa3)。

以下是一些例子:

当通过 HTTP 加载 web 页面时，您可以将检索资源(URL)的命令视为函数，将查询字符串视为参数。返回的结果表示页面的状态(HTML 输出):

```
state = getResource(queryString)
```

在 Git 中，树的当前状态表示包含一些更改的提交结果:

```
currentState = commit(changes)
```

同样在 Git 中，存储库的最终状态(您所指向的最后一次提交)代表了从开始按时间顺序重放所有过去提交的所有更改的结果:

```
finalState = replayAll(pastCommitsInChronologicalOrder);
```

对于前端组件，也有类似的模式:

```
visualRepresentation = renderComponent(attributes)
```

前端组件可以表示为一个**呈现函数**，它接受**属性**并向用户产生一个**视觉表示**。

> 前端组件是页面的一个功能。

在 HTML 中，**组件渲染**是通过一个 **HTML 元素**声明的，该元素使用浏览器向用户产生**可视化表示**。HTML 元素可以包含**属性**。

[The HTML code](https://gist.github.com/FagnerMartinsBrack/c48dbcc84408a980a8aef1f4659dc74a) for an input element with the attributes "type" and "placeholder"

[The pseudo-JavaScript code](https://gist.github.com/FagnerMartinsBrack/0bdee5bdd15fff0a27a94ffd86ff0336) for an "input" rendering function in which the result is assigned to a variable called "visual representation". The function is being called with an Object Literal containing the attributes "type" and "placeholder".

在 React 中，使用[](https://hackernoon.com/tagged/javascript)**JavaScript 声明**组件渲染**，JavaScript 操纵 DOM 输出 **HTML** ，这允许浏览器向用户提供最终的视觉表示。将**属性**命名为**道具**。**

**[The React J.S.X. code](https://gist.github.com/FagnerMartinsBrack/33bed13802b87410cbf240118fa38eaf) for an "auto-suggestion" element with the attributes "list of suggestions" that is an array and "disabled" that is a boolean.**

**[The](https://gist.github.com/FagnerMartinsBrack/bed4222c429222e41e5c905f371ad285) [pseudo-](https://gist.github.com/FagnerMartinsBrack/0bdee5bdd15fff0a27a94ffd86ff0336)[JavaScript code](https://gist.github.com/FagnerMartinsBrack/bed4222c429222e41e5c905f371ad285) for an "auto-suggestion" rendering function in which the result is assigned to a variable called "HTML". The function is being called with an Object Literal containing the attributes “list of suggestions” and “disabled”.**

**在 Angular 1.4 中，使用从*指令定义对象返回的`restrict: 'E'`属性，使用**指令**来声明**组件渲染**。*`restrict: 'E'`定义强制将指令声明为标记中的 HTML 元素。输出是 **HTML** ，允许浏览器向用户提供最终的可视化表示。组件也有**属性**。**

**[The Angular code](https://gist.github.com/FagnerMartinsBrack/8b801e0483f869805b17e44c89daadf2) for a "progress bar" element with the attribute “title”.**

**[The](https://gist.github.com/FagnerMartinsBrack/b45878f283d7277a84a7101ce537875d) [pseudo-](https://gist.github.com/FagnerMartinsBrack/0bdee5bdd15fff0a27a94ffd86ff0336)[JavaScript code](https://gist.github.com/FagnerMartinsBrack/b45878f283d7277a84a7101ce537875d) for a "progress bar" rendering function in which the result is assigned to a variable called "HTML". The function is being called with an Object Literal containing the attribute “title”.**

**基本上，组件都是一样的。**

**网络正走在软件工程多年前走过的同一条路上。它重新发明了构建抽象复杂逻辑的小组件的规则。一整套似乎已经失传的哲学体系。**

**今天，每个人都称它为`Components`。明天，可能会叫别的。**

**最终，每个组件只是一个产生结果和接受参数的函数:**

```
result = function(arguments)
```

> **我们应用于函数的许多原则也可以应用于组件。**

**让我们以一个无限可滚动列表为例，说明将组件视为功能可以帮助您构建更好的软件。**

**在内部，组件向服务器发送请求，以便在用户每次向下滚动时检索它需要的部分内容:**

**[The pseudo declarative code](https://gist.github.com/FagnerMartinsBrack/4e2f1fdb8bd331a6aaa952dfa7847307) for a component called "infinite scrollable list" using no specific syntax or framework, just the opening, and closing tags. There are no attributes set for it.**

**网络请求是一个副作用。您希望避免在组件内部处理副作用，就像在函数内部一样。相反，将其作为参数传递:**

**[The pseudo declarative code](https://gist.github.com/FagnerMartinsBrack/14901caeb99e7853f6a7e0706f1ce3eb) for a component called “infinite scrollable list” using no specific syntax or framework, just the opening, and closing tags. The element contains an attribute called "fetch" that make the request from the network and passes the result as a callback to the component.**

**如果组件本身不向服务器请求新数据，而是接受它作为一个属性，那么呈现组件进行测试将会更容易。如果组件的名字是正确的，并且封装得很好，它也将驱使你走向松耦合和高内聚的原则(参见[耦合](/@fagnerbrack/why-do-you-need-to-know-package-coupling-fundamentals-8e0fa8e33e20)和[内聚](/@fagnerbrack/why-do-you-need-to-know-package-cohesion-fundamentals-8a3510cba2c1)对于包意味着什么)。**

> **如果尽可能尝试构建纯函数是一个好的实践，那么对组件也是一个好的实践。**

**正如我去年提到的那样，网络有一种完全基于组件的趋势。我不确定谁会是赢家。可以是框架，可以是库，也可以是别的。有一点可以肯定的是，无论它是什么，它都必须实现这里介绍的同一种[接口](https://codeburst.io/why-do-you-need-to-know-interface-fundamentals-a129ac6ab0c3)。**

**功能是基本的，组件遵循相同的模式。它们也可以遵循相同的原则，就像它们是页面上下文中的函数一样。**

**如果你为了理解如何构建更好的组件而参考简单的功能——为此，我不是指核心的功能性[编程](https://hackernoon.com/tagged/programming)——你将能够创建具有高内聚和松耦合的弹性 web 应用程序，能够经受住时间的考验。**

**应用程序不必完全重写，因为有一个新的组件框架在做和其他人一样的事情。**

**感谢阅读。如果你有一些反馈，请在[推特](https://twitter.com/FagnerBrack)、[脸书](https://www.facebook.com/fagner.brack)或 [Github](http://github.com/FagnerMartinsBrack) 上联系我。**