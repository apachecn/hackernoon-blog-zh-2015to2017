# 高阶函数:幕后

> 原文：<https://medium.com/hackernoon/higher-order-functions-behind-the-scenes-5853179cfd9c>

![](img/283b0e2d9b95ba47e201231664beff82.png)

这篇文章不是向你展示如何使用`forEach`、`map`、`filter`和`reduce`、[和](https://hackernoon.com/tagged/nor)的，也不是展示高阶函数的好处的。有的是[其他的](/humans-create-software/a-dirt-simple-introduction-to-higher-order-functions-in-javascript-b33bf9e19056) [文章](/javascript-scene/higher-order-functions-composing-software-5365cf2cbe99) [那个](/functional-javascript/higher-order-functions-78084829fff4) [做](https://medium.freecodecamp.org/higher-order-functions-in-javascript-d9101f9cf528)那个。

当我想理解一个基本概念的时候，我喜欢尝试*自己实现*，哪怕是很基础的版本。人们以不同的方式学习，这对我很有用。所以让我带你踏上这段旅程，我试着在 [JavaScript](https://hackernoon.com/tagged/javascript) 中创建我自己的`forEach`、`map`、`reduce`和`filter`函数。

## myForEach

为了与内置的`forEach`区别开来，让我们称我的新函数为`myForEach`。

首先要做的是查看调用签名。你知道的 forEach 函数是这样做的:

```
let arr = [ 1, 2, 3, 4 ]// log each item of the array
arr.forEach(console.log)// double each item of the array and log it to the console
arr.forEach((val) => console.log(val * 2))
```

我们在寻找一个带两个参数的函数，一个数组和一个回调函数。回调函数可以是函数定义或匿名函数。我们要做的是迭代数组中的每一项，并调用该项的回调函数。所以让我们试一试。

```
function myForEach(arr, callback) {
  for (let i = 0; i < arr.length; i++) {
    callback(arr[i])
  }
}let arr = [ 1, 2, 3, 4 ]// log each item of the array
myForEach(arr, console.log)// double each item of the array and log it to the console
myForEach(arr, val => console.log(val * 2))
```

看起来很简单，而且很有效。但是…但是…不完全一样。如果你尝试`arr.myForEach(console.log)`，你会得到`TypeError: arr.myForEach is not a function`。这是因为我们将 myForEach 声明为一个全局函数。实际上，它应该是数组对象原型的一部分。注意本文的目的不是解释原型继承是如何工作的。为了实现我们想要的，我们需要这样做。

```
Array.prototype.myForEach = (callback) => {
  for (let i = 0; i < this.length; i++) {
    callback(arr[i])
  }
}// log each item of the array
arr.myForEach(console.log)
```

当我尝试运行上面的代码时，什么也没有发生。怎么了？

尝试使用箭头函数。箭头函数没有自己的`this`或`arguments`绑定。因此，当我在函数定义中调用`this`时，它并没有像我天真地期望的那样引用数组对象本身。这是正确的做法。这个 [StackOverflow 帖子](https://stackoverflow.com/questions/34361379/arrow-function-vs-function-declaration-expressions-are-they-equivalent-exch)有解释。

```
Array.prototype.myForEach = function (callback) {
  for (let i = 0; i < arr.length; i++) {
    callback(arr[i])
  }
}// log each item of the array
arr.myForEach(console.log)
```

太美了。既然我们已经了解了这些机制，我们可以继续讨论其他高阶函数了。

## 我的地图

签名如下。假设我们有一个数字数组，想要得到这些数字的平方数组。

```
let arr = [ 1, 2, 3, 4 ]let arrNew = arr.map(val => val * val)// or 
const squareNumber = n => n * n
let arrNew2 = arr.map(squareNumber)console.log(arrNew)
// outputs [ 1, 4, 9, 16 ]
```

所以我们应用于一个数组:一个接受回调的函数，并返回一个新的数组。回调是一个接受输入值的函数，通常将输入值转换成其他值。

```
Array.prototype.myMap = function(callback) {  
  let ret = []
  for (var i = 0; i < this.length; i++) {
    let newVal = callback(this[i])
    ret.push(newVal)
  }
  return ret
}
```

我们遍历数组(由`this`表示)，应用回调函数，并将结果值推送到一个新的数组上。并随后返回新数组。

我们可以使用新的`myForEach`函数更简洁地编写这个。

```
Array.prototype.myMap = function(callback) {  
  let ret = []
  this.myForEach(val => ret.push(callback(val)))
  return ret
}
```

继续前进。

## 我的过滤器

现在我们开始掌握它的窍门了。这是过滤器调用签名。

```
let people = [ 
  { 'name': 'Bob', 'age': 70 },
  { 'name': 'Sue', 'age': 30 },
  { 'name': 'Joe', 'age': 18 } ]let youngPeople = people.filter( person => person.age < 69 )
// returns array with two objects.
//  [ { 'name': 'Sue', 'age': 30 },
//  { 'name': 'Joe', 'age': 18 } ]// or
const isYoung = person => person.age < 59
let youngPeople = people.filter(isYoung)
```

因此，过滤器期望回调返回一个`true`或`false`，也称为谓词。该函数返回一个新数组。根据我们的经验，我们可以很快做出这个。

```
Array.prototype.myFilter = function(callback) {
  let ret = []
  this.myForEach( (val) => {
    if (callback(val) === true) ret.push(val)
  }
  return ret
}
```

相当直接。注意，我可以写`if (callback(val))`而不是`if (callback(val) === true)`，但是我这样做是为了强调我们期望回调返回`true`或`false`。

## myReduce

reduce 函数对于 grok 来说有点复杂。课本上的 reduce 例子是对一个数组的元素求和。这就是它看起来的样子。

```
let arr = [ 1, 2, 3, 4 ]let sum = arr.reduce( (acc, val) => acc + val )console.log(sum)
// prints out 10
```

回调函数有两个参数，第一个参数通常被称为累加器，第二个参数是数组中特定项的值。在这个例子中，累加器保存了一个运行总数，并且随着数组的每次迭代，它的值被添加到累加器中。最后累加器包含了我们想要得到的值。

这是这个函数最基本的形式。

```
Array.prototype.myReduce = function (callback) {
  let ret
  this.myForEach( val => {
    if (ret === undefined) ret = val
    else ret = callback(ret, val)
  }
  return ret
}
```

这比我们上面定义的其他高阶函数要稍微复杂一些。注意我们需要检查`ret`是否是`undefined`。这又引出了另一个问题。JavaScript reduce 函数允许您指定初始值。这不难添加到我们的功能中。我们只需在函数定义中添加一个 init 参数，并将`ret`变量初始设置为`init`。

```
Array.prototype.myReduce = function (callback, init) {
  let ret = init
  this.myForEach( val => {
    if (ret === undefined) ret = val
    else ret = callback(ret, val)
  }
  return ret
}const sumTwoValues = (a, b) => a + blet sum = arr.myReduce( sumTwoValues , 0 ) // added 0 as an argument for initial value.console.log(sum)
// prints 10
```

## 丰富

对于上面的每个函数，我们可以在回调签名中包含数组元素的索引，并使其在回调中可用。例如，这将允许我们执行以下操作。

```
var arr = [ 1, 2, 3, 4, 5, 6 ]arr.forEach( (val, idx) => { if (idx % 2 === 0) console.log(val) }
// prints the value of every other item in the array.arr.map( (val, idx) => idx % 2 === 0 ? val * 2 : val * 3 )
// create a new array where every even item has been doubled and every odd item has been trippledarr.map( (val, idx) => idx == 0) 
// return an array with only the first item 
```

实现相当容易。在 myForEach 函数中，我们将值和索引传递给回调函数。由于我们在所有其他函数中使用 myForEach，我们可以很容易地在回调调用中包含索引。

```
Array.prototype.myForEach = function(callback) {
  for (var i = 0; i < this.length; i++) {
    callback(this[i], i)
  }
}Array.prototype.myMap = function(callback) {
  let ret = []
  this.myForEach((val, idx) => ret.push(callback(val, idx)))
  return ret
}Array.prototype.myFilter = function(callback) {
  let ret = []
  this.myForEach((val, idx) => { if (callback(val, idx)) ret.push(val)} )
  return ret
}Array.prototype.myReduce = function(callback, init) {
  let ret = init
  this.myForEach( ( val, idx ) => {
    if (ret === undefined) ret = val
    else ret = callback(ret, val, idx)
  })
  return ret
}
```

我们应该做的另一件事是一些错误检查。例如，我们应该确保回调是一个函数。我们应该检查数组是否不为空。

```
Array.prototype.myForEach = function(callback) {
  if (this == null) {
    throw new TypeError('array is null')
  }
  if (typeof callback !== 'function') {
      throw new TypeError(callback + ' is not a function'
  }
  for (var i = 0; i < this.length; i++) {
    callback(this[i], i)
  }
}Array.prototype.myMap = function(callback) {
  if (this == null) {
    throw new TypeError('array is null')
  }
  if (typeof callback !== 'function') {
      throw new TypeError(callback + ' is not a function'
  }
  let ret = []
  this.myForEach((val, idx) => ret.push(callback(val, idx)))
  return ret
}Array.prototype.myFilter = function(callback) {
    if (this == null) {
    throw new TypeError('array is null')
  }
  if (typeof callback !== 'function') {
      throw new TypeError(callback + ' is not a function'
  }
  let ret = []
  this.myForEach((val, idx) => { if (callback(val, idx)) ret.push(val)} )
  return ret
}Array.prototype.myReduce = function(callback, init) {
  if (this == null) {
    throw new TypeError('array is null')
  }
  if (typeof callback !== 'function') {
      throw new TypeError(callback + ' is not a function'
  }
  let ret = init
  this.myForEach( ( val, idx ) => {
    if (ret === undefined) ret = val
    else ret = callback(ret, val, idx)
  })
  return ret
}
```

结束了。实际的 Javascript `forEach`、`map`、`filter`和`reduce`函数更加复杂。但是通过编写这个基本实现，您可以更好地理解它们是如何工作的。

如果你喜欢它，给我一些爱，点击下面的心。