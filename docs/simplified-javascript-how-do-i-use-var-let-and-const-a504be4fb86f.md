# 简化的 JavaScript:如何使用 var、let 和 const？

> 原文：<https://medium.com/hackernoon/simplified-javascript-how-do-i-use-var-let-and-const-a504be4fb86f>

![](img/ca98641569d2856abaf48fbfccea7a97.png)

*在阅读这篇帖子之前，先考虑阅读一下我早先关于* [*吊装*](https://hackernoon.com/simplified-javascript-hoisting-9128b74bb397)T22[*闭包*](https://code.likeagirl.io/simplified-javascript-getting-started-with-closures-f40f65317d00) *的帖子。他们可能会给你一些理解这篇文章所需的背景信息。*

在我开始学习 JavaScript 的几个月后，我第一次偶然发现了 ES6。当我意识到语法已经改变时，我感到很沮丧。(这是在我涉足 JavaScript 框架生态系统之前)。我不明白为什么 JavaScript 的语法需要更新。使用`var`我几乎没掌握，突然要学`let`和`const`？

我没抓住重点。`let`和`const`的行为与`var`不同。将它们添加到 JavaScript 中，可以让我们编写更富表现力、更优雅、更简洁的代码。

我希望有人能给我一个简单的解释，说明这三种声明局部变量的方式之间的区别。所以我为你写了一首！

# 定义变量

在 ES5 中，开发者使用`var`声明局部变量。`var`作用于最近的功能块。这意味着下面的代码不会出错。相反，它将返回 5。

```
const arr = [1, 2, 3, 4, 5];for (var i = 0; i < arr.length; i++) { 

};console.log(i) // 5
```

这是因为`i`的作用域是最近的函数块，而不是最近的封闭块，所以`console.log(i)`可以访问它。

更重要的是，`var` **可以在它的初始定义后被重新分配。这意味着我可以像分配`var language = "JavaScript"`一样分配一个[变量](https://hackernoon.com/tagged/variable)，然后在我的程序中，我可以将`var language`改为`var language = "Ruby"`。**

# 让

`let`在 ES6 中推出。它类似于`var`，因为它可以被重新分配，但是它的作用域是最近的封闭块，而不是最近的函数块。

那和`var`不一样。如果我们使用上面的同一个例子，但是用`let`代替`var`，我们会看到一个错误。

```
const arr = [1, 2, 3, 4, 5];for (let i = 0; i < arr.length; i++) {
};console.log(arr[i]); // ReferenceError: i is not defined
```

发生这种情况是因为`i`的作用域是`for`循环，而不是功能块。

当我定义一个我知道要重新赋值的变量时，用 Let 很好。

# 常数

当我不打算给一个变量重新赋值时，我使用`const`。让我们再看一次我们的例子:

```
const arr = [1, 2, 3, 4, 5];for (let i = 0; i < arr.length; i++) { 
  console.log(i) 
};// 1 2 3 4 5
```

在上面的例子中，`i`每次通过`for`循环时都会被重新分配。而`arr`是从来不重新分配的，所以我用了`const`。

# 经验法则

既然`let`和`const`已经存在，我会完全避免使用`var`。使用`let`和`const`可以让你清楚地表达你的意图。表现力将导致更干净、更优雅的代码。

# 更多阅读

*   [JavaScript ES6+: var，let，还是 const？](/javascript-scene/javascript-es6-var-let-or-const-ba58b8dcde75)
*   [堆栈溢出发布](https://stackoverflow.com/questions/762011/whats-the-difference-between-using-let-and-var-to-declare-a-variable)
*   [简化 JavaScript:提升](https://hackernoon.com/simplified-javascript-hoisting-9128b74bb397)