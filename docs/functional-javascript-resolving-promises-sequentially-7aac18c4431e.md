# 函数式 JavaScript:顺序解析承诺

> 原文：<https://medium.com/hackernoon/functional-javascript-resolving-promises-sequentially-7aac18c4431e>

![](img/15f5d3529e690d31554750fde2e3eb7d.png)

我喜欢 ES6 附带的新的 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 库，尽管有一点被遗漏了，那就是一个顺序执行多个承诺的功能。

我们可以使用类似 [Q](https://github.com/kriskowal/q) 、[蓝鸟](https://github.com/petkaantonov/bluebird)、 [RSVP.js](https://github.com/tildeio/rsvp.js/) 、 [Async](https://github.com/caolan/async) 等库。或者我们可以自己做。我真的只需要一个函数，为一个函数导入整个库似乎有点沉重，这就是我创建这个的原因。

这个承诺的一个特点是它立即执行。这实际上对我们不利，当我们准备好执行时，我们需要承诺来执行。

我这样做的方法是将每个承诺转换成一个工厂函数。工厂函数将是一个返回承诺的简单函数。现在，当我们做出决定时，我们的承诺就会实现。

对于这个虚构的例子，我决定使用 jQuery 的 ajax 方法作为我的承诺。

```
// some dummy urls to resolve
const urls = ['/url1', '/url2', '/url3']

// convert each url to a function that returns an ajax call
const funcs = urls.map(url => () => $.ajax(url))
```

解决这个问题有点复杂，我发现它有助于我提前考虑我们的函数应该输出什么。大概是这样的:

```
Promise.resolve()
  .then(x => funcs[0]()) // resolve func[0]
  .then(x => funcs[1]()) // resolve func[1]
  .then(x => funcs[2]()) // resolve func[2]
```

我还希望最终承诺返回一个包含每个承诺结果的数组。

这是最复杂的部分。我需要用一个空数组`[]`开始每个承诺，然后将每个承诺的结果连接到该数组。跟着我，我会尽力分解它。

我将用一个空数组的初始值开始这个承诺，就像这个`Promise.resolve([])`。然后使用 Promise 的`then`功能执行每个工厂功能。

为了简单起见，这个例子只解析 func 的索引`0`。剩下的我们以后再做。

```
// start our promise off with an empty array. this becomes all.
Promise.resolve([])
  // all is the array we will append each result to.
  .then(all => {
    return funcs[0]().then(result => {
      // concat the resolved promise result to all
      return all.concat(result)
    })
   })
```

这段代码可以通过从我们的代码中删除所有的`{`、`}`和`return`以更简洁的方式来表达。

```
Promise.resolve([])
  .then(all => funcs[0]().then(result => all.concat(result)))
```

去掉箭头函数一个巧妙方法是像这样直接调用`concat`:

```
Promise.resolve([])
  .then(all => funcs[0]().then(Array.prototype.concat.bind(all)))
```

最后，这将是我们函数的输出:

```
Promise.resolve([])
  .then(x => funcs[0]().then(Array.prototype.concat.bind(x)))
  .then(x => funcs[1]().then(Array.prototype.concat.bind(x)))
  .then(x => funcs[2]().then(Array.prototype.concat.bind(x)))
```

还不算太糟吧？现在我们知道了我们的输入和输出，让我们开始吧！

我们可以使用一个`for`循环(但这不是很实用)，我们也可以使用递归，但我真正喜欢这个问题的是`reduce`。

> 每当需要将一个列表转换成一个对象时，可以考虑使用 reduce。

我们的`promiseSerial`函数应该接受一个工厂函数数组(每个函数返回一个承诺),并将它们简化为上面表示的单个承诺链。

我们的初始值`Promise.resolve([])`像这样传递给我们的 reduce 方法:

```
const promiseSerial = funcs =>
  funcs.reduce((promise, func) => ???, Promise.resolve([]))
```

最后一部分是从上面概括我们的一个承诺`then`，并更新一些参数名称。(新部分以粗体显示)

```
const promiseSerial = funcs =>
  funcs.reduce((promise, func) =>
    promise.then(result =>
      **func().then(Array.prototype.concat.bind(result)))**,
      Promise.resolve([]))
```

就是这样！一个非常简单的…划掉那个…短函数，它将依次解析承诺。

最后，让我们一起拍击它。

现在，我们已经不再需要安装第三方库，因为我们有了全新的`promiseSerial`功能。

嘿！你居然坚持到了这篇文章的结尾！

你在顺序解决承诺的用例是什么？你是怎么解决的？请分享你的经历。

我知道这是一件小事，但当我在媒体和 Twitter 上收到这些后续通知时，我感到非常高兴。或者你觉得我满嘴屁话，在下面的评论里告诉我。

干杯！

# 相关文章

[](/@joelthoms/rethinking-javascript-break-is-the-goto-of-loops-51b27b1c85f8) [## 重新思考 JavaScript: Break 是循环的归宿

### 在我的上一篇文章《for 循环之死》中，我试图说服您放弃 for 循环，转而使用一个更具功能性的…

medium.com](/@joelthoms/rethinking-javascript-break-is-the-goto-of-loops-51b27b1c85f8) [](https://hackernoon.com/rethinking-javascript-death-of-the-for-loop-c431564c84a8) [## 重新思考 JavaScript:For 循环的死亡

### JavaScript 的 for 循环为我们提供了很好的服务，但是它现在已经过时了，应该被更新的函数所取代…

hackernoon.com](https://hackernoon.com/rethinking-javascript-death-of-the-for-loop-c431564c84a8) [](/@joelthoms/rethinking-javascript-the-if-statement-b158a61cd6cb) [## 重新思考 JavaScript:if 语句

### 功能性思维打开了我对编程的思维。

medium.com](/@joelthoms/rethinking-javascript-the-if-statement-b158a61cd6cb) [![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)