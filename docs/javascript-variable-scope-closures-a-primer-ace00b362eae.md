# Javascript 变量范围和闭包:初级读本

> 原文：<https://medium.com/hackernoon/javascript-variable-scope-closures-a-primer-ace00b362eae>

![](img/9608cd83b8214c847d360844ea335256.png)

对于大多数人来说，Javascript 可能看起来是一种非常容易理解的编程语言，但是某些概念对于初学者来说可能非常棘手。最常提起的话题之一是**变量作用域**以及什么是**闭包**，所以在这篇文章中，我将尝试以一种对初学者友好的方式介绍这些概念。

## 范围

当我们谈论**范围**时，我们指的是一个或多个实体对我们代码的某些部分的可见性。出于本文的目的，我们的实体将限于变量。

Javascript 中有两种类型的作用域:**全局**和**局部**。

**全局作用域**非常简单:在任何函数或花括号之外定义的任何变量都是全局作用域的一部分，可以从代码中的任何地方访问。

**局部范围**稍微复杂一点。在`function`声明或花括号中定义的任何变量只能在分别声明它的函数或代码块中访问。

好了，这基本上就是你需要知道的关于变量作用域的所有内容。让我们看一些简单的例子:

An example of how global scope works

An extensive example of how function scope works

An extensive example of how block scope works

使用`var`和`let`的块范围和定义起初可能看起来有点混乱，但是我建议您阅读[使用 let 和 const](https://medium.freecodecamp.org/what-is-variable-hoisting-differentiating-between-var-let-and-const-in-es6-f1a70bb43d) 的 Javascript 变量提升指南(3 分钟阅读),并仔细研究 [MDN 的文档中的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let) `[let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)` [关键字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)以澄清任何遗留的问题和疑问。

[](https://medium.freecodecamp.org/what-is-variable-hoisting-differentiating-between-var-let-and-const-in-es6-f1a70bb43d) [## JavaScript 变量提升指南🚩带字母和常量

### 新的 JavaScript 开发人员通常很难理解变量/函数提升的独特行为。

medium.freecodecamp.org](https://medium.freecodecamp.org/what-is-variable-hoisting-differentiating-between-var-let-and-const-in-es6-f1a70bb43d) 

## 关闭

闭包是那些乍看起来令人困惑的事物之一，但实际上并非如此。定义在另一个函数内部的函数是闭包。当您想从函数外部访问函数范围内定义的变量时，闭包特别有用。

闭包的一个实际应用是定义一组公共函数，这些函数可以在函数中获取或设置一些私有变量(这是面向对象编程中一个非常常见的用例)。下面是一个非常简单的闭包例子:

Using closures to define public getter/setter functions for private variables

## 总结

**作用域**指变量的可访问性。它可以是公共的(定义在任何函数和花括号之外，可以在代码中的任何地方访问)，也可以是局部的(定义在函数或块内，只能在所述函数或块内访问)。**闭包**是定义在其他函数内部的函数，可以用来从函数自身的作用域之外访问函数的私有变量。