# 掌握 ES6:变量

> 原文：<https://medium.com/hackernoon/getting-to-grips-with-es6-variables-f27b72798bf3>

![](img/79ee1ea95cb0b783f134895fe4a11d8e.png)

## 第一部分:理解 var，let，const 和 scope

[*点击此处查看第二部分:了解胖箭头功能*](/@andrewjrhill/getting-to-grips-with-es6-arrow-functions-ebfa62c5c5d6#.3x4gbza5l)

## **前言**

这篇文章是为希望更好地理解 [ES6](https://hackernoon.com/tagged/es6) 变量的初级到中级开发人员设计的“入门”读物。

作为我的“掌握 ES6”系列的一部分，我的目标是创建一个参考，其中包含简单明了的解释，以帮助我们理解这些概念，并将它们直接应用到我们当前的开发过程中。

作者:安德鲁·希尔。
你可以在 [LinkedIn](http://www.linkedin.com/in/andrewjrhill) 、 [Twitter](https://twitter.com/andrewjrhill) 、 [Instagram](https://www.instagram.com/andrewshills/) 和 [GitHub](https://github.com/Sntax/) 上找到我。

# “var”语句

虽然`var`语句对 ES6 来说并不陌生，但我强烈建议通读这篇复习资料，以帮助我们更好地理解`const`和`let`语句引入的概念。

在其核心，`var`语句允许我们定义、更新和重定义变量。用`var`声明的变量被认为是*函数作用域。*

## **定义并更新**

我们可以定义和更新用`var`声明的变量，而不会有任何错误。

```
var test = 100;console.log(test);
// Output: 100test = 200;console.log(test);
// Output: 200
```

## **重定义(允许)**

我们还可以定义一个同名的全新变量来覆盖之前的变量，而不会出错。

```
var test = 100;console.log(test);
// Output: 100var test = 200;console.log(test);
// Output: 200
```

## **范围**

使用`var`定义的变量范围如下:

*   *函数作用域*如果它们被定义在一个函数内，使得它们只能在定义它们的函数内被访问。
*   *全局作用域*如果它们没有被定义在一个函数中，使得它们在任何地方都可以被访问。

考虑下面的例子:

```
function scopeTest() {
  var test = 100;
  console.log(test);
}scopeTest();
// Output: 100
```

`test`变量被认为是一个局部变量，函数作用于`scopeTest()`函数。我们能够访问存储在变量`test`中的值，因为变量声明和`console.log()`存在于同一个`scopeTest()`函数中。

使用此示例的修改版本，我们可以说明此变量在它所作用的函数之外是如何不可访问的:

```
function scopeTest() {
  var test = 100;
}scopeTest();console.log(test);
// Output: [Error] 'test' is not defined.
```

我们还可以在更高的范围内更新变量，以获取函数外部的值。由于超出本文*范围*的原因，这通常被认为是不好的做法，除非绝对必要，否则应该避免。

```
var test;function scopeTest() {
  test = 100;
}scopeTest();console.log(test);
// Output: 100
```

## **var 的“坏”侧**

那么为什么要在 ES6 中引入新的`const`和`let`语句呢？*因为任何使用* `*var*` *在函数外部定义的变量都是全局范围的。*

全球范围界定是**不好的**，因为它介绍了:

*   *命名冲突*——当你或你的团队无法跟踪变量名时，变量会被无意中重新定义，从而被覆盖。
*   *安全性减弱*——特别是在网络上，每个用户天生就可以访问全球范围。对此的访问允许用户查看和修改您的变量。
*   *性能差*——尽管可以忽略不计，但是如果在当前被调用的地方找不到变量，JavaScript 引擎会在更高的范围内寻找变量。如果您的变量都是全局范围的，引擎将始终需要找到全局窗口对象，以便找到它正在寻找的值。

# “let”语句

`let`语句允许我们像`var`语句一样定义和更新变量，但是它不允许我们重新定义已经在相同范围内定义的变量。用`let`定义的变量也被认为是*块范围的。*

## **定义并更新**

我们可以定义和更新用`let`声明的变量，而不会有任何错误。

```
let test = 100;console.log(test);
// Output: 100test = 200;console.log(test);
// Output: 200
```

## **重定义(不允许)**

禁止尝试定义一个与我们之前定义的`let`变量同名的新变量，这会导致错误。

```
let test = 100;console.log(test);
// Output: 100let test = 200;
// Output: [Error] Identifier 'test' has already been declared.console.log(test);
// Output: 100
```

# “常量”语句

`const`语句只允许我们定义变量。与被更新的`let`不同，`const`完全禁止更新变量。此外，像`let`，`const`一样，禁止重新定义已经在同一个作用域中定义的变量。用`const`定义的变量被认为是*块范围的。*

## **定义(允许)和更新(不允许)**

我们可以定义`const`变量，但禁止更新分配给变量的值。试图更新常量变量将导致错误。

```
const test = 100;console.log(test);
// Output: 100test = 200;
// Output: [Error] Assignment to constant variable.console.log(test);
// Output: 100
```

## **重定义(不允许)**

试图定义一个与我们之前定义的`const`变量同名的新变量是被禁止的，这会导致错误。

```
const test = 100;console.log(test);
// Output: 100const test = 200;
// Output: [Error] Identifier 'test' has already been declared.console.log(test);
// Output: 100
```

## **可变性**

有一种越来越多的误解认为`const`是完全不可变的，这意味着一旦将对象属性赋给了`const`变量，就永远无法更新它。当涉及到对象和`const`时，你不能重新定义`const`变量，但是你**可以**更新对象的属性。

在下面的例子中，我们试图给变量`test`分配一个新的对象。如前所述，这是不允许的，我们会遇到错误。

```
const test = {
  milliseconds: 1862,
  createdBy: 'Andrew Hill',
}test = {
  milliseconds: 1862,
  createdBy: 'Jane Doe',
} // Output: [Error] Assignment to constant variable.console.log(test);
// Output: Object { milliseconds: 1862, createdBy: 'Andrew Hill' }
```

然而，在这个例子中，我们能够毫无问题地更新`test`变量的属性。

```
const test = {
  milliseconds: 1862,
  createdBy: 'Andrew Hill',
}test.createdBy = 'Jane Doe';console.log(test);
// Output: Object { milliseconds: 1862, createdBy: 'Jane Doe' }
```

如果您正在寻找*【真正】*不可变的对象，看一看`[Object.freeze()](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)`方法，它防止添加新的属性，现有的属性被删除，并且防止现有的属性、它们的可枚举性、可配置性和可写性被改变。

# 范围:**常量和 let**

使用`const`和`let`定义的变量被认为是块范围的，这意味着它们在定义它们的代码块中是可用的。JavaScript 代码块是花括号内的任何东西`{}`。对象和函数都包含块，让块独立存在是完全正确的。

考虑下面的例子。注意，我们在下面的例子中使用了`let`，但是同样的规则也适用于`const`的作用域:

```
function scopeTest() {
  let test = 100;
  console.log(test);
}scopeTest();
// Output: 100
```

在这个例子中，`test`变量被认为是一个局部变量，其作用域是由`scopeTest()`函数创建的块。我们能够访问存储在变量`test`中的值，因为变量声明和`console.log()`存在于同一个`scopeTest()`功能块中。

使用这个例子的修改版本，我们可以说明这个变量在它所作用的功能块之外是如何不可访问的。

```
function scopeTest() {
  let test = 100;
}scopeTest();console.log(test);
// Output: [Error] test is not defined.
```

使用`const`和`let` 可以用相同的名称定义多个变量，只要它们不在同一个块中。这样做将导致两个完全不同的变量在不同的范围内存在相同的名称。

```
let test = 100;console.log(test);
// Output: 100function scopeTest() {
  let test = 200;console.log(test);
  // Output: 200
}console.log(test);
// Output: 100
```

# 最后的想法

那么什么时候应该用什么呢？在这个问题上有一些不同的观点，你可以在谷歌上快速搜索一下。最终，我选择以 Mathias Bynens 在他的文章中描述的方式使用它们:

*   默认情况下，对所有变量声明使用`const`。
*   仅在需要重新绑定时使用`let`。
*   `var`不应在 ES6 中使用

感谢阅读。如果您喜欢这篇文章，请考虑推荐它来支持本系列的未来安装。👏

[*点击此处查看第二部分:了解胖箭头功能*](/@andrewjrhill/getting-to-grips-with-es6-arrow-functions-ebfa62c5c5d6#.3x4gbza5l)