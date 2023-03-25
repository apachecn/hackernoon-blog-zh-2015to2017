# 声明函数

> 原文：<https://medium.com/hackernoon/declaring-functions-b9f85e7c74c6>

这是我的初级开发人员博客技术部分的第六部分。这是记录我在技术领域的工作。我来自一个非传统的编码背景，所以我努力工作，练习基础知识(总是编码)。我的博客有一半是关于技术主题的，另一半是关于平等、社会公正和技术进步的。

这次又短又甜。直奔主题。关于如何在 ES5 [JavaScript](https://hackernoon.com/tagged/javascript) 中声明[函数](https://hackernoon.com/tagged/functions)的一些基础知识。

![](img/4abf31ff04aa8a376ce0870205b4a673.png)

Be Like Neo

首先，JS 编译器从上到下读取你的代码。那么函数表达式和声明有什么区别。

## 函数表达式

函数声明和函数表达式之间的区别在训练营中没有被很好地教给我，提升和闭包也没有。因此，你可能不得不做或者应该做的是，重新学习或者自己学习这些基础知识，以便更深入地理解 JavaScript 的基础知识。让我们看一个例子。

```
**var** name **=** "Baggins";

(**function** () {
    *// Outputs: "Original name was undefined"*
    console.log("Original name was " **+** name); //Why??? 

    **var** name **=** "Underhill";

    *// Outputs: "New name is Underhill"*
    console.log("New name is " **+** name);
})();
```

这个例子取自于[一个 JavaScript](http://adripofjavascript.com/blog/drips/variable-and-function-hoisting.html) 的点滴。上面的例子包含一个[变量声明](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var)和一个[立即调用的函数表达式](http://adripofjavascript.com/blog/drips/an-introduction-to-iffes-immediately-invoked-function-expressions.html)。[函数表达式](http://adripofjavascript.com/blog/drips/an-introduction-to-iffes-immediately-invoked-function-expressions.html)要么将函数对象存储在变量内，要么写在括号或对象内。函数后面的名字没有定义的原因是因为提升。在这种情况下，立即调用的函数表达式是在将`"Baggins"`赋给`var name`之前定义的。因此，编译器读取代码的方式如下:

```
var name = "Baggins";(function (){
  console.log("Original name was " + name); 
  var name = "Underhill";
  console.log("New name is " + name);
}());console.log(name);
```

编译器会抛出:

```
Original name was undefined
New name was Underhill
Baggins
```

上面这个函数表达式的例子很特殊，因为它是被立即调用的。然而，它仍然是一个函数表达式，因此在提升的时候，它并不优先于函数声明。例如:

```
const myFunc = (value) => {
  console.log(value);
};
```

一个函数对象被存储在一个名为`myFunc`的变量中。假设你调用一个函数表达式，如下所示:

```
myFunc('hello');const myFunc = (str) => {
  console.log(str);
};
```

编译器抛出

```
TypeError: myFunc is not a function 
```

因为编译器读起来像

```
myFunc('hello'); // tries to call this function but not in memory, errors out
```

## 函数声明

ES5 和 ES6 中的函数声明是相似的。它只包含单词`function`作为语句的开头。

```
function myFunc(str) {
  console.log(str);
}
```

在提升过程中，函数声明优先，变量名优先。因此，上面记录单词`hello`的例子产生了一个错误。如果您使用函数声明，它会运行得非常好。

```
myFunc('Look Ma! You can call me anywhere');function myFunc(str) {
  console.log(str);
}
```

啊，JavaScript 的狂野世界。下次见！