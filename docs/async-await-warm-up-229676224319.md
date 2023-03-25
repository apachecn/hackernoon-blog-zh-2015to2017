# 异步/等待预热

> 原文：<https://medium.com/hackernoon/async-await-warm-up-229676224319>

![](img/3daed7af1949789968746d9155865fae.png)

如果你习惯于使用承诺，[*async*](https://hackernoon.com/tagged/async)*/await*是让你的 [javascript](https://hackernoon.com/tagged/javascript) 更易读的一种优雅方式。它评估为同步代码，但返回一个在下一个[微任务](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)上执行的承诺。

让我们直接跳进去四处看看。打开 [babel repl](https://babeljs.io/repl/#?babili=false&evaluate=true&lineWrap=false&presets=es2015%2Cstage-3&code=) 和我一起编码。取一个简单的函数，并将*异步*添加到开头:

```
**async function sayHello() {
 return 'hello';
}****console.log(sayHello());** // => Promise {}
```

您将看到调用这个函数返回一个*承诺*而不是“hello”。这是因为从一个*异步*函数返回的任何东西都被自动包装在一个承诺中。为了记录“你好”，我们可以这样做:

```
**sayHello()
  .then(str => console.log(str))** *// => 'hello'*
```

## 好吧，那等待呢？

首先，编写一个返回承诺的函数:

```
**function mapLater(arr, fn, time) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(arr.map(fn));
    }, time);
  });
}**
```

很简单，我们在给定的时间后映射一个数组。现在让我们在一个*异步*函数中使用它。

```
**async function addAndMultiply(arr) {
  const added = await mapLater(arr, i => i + 2, 2000);
  const multiplied = await mapLater(added, i => i * 2, 2000);

  console.log(multiplied);
}****addAndMultiply([1, 3, 6, 20]);** **console.log('hello')***// =>* 'hello' *// 4 secs later =>* [6, 10, 16, 44]
```

在我们返回数组之前，您会看到记录了“hello”。我们的*异步*函数是非阻塞的。

需要注意的是*等待*必须总是在*异步*闭包内。在全局范围内或在没有 async 关键字的函数中调用它将引发错误。

## 处理错误

想象一下我们最初的 *mapLater* 功能出了问题:

```
...
setTimeout(() => {
 **reject('nope');**
}, time);
```

目前，我们没有办法暴露我们的 async *addAndMultiply* 函数中的错误。当我们运行它的时候，函数会无声地失败。为了处理错误，一个解决方案是使用 *try / catch* :

```
async function addAndMultiply(arr) {
 **try {**    const added = await mapLater(arr, i => i + 2, 2000);
    const multiplied = await mapLater(added, i => i * 2, 2000);
    console.log(multiplied); 
 **} catch(err) {**    **console.error(err);** *// 2 secs later => 'nope'*
 **}** }
```

因为默认情况下会出现错误，所以另一个实用的解决方案是在异步入口点处理错误:

```
intricatelyNestedAsyncFunc().catch(err => console.error(err));
```

## 现实世界

今天就用吧！如此多伟大的库提供了返回承诺的 API。例如，如果您正在使用 [*获取*](https://github.com/github/fetch) 来检索数据，您可以开始这样做:

```
async function getProfileData(id) {
  try {
 **const users = await getUser(id);**
    ...  
  } catch {
    ...
  }
}
```

要使用*async/await*now*你需要使用 Babel 的[*transform-async-to-generator*](https://babeljs.io/docs/plugins/transform-async-to-generator/)。*