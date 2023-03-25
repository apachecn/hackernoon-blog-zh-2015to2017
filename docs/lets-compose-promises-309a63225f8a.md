# 让我们写下承诺！

> 原文：<https://medium.com/hackernoon/lets-compose-promises-309a63225f8a>

今天我们将结合 JavaScript 开发人员工具箱中最有用的两个工具。[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)和[功能组成](/javascript-scene/master-the-javascript-interview-what-is-function-composition-20dfb109a1a0)。

![](img/314dbf0224478626aa9b5ecb6553cb13.png)

Please enjoy this barely related comic about Functional Programming :)

## 承诺

[Promise API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 是一种简单但强大的处理异步操作的方式。要创建一个承诺，你可以输入`new Promise()`，唯一的参数是一个回调函数。

回调函数接受两个参数:`resolve`和`reject`。当异步函数完成时，调用 resolve 并给出结果。如果你的异步函数抛出一个错误，你可以用这个错误调用`reject`。看起来是这样的:

```
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('Hello World');
  }, 2000);
});myPromise.then(res => console.log(res)); // 'Hello World' is shown after 2 seconds.
```

我们能够通过调用 [Promise.prototype.then](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) 来访问`resolve`的结果，该函数接收一个回调函数，该函数将您调用的`resolve`作为其参数。

有时候一个承诺会抛出一个错误。在这个场景中，我们不能使用传统的 [try/catch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch) 块，因为错误可能会在稍后被抛出。相反我们使用[promise . prototype . catch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)。就像`.then()`它接收一个回调函数。不同之处在于，它将接收您传递给`reject`参数的任何内容。

最后，我们可能要做一些清理工作。我们用什么？你猜对了:[promise . prototype . finally](https://developers.google.com/web/updates/2017/10/promise-finally)。最终接受一个回调函数，但是这个回调函数不接受任何参数。您可以指望这个函数在您的承诺链执行完毕后被调用。在撰写本文时,`finally`并不是到处都有。你可以在最新版本的 Chrome 或者[蓝鸟](http://bluebirdjs.com/docs/api/finally.html)中使用。

## 连锁承诺

当处理承诺时，你可以把它们锁住。这意味着当一行中有多个异步选项时，可以一个接一个地执行。它看起来像这样:

```
// In this example Promise.resolve represents any async action that returns a promise.
new Promise(resolve => setTimeout(() => resolve(10), 3000))
  .then(res => Promise.resolve(res + 10))
  .then(res => Promise.resolve(res + 10))
  .then(console.log)
  .catch(console.error)  
  .finally(() => console.log('All done!');
// After 3 seconds logs 30 
// Then it logs 'All done!'
```

正如您所看到的，承诺可以被链接起来，这样异步操作就可以像同步操作一样发生。

我们使用 [Promise.resolve](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve) 来表示一个返回承诺的函数。`Promise.resolve`在承诺中包装一个值。

这是一种不错的承诺方式，但我认为我们可以做得更好。

## 作文

函数组合很简单，但是一开始可能很难理解。组合的全部意义在于允许你将函数串在一起。如果我们看一下上一个例子，但是使用同步函数，它看起来会像这样:

```
const result = compose(
  res => res + 10,
  res => res + 10,
)(10);
console.log(result); // 10
```

我们取每个函数的结果，传递给下一个函数，这就是整个组合函数的结果。

那个`compose`函数不像`Promise`或`Promise.resolve`那样被假定为全局函数。我们必须给它下定义。要定义 compose，我们需要这样的东西:

```
const compose = (...functions) => 
  initialValue =>
    functions.reduceRight(
      (sum, fn) => fn(sum),
      initialValue,
    );
```

因此..这到底是怎么回事？让我一步一步地走你想过的路。这可能会令人困惑，尤其是如果你不熟悉 JavaScript 的最新更新版本 [ES6](http://es6-features.org) 。

首先，我们接受任意数量的函数。然后我们使用[扩展操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)将所有这些函数聚集到一个数组中。

接下来，我们接受我们的 compose 函数将使用的初始值作为它的初始参数。

一旦我们有了初始值，我们开始从右到左(或者从下到上，如果你看上面的例子)调用每个函数。我们使用[array . prototype . reduce right](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/ReduceRight)来确保这些函数按照这个顺序被调用。如果你熟悉 [Array.prototype.reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 的工作方式，那么你已经熟悉 reduceRight，只是你可能不知道而已！`reduceRight`是 reduce，但它反向工作。

Array reduce 和 reduceRight 方法遍历一个数组，在数组中的每个索引处调用一个回调。该回调接收两个参数。求和，以及数组当前索引处的值。

在我们的例子中，当前索引的值是一个函数。总和是数组中第一个函数的`initialValue`。在那之后，求和是前一个函数的结果，这个函数是用它之前的函数的结果调用的。

所以现在我们希望看到承诺的力量，写作的方便和清晰。当我们把它们放在一起时会发生什么？

## 许下承诺

你可能想知道为什么我们不能用我们刚刚创建的`compose`函数来撰写承诺。问题是我们通过 [Promise.then](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) 访问那个值。所以除非我们的每个函数都解开了它的参数，比如:

```
function myComposedFunction(argument) {
  argument.then(() => {
    // actual content of myComposedFunction goes here
  });
}
```

那么函数的参数就不会是它所期望的。另外还有一个问题。现在`myComposedFunction`只会用承诺来工作！我们可以相当简单地解决那个问题。

```
function myComposedFunction(argument) {
  Promise.resolve(argument).then(() => {
    // actual content of myComposedFunction goes here
  });
}
```

但是这看起来像是一大堆样板文件。特别是如果这是一个简单的函数。也许确实如此:`return argument + 10;`我们刚刚把一个非常简单的函数变成了一个非常复杂的函数。

更好的方法是在`compose`函数本身中处理这个问题。

```
const composePromise = (...functions) =>
  initialValue =>
    functions.reduceRight(
      (sum, fn) => Promise.resolve(sum).then(fn),
      initialValue
    );
```

这个`composePromise`函数与前面的`compose`函数完全一样，只有一个主要区别。它接受承诺，并返回承诺。你可以这样使用它:

```
const add100ToNumberString = composePromise(
  console.log,
  res => res.toString(),
  res => Promise.resolve(res + 100),
  res => Promise.resolve(Number(res)),
);add100ToNumberString(new Promise(resolve => {
  setTimeout(() => {
    resolve('400');
  }, 2000);
})); 
// Eventually prints out '500' after 2 seconds
```

可以继续链，因为`add100ToNumberString`的结果是一个承诺。如果需要，你也可以使用`.catch()`和`.finally()`！

## 你自己试试！

下面我嵌入了一个 [CodePen](https://codepen.io/) 游乐场，里面有所有这些代码，你可以自己尝试一下！打开控制台查看结果。

嗨，我是贾斯汀·富勒。很高兴你看了我的帖子！我需要让你知道，我在这里写的一切都是我自己的观点，并不代表我的雇主。所有代码样本都是我自己的，与美国银行的代码完全无关。

我也很乐意收到您的来信，请随时通过 [LinkedIn](https://www.linkedin.com/in/justin-fuller-8726b2b1/) 、 [Github](https://github.com/justindfuller) 或 [Medium](/@justindanielfuller) 与我联系。再次感谢阅读！