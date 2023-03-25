# 简化的 JavaScript:提升

> 原文：<https://medium.com/hackernoon/simplified-javascript-hoisting-9128b74bb397>

![](img/f846996cedfd073345e2743ab15013f7.png)

#hoisting

提升仍然是 [JavaScript](https://hackernoon.com/tagged/javascript) 的古怪之处之一。当[开发者](https://hackernoon.com/tagged/developer)在 JavaScript 中声明一个变量时，那个变量*的行为*就好像它已经被提升到了它的可用范围的顶端。本文不打算关注在幕后究竟发生了什么导致了这种行为。相反，我希望提供一个对 JavaScript 新用户有意义的提升的简单解释。

# 在 ES5 中吊装

我花了大约一年半的时间没有真正理解起重。我只是坚持一个通用规则——我总是在文件的顶部声明和赋值我所有的变量。在 ES6 问世之前，它看起来类似于下面的代码:

```
var a = 1; 
var b = 2; 
var c = 3;var adder = function (a, b, c) { 
  return (a + b + c);
};adder(a, b, c); // Returns 6
```

上面的代码执行正确，当我运行它时返回数字 6。我在顶部声明并赋值我的变量，然后定义一个函数表达式，然后调用我的函数，传入我之前赋值的变量。

但是当我在顶部声明变量而没有赋值时，我得到了相同的返回值，就像这样:

```
var a;
var b;
var c;var adder = function (a, b, c) {
  console.log(a + b + c);
};a = 1;
b = 2;
c = 3;adder(a, b, c); // Returns 6
```

或者我可以在声明之前调用我的函数，我仍然会得到 6:

```
var a = 1;
var b = 2;
var c = 3;adder(a, b, c); // Returns 6function adder(a, b, c) {
  console.log(a + b + c);
};
```

这里有一个挑战。在您继续阅读之前，请考虑下面的代码，并尝试确定它的计算结果。

```
function hoist() {
  a = 1;
  var b = 2;
}

hoist();

console.log(a); 

console.log(b);
```

明白了吗？好吧，我把答案分享给你。

`console.log(a)`将返回`1`。另一方面，`console.log(b)`会带着``ReferenceError: b is not defined``回来。`a`和`b`都被提升到它们作用域的顶部，但是因为`b`是在`hoist()`函数内部声明的，所以它对全局作用域不可用。

# ES6 中的吊装

`let`和`const`介绍了一种不同的吊装方式。

## 让

我们从`let`开始。在下面的例子中，我试图记录`hoist`变量，然后声明并分配它:

```
console.log(hoist); // ReferenceError: hoist is not defined
let hoist = 'hoisted';
```

在这种情况下，我得到了一个`ReferenceError`。这与我写下`var hoist = 'hoisted';`时得到的结果不同。那样的话，我早就跟`undefined`结束了。

`var`和`let`抛出不同的错误，因为 JavaScript 初始化它们的方式不同。当`var`被声明时，JavaScript 将其设置为`undefined`。另一方面，`let`仍未初始化。这样，它抛出一个有用的错误，而不仅仅是返回`undefined`。

## 常数

让我们用`const`试试这个:

```
console.log(hoist); // ReferenceError: hoist is not defined
const hoist = 'hoisted';
```

像`let`一样，我们得到一个明确的错误，告诉我们`hoist`在被调用之前没有被定义。

# 功能

JavaScript 将提升函数声明，但不提升函数表达式。我将提供一些例子:

## 函数声明

如果一个函数被声明但没有被赋值给一个变量，它将被提升到其作用域的顶部。

```
hoister(); // hoistsfunction hoister() {
  console.log('hoists');
};
```

## 函数表达式

另一方面，函数表达式不会被提升，所以下面的代码返回一个`TypeError`。

```
hoister(); // TypeError: hoister is not a functionvar hoister = function () { 
  console.log('hoists'); 
};
```

希望这篇文章能帮助你更好地理解提升。当然，总有更多的东西要学。

如果你有任何问题，请在下面的评论中告诉我，如果你喜欢这篇文章，请鼓掌！

# 继续读

*   [理解 JavaScript 中的提升](https://scotch.io/tutorials/understanding-hoisting-in-javascript)
*   [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting)