# 函数装饰者:将回调转化为承诺，然后再转化回来

> 原文：<https://medium.com/hackernoon/transforming-callbacks-into-promises-and-back-again-e274c7cf7293>

![](img/1490fe1d6a7c5392ec6f04d6df219414.png)

[geralt @ pixelbay](https://pixabay.com/en/think-switch-arrows-rethinking-2177840/)

我在 JavaScript 领域工作的每一天，都会遇到回调、承诺或 async/await。对于如何处理异步代码，我有自己的偏好，尽管有时我没有选择，因为像 [fs](https://nodejs.org/api/fs.html) 、[无服务器](https://serverless.com)、 [aws-sdk](https://www.npmjs.com/package/aws-sdk) 等外部库。正在使用其他东西。

我不喜欢任何代码库中的不一致性。所以，如果我从承诺开始，我会从头到尾使用承诺。

这提出了一个小问题；我如何在一个有外部库的项目中保持一致性？正如本文的标题所示，我对它们进行了改造。

# 回访和承诺

我遇到的最常见的场景是一个使用节点样式回调的旧库，我更喜欢使用 promises 或 async/await 等类似的东西。

回顾一下，典型的节点样式回调可能如下所示:

```
fs.readFile('./kittens.txt', (err, data) => {
  if (err) throw err;
  console.log(data);
});
```

但这是我想使用的方法:

```
fs.readFile('./kittens.txt')
  .then(data => console.log(data))
```

通常，我只是踢踢腿，使用来自 [bluebird.js](http://bluebirdjs.com) 的 [promisify](http://bluebirdjs.com/docs/api/promise.promisify.html) 。这是最简单、最快和最懒惰的方法。但是…

> 导入一个库来使用单个函数是懒惰的，应该被禁止。

…尤其是当所讨论的函数很容易编写的时候。(谷歌 [*leftpad 爆料互联网*](https://encrypted.google.com/#q=leftpad%20broke%20the%20internet) )

现在停下来，看看 node_modules 文件夹的大小。太猥琐了！node_modules 文件夹超过 1GB 甚至 2GB 的情况并不少见！

这些事情加起来。

# 许诺

我们想要的叫做**函数装饰器**。

> 一个 ***函数装饰器*** 是一个高阶函数，它以一个函数为自变量，返回另一个函数，返回的函数是自变量函数的变体。[来源](https://leanpub.com/javascriptallongesix/read#decorators)

一个`promisify`函数装饰器看起来像什么的例子:

```
const readFile = promisify(fs.readFile)readFile('./kittens.txt')
  .then(data => console.log(data))
```

功能`readFile`，仍然与`fs.readFile`紧密相关，已经被转换成原始功能的变体。这是一个**函数装饰器**。

这个应该很容易做。首先，我们创建一个带有 1 个参数的函数，并返回一个函数。当返回的函数被调用时，它将返回一个承诺。

```
function promisify(func) {
  return () =>
    new Promise((resolve, reject) => { })
}
```

我们在上面使用了关键字`function`，因为我们需要访问`this`，这对于箭头函数是不可用的。

注意(下面)一个额外的参数，`callback`是如何附加到`args`的末尾的。这是因为我们的修饰函数不接受回调作为参数，但是原始函数`func`接受。

```
func.apply(this, [...args, callback])
```

综合起来，它看起来像这样:

```
function promisify(func) {
  return (**...args**) =>
    new Promise((resolve, reject) => {
      **const callback = ???** **func.apply(this, [...args, callback]);**
    })
}
```

最后一步也是最简单的，创建`callback`。`callback`只是一个节点样式的`callback` ，它将基于`err`的存在调用`resolve`或`reject`。

# 回调

我遇到了一个不太常见的情况，我需要的是相反的情况。我必须创建的函数需要是一个节点样式的回调函数。Uggg，我的代码已经用承诺写好了。

有问题的代码是针对 AWS lambdas 的，AWS lambdas 通常使用回调来编写，所以我的代码最终看起来像这样:

```
module.exports.handler **=** function(event, context, **callback**) {
  myHandler(event)
    .then(data => **callback(null, data)**)
    .catch(err => **callback(err)**)
}
```

因为我的库都是基于承诺的，所以我更喜欢这样写:

```
module.exports.handler = (event, context) =>
  myService(event) // note: I don’t need context.
```

我最终创建了`promisify`、`callbackify`的反向，所以我可以像这样创建我的 lambda:

```
module.exports.handler = callbackify((event, context) =>
  myService(event))
```

因为我们几乎和上面做的一样，所以我们可以更快一点。让我们用一个返回函数的参数`func`创建`callbackify`。因为返回的函数将 args + callback 作为参数，所以我们必须将它们分开。

```
function callbackify(func) {
  return function(...args) {
 **const onlyArgs = args.slice(0, args.length - 1)
    const callback = args[args.length - 1]**
  }
}
```

最后，我们需要在`func`上调用`apply`，并传入我们的`onlyArgs`。

# async/await 如何适应这一切？

async 和 await 的操作几乎与 promises 完全一样，所以可以互换使用。

```
const readFile = promisify(fs.readFile)
const file = await readFile('./kittens.txt)console.log(file)
```

看看这个，你可以等待一个节点风格的`callback`函数。整洁！

# 外卖

无论你使用回调、承诺还是异步/等待，保持一致是最重要的。

在导入库之前，尝试创建自己的函数。

如果你想调整库函数的工作方式，创建一个**函数装饰器**。

# 开源代码库

我把这些函数放在 github 上，这样我就可以很容易地把它们导入到我的项目中，因为我几乎在每个项目中都用到它们。你也可以使用它们。

[](https://github.com/joelnet/functional-helpers) [## Joel net/功能助手

### 功能助手—功能助手

github.com](https://github.com/joelnet/functional-helpers) 

# 有关系的

[util.promisify 正在被添加到节点](https://github.com/nodejs/node/compare/f72376d3230be7c968e392d59680bd01f8c20360...faf6654ff75e0f275afddfd980387235c3ddf103)

[蓝鸟的承诺](http://bluebirdjs.com/docs/api/promise.promisify.html)

# 结束

如果您对此感兴趣，或者对函数式编程感兴趣，您可能会喜欢我的其他一些文章。

[](https://medium.com/@joelthoms/latest) [## 乔尔·托马斯写的最新故事——中等

### 阅读乔尔·托马斯在 Medium 上写的最新故事。计算机科学家和技术布道者，拥有 21 年的…

medium.com](https://medium.com/@joelthoms/latest) 

我知道这是一件小事，但当我在媒体和 Twitter ( [@joelnet](https://twitter.com/joelnet) )上收到这些后续通知时，我感到非常高兴。或者你觉得我满嘴屁话，在下面的评论里告诉我。

干杯！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)