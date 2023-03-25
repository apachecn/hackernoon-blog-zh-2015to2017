# 我向你保证一个奇迹

> 原文：<https://medium.com/hackernoon/i-promise-you-a-miracle-396301751c4b>

## 我正在滚动我自己的 JavaScript 承诺。

![](img/06e59c4755aab7950424adb8a94d6c9a.png)

有时候，当你真的想理解一件事的时候，尝试自己去构建它是值得的。这就是我在这篇文章中对承诺所做的。我试图建立一个非常基本的“承诺”。

## 一开始有试镜…

让我们从最基本的开始，在任何人听说承诺之前，我们有过回调。看看下面的片段。

```
const testUrl = '[https://api.github.com/users/odemeulder](https://api.github.com/users/odemeulder)'
const fetchData = (url, callback) => {
  let xhr = new XMLHttpRequest()
  xhr.onreadystatechange = () => {
    if (xhr.readyState == 200 ) { 
      callback(xhr.responseText)
    }
  }
  xhr.open(url)
  xhr.send()
}
fetchData(testUrl, response => console.log(response) )
```

它是做什么的？它创建了一个非常简单的[函数](https://hackernoon.com/tagged/function)，该函数获取一个 url，使用`XMLHttpRequest`读取它，并允许回调函数对结果做一些事情。在最后一行，我们调用新函数。作为回调，我们提供了一个打印响应的函数。很简单。如果你对`XMLHttpRequest`不熟悉，不要苦恼，对于文章的其余部分理解并不重要。只知道它是浏览器提供给 [JavaScript](https://hackernoon.com/tagged/javascript) 执行`http` 请求的对象。这是一个异步函数，它在`onreadystatechange` 事件触发时接受回调。

## 承诺是什么样子的？

在开始写我们自己的承诺之前，让我们看看最终的结果应该是什么样的。什么是呼叫签名？

```
function fetchDataReturningPromise(url) {
  // what happens here?
}function functionWithDelay(arg) {
  // what happens here?
}fetchDataReturningPromise(url).then(response => console.log(response))
// or
functionWithDelay(arg1).then(performFollowUpOperation)
// or
functionWithDelay(arg1).then((resolvedValue) =>
  PerformFollowUpOperation(resolvedValue))
```

这是什么？让我们来关注第三个例子。

我们有一个延迟函数。函数内部的某个东西进行了异步调用。可能是超时、http 请求、数据库调用。

那个函数返回“某物”。那个某物有一个`then` 方法，所以‘某物’一定是一个对象。事实上,“某物”将是一个函数。

`then` 函数以函数或回调的形式接受一个参数。传递给`then` 函数的回调直到`functionWithDelay` 完成后才会执行。在我们的第三个例子中，传递给`then`的函数被标记为`performFollowUpOperation`。请注意，`performFollowUpOperation`接受一个参数(`resolvedValue`)，该参数由`functionWithDelay`提供。这里有很多东西要打开。暂停，重读上一段(如果你愿意的话)。

总而言之:

1.  用 next 函数定义一个函数。

2.定义一个具有延迟的函数，该函数返回一个具有下一个函数的函数(`functionWithDelay`)

3.带`next` 函数的函数需要带另一个函数作为参数。在延迟功能完成之前，不能执行该后续功能。(`performFollowUpOperation`)

4.具有延迟的函数必须以某种方式向后续函数传递一个值。

所以现在我们需要填充`functionWithDelay`，首先我们需要定义那个有`next` 功能的东西。

## 第一次尝试

让我们做第一次尝试。

```
 1: function FunctionWithAThenFunction() {
 2:  this.then = function (followUpFunction) {
 3:     followUpFunction()
 4:   }
 5: } 6: function functionWithDelay(param1) {
 7:   let xhr = new XMLHttpRequest()
 8:   xhr.onreadystatechange = () => {
 9:     if (xhr.readyState == 200) callback(xhr.responseText)
10:   }
11:   xhr.open(param1)
12:   xhr.send()
13:   return new FunctionWithAThenFunction()
14: }
```

好的，我们有一个带有`then`函数的函数。我们的`functionWithDelay` 用`then`函数返回一个函数。酪 9 号线`callback`是什么？不知何故，T8 应该在回调中结束。这是不可能的。因此，`followUpFunction` 将立即执行，而不是等到`functionWithDelay` 完成。

我们如何让那个`followUpFunction` 成为`callback`？

## 第二次尝试

我们来看看`functionWithDelay`。延迟函数的特征是什么？或者说异步函数的特征是什么？他们通常会以某种方式进行回调。因此，让我们试着将该函数重写为接受回调，这样我们就可以轻松地访问回调。

然后让我们重写我们的`FunctionWithAThenFunction` ,让它接受一个函数，更确切地说是一个带回调的异步函数。

```
1: function FunctionWithAThenFunction(aFnWithDelayAndACallback){
 2:   let nextThingToDo
 3:   this.then = function ( followUpFunction) {
 4:     nextThingToDo = followUpFunction
 5:   }
 6:   aFnWithDelayAndACallback( nextThingToDo )
 7: }
 8: function functionWithDelay(param1) {
 9:   const httpRequestWithCallback = function (callback) {
10:     let xhr = XMLHttpRequest()
11:     xhr.onreadystatechange = () => {
12:       if (xhr.readyState == 200) callback(xhr.responseText)
13:     }
14:     xhr.open(param1)
15:     xhr.send()
16:   }
17:   return new FunctionWithAThenFunction(httpRequestWithCallback)
18: }
19: functionWithDelay('some argument').then(console.log)
```

第 1–7 行定义了我们的`FunctionWithAThenFunction`。这是延迟函数返回的函数。它需要一个函数作为参数。更确切地说是带有回调的函数。然后，`then`函数在一个局部变量中设置回调函数(`nextThingToDo`)。这个函数实际上做了一件事，那就是用回调函数调用这个函数，在我们的例子中是标记为`aFnWithDelayAndACallback`的函数。

第 8–18 行定义了我们的`functionWithDelay`。在我们的例子中，这个函数执行一个`http` 请求，并使用一个`then`函数返回一些东西。具有`then`功能的东西是`FunctionWithAThenFunction`的新实例。它看起来和我们上面做的有点不同。我们创建一个新的名为`httpRequestWithCallback`的本地临时函数，它接受一个回调参数，并包装前面示例中的整个`XMLHttpRequest` 代码。然后，这个新的局部函数作为参数传递给`FunctionWithAThenFunction`。

现在，如果您尝试运行这个代码(第 19 行)，它仍然不能完成我们想要做的事情。如果你继续下去，你会看到在我们有机会给它赋值之前，我们称之为`nextThingToDo` 的是`undefined` 。我们缺少一样东西，那就是某种地位。

## 第三次尝试

让我们添加一个跟踪执行状态的变量。

```
1: function FunctionWithAThenFunction(aFnWithDelayAndACallback){
 2:   let nextThingToDo
 3:   let status = 'pending'
 4:   this.then = function ( followUpFunction) {
 5:     if (status === 'pending') {
 6:       nextThingToDo = followUpFunction
 7:     }
 8:     else {
 9:       followUpFunction()
10:     }
11:   }
12:   aFnWithDelayAndACallback( (newValue) => { 
13:      status = 'resolved'
14:      nextThingToDo(newValue)
15:   })
16: }
17: function functionWithDelay(param1) {
18:   const httpRequestWithCallback = function (callback) {
19:     let xhr = XMLHttpRequest()
20:     xhr.onreadystatechange = () => {
21:       if (xhr.readyState == 200) callback(xhr.responseText)
22:     }
23:     xhr.open(param1)
24:     xhr.send()
25:   }
26:   return new FunctionWithAThenFunction(httpRequestWithCallback)
27: }
28: functionWithDelay('some argument').then(console.log)
```

您会看到我们声明了一个`status` 变量(第 3 行)。这样我们就覆盖了所有的基础。如果在主函数完成之前调用了`then`，我们将`followUpFunction` 存储在一个变量中，如果没有，我们可以安全地执行它。但是它没有主函数的值。让我们解决这个问题。

```
1: function FunctionWithAThenFunction(aFnWithDelayAndACallback){
 2:   let nextThingToDo, value
 3:   let status = 'pending'
 4:   this.then = function ( followUpFunction) {
 5:     if (status === 'pending') {
 6:       nextThingToDo = followUpFunction
 7:     }
 8:     else {
 9:       followUpFunction(value)
10:     }
11:   }
12:   aFnWithDelayAndACallback( (newValue) => { 
13:      status = 'resolved'
14:      value = newValue
15:      nextThingToDo(newValue)
16:   })
17: }
18: function functionWithDelay(param1) {
19:   const httpRequestWithCallback = function (callback) {
20:     let xhr = XMLHttpRequest()
21:     xhr.onreadystatechange = () => {
22:       if (xhr.readyState == 200) callback(xhr.responseText)
23:     }
24:     xhr.open(param1)
25:     xhr.send()
26:   }
27:   return new FunctionWithAThenFunction(httpRequestWithCallback)
28: }
29: functionWithDelay('some argument').then(console.log)
```

这里我们声明一个名为`value`的新局部变量。(第 2 行)当我们解析 main 函数时，我们设置这个值。因此它可以作为参数传递给第 9 行的`followUpFunction`。

说到解决，为了清楚起见，让我们添加重写这个。与以前的示例相比，功能没有变化。

```
1: function FunctionWithAThenFunction(aFnWithDelayAndACallback){
 2:   let nextThingToDo, value
 3:   let status = 'pending'
 4:   this.then = function ( followUpFunction) {
 5:     if (status === 'pending') {
 6:       nextThingToDo = followUpFunction
 7:     }
 8:     else {
 9:       followUpFunction(value)
10:     }
11:   }
12:   const resolve = newValue => {
13:     value = newValue
14:     status = 'resolved'
15:     if (nextThingToDo) nextThingToDo(newValue)
16:   }
17:   aFnWithDelayAndACallback(resolve)
17: }
18: function functionWithDelay(param1) {
19:   const httpRequestWithCallback = function (callback) {
20:     let xhr = XMLHttpRequest()
21:     xhr.onreadystatechange = () => {
22:       if (xhr.readyState == 200) callback(xhr.responseText)
23:     }
24:     xhr.open(param1)
25:     xhr.send()
26:   }
27:   return new FunctionWithAThenFunction(httpRequestWithCallback)
28: }
29: functionWithDelay('some argument').then(console.log)
```

这里，为了清楚起见，我们在第 12–16 行引入了局部解析函数。它随后在第 17 行被调用。

## 把所有的放在一起

我们已经走了很长一段路。让我们再写一遍上一个例子，并稍微改变一下函数名。

*   `FunctionWithAThenFunction` 变为`Promise`
*   `functionWithDelay` 变为`fetchData`
*   `httpRequestWithCallback` 变为`httpRequest`
*   `aFnWithDelayAndACallback` 成为`fn`

```
function Promise(fn){
   let nextThingToDo, value
   let status = 'pending'
   this.then = function ( callback ) {
     if (status === 'pending') {
       nextThingToDo = callback
     }
     else {
       callback(value)
     }
   }
   const resolve = newValue => {
     value = newValue
     status = 'resolved'
     if (nextThingToDo) nextThingToDo(newValue)
   }
   fn(resolve)
 }
function fetchData(url) {
  const httpRequest = function (callback) {
    let xhr = XMLHttpRequest()
    xhr.onreadystatechange = () => {
      if (xhr.readyState == 200) callback(xhr.responseText)
    }
    xhr.open(url)
    xhr.send()
  }
  return new Promise(httpRequest)
}const testUrl = '[https://api.github.com/users/odemeulder](https://api.github.com/users/odemeulder)'
fetchData(testUrl).then(console.log)
```

`Promise` 代码现在可用于其他功能。假设您想要一个类似于`setTimeout` 的函数来返回一个承诺。让我们调用函数`Wait` ，它将接受以毫秒为单位的延迟时间。

```
function Wait(milliSeconds) {
  const fn = (callback) => setTimeout(callback, milliSeconds)
  return new Promise(fn)
}
Wait(5000).then(() => console.log('we waited 5 seconds'))
```

或者假设您想在 Node 中使用`httpRequest`示例。在 Node `XmlHttpRequest` 中无法工作，因为那是浏览器提供的对象，你必须使用 Node 的内置`https` 模块。

```
const https = require('https')
const testUrl = '[https://api.github.com/users/odemeulder](https://api.github.com/users/odemeulder)'
function fetchDataForNode(url) {
  const fn = callback => {
    const options = {
      url: url,
      headers: { 'User-Agent': 'request' }
    }
    https.get(options, response => {
      let ret
      response.on('data', chunk => ret += chunk)
      response.on('end', () => callback(ret))
    })
  return new Promise(fn)
}
fetchDataForNode(testUrl).then(console.log)
```

同样，不要担心`https.get`在 node 中如何工作的技术细节。重要的部分是:创建一个接受回调并包装异步调用的函数，并将该函数传递给你的`Promise` 构造函数。

## 后续步骤

很明显，承诺远不止我们刚刚概述的内容。这篇文章已经够长了。但是这里有一些改进/补充。

一件事是`then`函数应该返回一个新的承诺。这让你可以连锁承诺。`doSomething().then(somethingElse).then(aThirdThing)`

另一件我们没有涉及的大事是错误处理。我们的`Promise` 函数也应该实现一个`catch` 函数。该函数将一个函数作为参数，如果有任何错误，将调用该函数。

## 结论

我们创建了一个非常基本的 promise 对象，我向您展示了如何创建一个返回 Promise 的函数的三个示例(`fetchData` 用于浏览器 http 请求，`fetchDataForNode` 用于 Node 中的 http 请求，`wait`)。在每种情况下，您创建一个接受回调的函数，创建一个新的`Promise` 函数并将该函数作为回调传递。

我欢迎任何反馈和掌声。