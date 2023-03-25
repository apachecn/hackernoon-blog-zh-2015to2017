# 如何在 Async/Await 中使用 Lo-Dash 函数

> 原文：<https://medium.com/hackernoon/how-to-use-lo-dash-functions-with-async-await-b0be7709534c>

是一个非常酷和有用的库。如果我们能够使用`Async/Await`的函数，JavaScript 的生活将会更加舒适。
我想尽可能地写出与`Lo-Dash`函数相同的方式。因为如果我们已经知道如何使用`Lo-Dash`，就不需要学习新的功能。示例代码如下所示。

```
// example 1
const array = [1, 2, 3];
const result = _.map(array, syncFunc);// ↓ async/awaitconst result = await _.map(array, asyncFunc);function syncFunc(n) {
  return n * 2;
}
function asyncFunc(n) {
  return new Promise(resolve => setTimeout(resolve, 10, n * 2));
}
```

`_.chain`使我们的代码变得干净，我们也想使用它。

```
// example 2
const array = [1, 2, 3];
const result = _.chain(array)
  .map(syncFunc)
  .sum()
  .value();// ↓ async/awaitconst result = await _.chain(array)
  .map(asyncFunc)
  .sum()
  .value();
```

# 将 Lo-Dash 函数转换为异步函数

我开发的库具有`mixin`功能。它将`Lo-Dash`函数转换为异步函数，并将它们分配给`Aigle`。用法如下。

```
const _ = require('lodash');
const Aigle = require('aigle');Aigle.mixin(_);// example 1
const array = [1, 2, 3];
const result = await Aigle.map(array, asyncFunc);// example 2
const result = await Aigle.chain(array)
  .map(asyncFunc)
  .sum()
  .value();// orconst result = await Aigle.resolve(array)
  .map(asyncFunc)
  .sum();
```

您将能够异步使用`Lo-Dash`函数。此外，`Aigle`已经有许多函数与`Lo-Dash`函数具有相同的功能，这些函数不会被转换，因为它们已经被优化。

# 结论

`Aigle`会让你的代码和`Lo-Dash`一样干净。如果你对`Aigle`感兴趣，我希望你能阅读下面的文章。用`Aigle`来享受`Async/Await`吧。

## 参考

*   [艾格](https://github.com/suguru03/aigle)
*   [破折号](https://github.com/lodash/lodash)
*   [如何最快做出承诺库](https://hackernoon.com/how-to-make-the-fastest-promise-library-f632fd69f3cb)
*   [艾格 vs 蓝鸟](https://hackernoon.com/aigle-vs-bluebird-5ed3becb4c4c)