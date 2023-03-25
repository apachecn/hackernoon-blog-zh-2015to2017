# 初学者的部分应用

> 原文：<https://medium.com/hackernoon/partial-application-for-beginners-1cd355f8f5bc>

![](img/341b0df57e52d6f70efb1d2a83036dae.png)

*注:此为本人* [*个人网站*](https://btnwtn.com/articles/partial-application-for-beginners) *原帖。*

# 为什么使用部分应用程序？

[局部应用](https://hackernoon.com/tagged/partial-application)是写干巴巴(不要重复自己)又有表现力的代码的一个核心概念。您可以使用部分应用程序来编写和创建新的函数，并编写如下代码:

```
function sortFilesByName() {
  /* … */
}function sortFilesByType() {
  /* … */
}function sortFilesByFilesize() {
  /* … */
}function sortFilesByDate() {
  /* … */
}<button onClick={() => sortFilesByName()}>Name</button>
<button onClick={() => sortFilesByType()}>Type</button>
<button onClick={() => sortFilesByFilesize()}>Filesize</button>
<button onClick={() => sortFilesByDate()}>Date</button>
```

变成这样:

```
function sortFilesBy(prop) {
  /* … */
}<button onClick={sortFilesBy('name')}>Name</button>
<button onClick={sortFilesBy('type')}>Type</button>
<button onClick={sortFilesBy('filesize')}>Filesize</button>
<button onClick={sortFilesBy('date')}>Date</button>
```

# 了解局部应用

部分应用是一个计算机科学术语，描述将一些参数固定到一个函数，然后返回一个较小 arity 的函数的过程。那么这到底意味着什么呢？

# 公式

在 Javascript 中，我们可以用几种方式定义函数。我们可以声明它们:

```
function add(x, y) {
  return x + y
}console.log(add)
//=> ƒ add(x, y) { return x + y }
```

我们可以通过将它们定义为表达式(函数表达式)来使用它们作为值:

```
const add = function (x, y) {
  return x + y
}console.log(add)
//=> ƒ (x, y) { return x + y }
```

函数表达式使我们能够像传递其他值一样传递函数。这意味着您可以将一个函数作为参数传递给另一个函数(例如`map(function () {})`)，并从一个函数返回一个函数:

```
const fn = function () {
  return function (x) {
    return x
  }
}fn()
//=> ƒ (x) { return x }fn()(1)
//=> 1
```

这是 Javascript 的一个核心特性，它使我们能够编写出非常干净灵活的代码。

# Arity

Arity 是一个描述函数接受的参数数量的术语。接受零参数的函数是零实参的函数。接受一个参数的函数是一个 arity 的函数，两个参数的 arity 是二，以此类推。

我们可以通过使用`[Function.length](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/length)`来确定函数的 arity。

```
add.length
//=> 2(function (x, y) { return x + y }).length
//=> 2(function (x) { return x }).length
//=> 1(function () {}).length
//=> 0
```

# 关闭

我们知道可以用函数表达式从函数中返回一个函数。我们可以利用这种行为，从返回它们的函数中返回“记住”值的函数。听起来令人困惑，但实际上有点简单:

```
const add = function add() {
  const x = 1 return function innerFunction() {
    return x
  }
}add()
//=> ƒ innerFunction() { return x }add()()
//=> 1
```

内部函数(`innerFunction`)从外部函数传递`x`，并记住该值。这个内部函数被称为闭包，因为它从外部`add`函数的环境中访问数据。我们可以重写`add`来返回一个接受另一个变量的闭包:

```
const add = function add() {
  const x = 1 return function innerFunction(y) {
    return x + y
  }
}add()
//=> ƒ innerFunction(y) { return x + y }add()(4)
//=> 5add()(1)
//=> 2add()(0)
//=> 1
```

如果我们把`x`作为一个参数，去掉`innerFunction`的名字，我们得到:

```
const add = function add(x) {
  return function (y) {
    return x + y
  }
}add(1)(1)
//=> 1add(0)(0)
//=> 0
```

# 把它放在一起

> *部分应用是将一些参数固定到一个函数上，然后返回一个更小 arity 的函数的过程。*

因为上面的`add`函数返回一个函数，所以我们可以用一个参数将一个变量赋给调用`add`的结果:

```
const add5 = add(5)
//=> ƒ (y) { return x + y }add5(10)
//=> 15
```

函数`add5`调用`add`，将值`5`固定(绑定)到`x`。`add` 返回内部函数，用固定值`ƒ (y) { return 5 + y }`替换`x`。

当我们这样做的时候，我们已经用*部分应用了*函数`add`。它产生了一个函数(`add5`)，我们可以用不同的方式使用它。

现在我们有了一个接受一个参数的函数，我们可以将这个函数传递给高阶函数(将函数作为参数的函数)，比如`map`:

```
[1, 2, 3].map(add5)
//=> [6, 7, 8]// same as:
[1, 2, 3].map(function (y) { return 5 + y })
```

虽然`add`和`add5`示例很好地解释了部分应用的“内容”和“方式”,但它们未能说明“为什么”。让我们继续看一些真实世界的例子。

# 真实世界的例子

让我们来看看本文开头的例子:

```
function sortFilesByName() {
  /* … */
}function sortFilesByType() {
  /* … */
}function sortFilesByFilesize() {
  /* … */
}function sortFilesByDate() {
  /* … */
}<button onClick={() => sortFilesByName()}>Name</button>
<button onClick={() => sortFilesByType()}>Type</button>
<button onClick={() => sortFilesByFilesize()}>Filesize</button>
<button onClick={() => sortFilesByDate()}>Date</button>
```

假设我们有一个请求，要求通过一个新字段添加排序:“Last Modified”。我们可以将此作为重构和简化代码的机会，而不是添加一个新函数`sortFilesByLastModified`。

首先，让我们将这些函数分组到一个普通对象中，该对象的键将表示我们要排序的属性，值将是接受要过滤的文件列表的函数:

```
const SortBy = {
  name: function (files) {
    // sort files
    return sortedFiles
  }, size: function (files) {
    // sort files
    return sortedFiles
  }, /* etc. */
}
```

我知道我想创建一个单一的通用函数，而不是许多`onClick`处理程序。这个函数应该接受一个属性来过滤并返回一个[事件处理程序](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Event_handlers):

```
function sortFilesBy(property) {
  return function (event) {
    // ???
  }
}
```

我们可以像这样使用这个函数:

```
<button onClick={sortFilesBy('name')}>Name</button>
<button onClick={sortFilesBy('type')}>Type</button>
<button onClick={sortFilesBy('filesize')}>Filesize</button>
<button onClick={sortFilesBy('date')}>Date</button>
```

当使用这个函数时，我们将`property`绑定为作为参数传入的任何内容，并返回一个`onClick`可以触发的事件处理程序。现在我们需要对文件进行分类:

```
function sortFilesBy(property) {
  return function (event) {
    const sort = SortBy[property]
    //=> ƒ (files) { return sortedFiles } this.setState(function (state) {
      return {
        files: sort(state.files)
      }
    })
  }
}
```

因为`property`被固定为我们提供给`sortFilesBy` 函数的任何值，我们可以用它从`SortBy`对象中查找我们的排序函数。这个例子部分应用了`sortFilesBy`函数来产生一个事件处理程序，它根据传递给`property`的内容对文件进行排序。

这里的好处是，我们可以向`SortBy`对象添加和移除函数，而不是向我们的 React 组件(或其他组件)添加新函数。这减少了组件的代码行数。更好的是，它将过滤和管理状态的逻辑从组件中分离出来。`SortBy`现在可以在整个代码库中重用，而以前它是紧密集成到特定组件中的。

# 结论

这只是部分应用函数的众多好处之一。部分应用使编写[可组合函数](https://leanpub.com/javascriptallongesix/read#leanpub-auto-compose-and-pipeline)变得容易，代码简洁，并且有其他用途。我鼓励你在自己的项目中找到可以受益于这种技术的代码！