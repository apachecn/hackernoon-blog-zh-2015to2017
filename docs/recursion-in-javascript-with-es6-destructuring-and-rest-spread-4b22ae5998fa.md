# ES6 JavaScript 中的递归、析构和 rest/spread

> 原文：<https://medium.com/hackernoon/recursion-in-javascript-with-es6-destructuring-and-rest-spread-4b22ae5998fa>

JavaScript(ECMAScript 6)的最新 ECMA 标准通过使用函数构造和新操作符鼓励更具声明性的风格，使 JavaScript 更具可读性。

# 解构

我最喜欢的 ES6 特性之一是**析构**。它允许您通过使用**结构**将数据从一个变量提取到另一个变量。例如，对于数组，这意味着:

```
var [ first, second ] = [ 1, 2, 3, 4 ];
// first: 1
// second: 2
```

你还可以做更多的事情，比如跳过运算右边数组的一些成员。

```
var [ first, , third, fourth ] = [ 1, 2, 3, 4 ];
// first: 1
// third: 3
// fourth: 4
```

这实际上很容易移植到等效的 ES5 中

```
var arr = [ 1, 2, 3, 4 ];
var first = arr[0];
var second = arr[1];
// etc ...
```

# 休息

这就是 ES6 特性变得更有趣的地方。通过析构，我们还可以分配数组的剩余部分**。我们用`...`符号表示**休止符**。**

```
var [ first, ...notFirst ] = [ 1, 2, 3, 4 ];
// first: 1
// notFirst: [ 2, 3, 4 ]
```

命名约定产生的代码更类似于以下代码:

```
var [ first, second, ...rest ] = [ 1, 2, 3, 4 ]
// first: 1
// second: 2
// rest: [ 3, 4 ]
```

`rest`操作符有一些有趣的属性:

```
var [ first, ...rest ] = [ 1 ]
// first: 1
// rest: []
```

它总是返回一个数组。这意味着即使在防御性的 JavaScript 领域，也可以在没有守卫的情况下做类似检查`rest`的`.length`这样的事情。

ES5(及以下)中的等价方法是使用`Array.slice`函数。

```
var arr = [ 1, 2, 3, 4 ];
var first = arr[0];
var rest = arr.slice(1);
// first: 1
// rest: [ 2, 3, 4 ]
```

这里要注意两件事:

*   ES5 版本更加冗长
*   ES5 版本更具命令性，我们告诉 JavaScript **如何**做某事，而不是告诉它**我们想要什么**。

现在我也觉得结构匹配版(带 rest)可读性更强。

# 参数析构

我们可以在函数定义的参数上使用析构:

```
function something([ first, ...rest ]) {
  return {
    first: first,
    rest: rest
  };
}
var result = something([1, 2, 3]);
// result: { first: 1, rest: [ 2,3 ] }
```

等效 ES5:

```
function something(arr){
  var first = arr[0];
  var rest = arr.slice(1);
  return {
    first: first,
    rest: rest
  };
}
```

同样，它更冗长，也更有命令性。

# 传播

Spread 使用与 rest 相同的符号:`...`。它的功能完全不同。

```
var arr = [ 1, 2, 3 ];
var newArr = [ ...arr ];
// newArr: [ 1, 2, 3]
```

ES5 当量:

```
var arr = [ 1, 2, 3 ];
var newArr = [].concat(arr);
```

需要注意的是，数组的内容是**复制的**。所以`newArr`不是对`arr`的引用。

我们还可以做一些事情，比如追加或者预先放置一个数组。

```
var arr = [ 1, 2, 3] ;var withPrepend = [ ...arr, 3, 2, 1];
var withAppend = [ 3, 2, 1, ...arr ];
// withPrepend: [ 1, 2, 3, 3, 2, 1]
// withAppend: [ 3, 2, 1, 1, 2, 3 ]
```

# 函数式编程:列表和递归

在函数式编程中，当我们在列表上递归运行函数时，我们喜欢将列表建模为头部和尾部。

头部是列表的第一个元素，尾部是列表减去头部后的列表。

```
arr = [ 1, 2, 3 ]
// head(arr): 1
// tail(arr): [ 2, 3 ]
```

在 ES6 中，我们可以通过用**析构**和 **rest:** 适当地命名变量来做到这一点

```
var [ head, ...tail ] = [ 1, 2, 3 ];
// head: 1
// tail: [ 2, 3 ]
```

我们也可以使用 ES6 轻松实现`head`和`tail`函数:

```
function head([ head, ...tail ]) { 
  return head;
}
function tail([ head, ...tail ]) {
  return tail;
}
// or with arrow function syntax
var head = ([ head, ...tail ]) => head;
var tail = ([ head, ...tail ]) => tail;
```

## (尾)递归

我们可以使用**参数析构** 和**递归**来实现操作数组(或列表，因为它们在函数式编程中更倾向于被调用)的函数。

例如，map 可以通过以下方式实现:

*Map 是一个函数，它接受一个列表和一个函数，并返回一个列表，该列表包含函数应用于列表中每个元素的结果。*

```
function map([ head, ...tail ], fn) {
  if(head === undefined && !tail.length) return [];
  if(tail.length === 0){
    return [ fn(head) ];
  }
  return [ fn(head) ].concat(map(tail, fn));
}
```

`tail.length === 0`检查是否还有一个尾部可以递归。否则，递归在此停止。

就内存使用和速度而言，这不一定是最有效的 map 版本，但它是 ES6 的一个很好的例子。

我们可以通过用`spread`操作符替换`concat`并使用带有三元操作符的单一返回语句来进一步简化它。

## 非常 ES6 地图

我们的 ES6 递归/析构映射可以简化为:

Map implementation using ES6, destructuring and recursion

或者如果我们想滥用 ES6，让自己忘记我们实际上是在做 JavaScript:

Map implementation using ES6, arrow function, destructuring and recursion

ES5 当量

Map implementation using ES5 and recursion

所有的特性加起来，虽然 ES6 中的递归映射本质上是一行程序，但在 ES5 中，它是一个笨重、冗长、难以阅读的函数。

## 重新实现列表操作函数

现在你可以使用上面的技术重新实现`filter`、`reduce`和`join`。

折下解:)。

ES6 允许我们用函数式风格更简洁有效地编写代码。

*给一些💚如果你喜欢这篇文章，请点击* ***关注*** *了解更多与 JavaScript 相关的内容。*

*你可以在 Twitter 上找到我*[*(at)Hugo _ _ df*](https://twitter.com/hugo__df)*，在 GitHub 上为*[*HugoDF*](https://github.com/HugoDF)*，这里还有更多 JavaScript gists*[](https://gist.github.com/HugoDF)**。**

## *ES6 中带有 rest/spread 和析构的递归列表操作*

*Filter implementation using ES6, destructuring and recursion*

*Reduce implementation using ES6, destructuring and recursion*

*Join implementation using ES6, destructuring and recursion*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*