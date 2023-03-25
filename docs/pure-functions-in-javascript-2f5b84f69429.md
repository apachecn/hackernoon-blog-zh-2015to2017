# JavaScript 中的纯函数

> 原文：<https://medium.com/hackernoon/pure-functions-in-javascript-2f5b84f69429>

本周，我决定承担[学习](https://hackernoon.com/tagged/learning)一个新的 [JavaScript](https://hackernoon.com/tagged/javascript) 库 React 的任务。我在学习 React 时发现的一件有趣的事情是它使用了函数式编程，更重要的是它使用了纯函数。

那么什么是纯函数，作为一名软件工程师，我为什么要关心呢？纯函数很重要，因为纯函数更容易推理，也更容易测试。可测试性是很重要的，因为当你写一个函数时，你想知道每次你给它一个输入，你的结果会是什么。

纯函数有两个重要的特征。

*   纯函数当给它们相同的输入时，它们总是返回相同的结果。
*   当你写一个纯函数时，函数不会修改超出其作用域的变量。

一些纯函数的例子有哪些？我们来看几个。

```
function subtract (a, b) {
  return a - b;
}function multiply (a, b) {
  return a * b;
}
```

虽然这些函数很简单，但这两个函数是纯函数，在给定相同输入的情况下，将总是返回相同的结果。即

```
subtract(6, 5);  // 1
multiply(10, 4) // 40
```

如果输入始终是 6 和 5，那么减法函数将始终返回 1；如果输入是 10 和 40，那么乘法函数将始终返回 40。纯函数没有副作用。

现在让我们看两个本地 JavaScript 方法。slice()和。拼接()。的。slice()方法是一个纯函数。拼接方法不是，因为它不会每次都返回相同的结果。

```
var cohorts = ["elevens", "twelves", "thirteens"];
 cohorts.slice(0,1) // "elevens"
 cohorts.slice(0,1) // "elevens"
 cohorts.slice(0,1) // "elevens" cohorts.splice(0,1) //["elevens"]
 cohorts.splice(0,1) //["twelves"]
 cohorts.splice(0,1) //["thirteens"]
```

正如您可以看到的，当您调用。切片方法，它是可预测和可测试的，因为它总是返回相同的结果。当您调用。用相同的参数拼接方法，你不会每次都得到相同的结果，因为它不是一个纯函数。