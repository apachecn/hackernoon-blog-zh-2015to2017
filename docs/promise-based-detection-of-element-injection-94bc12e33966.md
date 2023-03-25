# 检测与选择器匹配的 DOM 元素添加

> 原文：<https://medium.com/hackernoon/promise-based-detection-of-element-injection-94bc12e33966>

## 停止轮询动态插入元素的 DOM

![](img/110d0d210e0f8c7cf21b4c6090b0f19b.png)

[Photo credit](http://www.burtchworks.com/2014/12/08/8-biggest-job-search-mistakes-quants-still-making/)

在很多情况下，您的页面可能需要等待 DOM 中特定操作的发生。通常，人们可能只依赖于`document` ready 或`window` load 事件来执行一些静态初始化，或者如果 Ajax 调用需要首先完成，显然您会在收到成功的响应后进行初始化。

当您的初始化依赖于一个未知事件时，事情可能会变得很糟糕，这个未知事件在您的控制之外，但是负责将内容插入 DOM。例如，您可能有一个需要加载和执行的第三方`<script>`，或者您可能需要根据某种用户交互对修改 DOM 的供应商插件代码做出反应。

## 古代史

过去，开发人员总是不情愿地使用两种常用方法来处理这种情况:

1.  这是一个足够简单的解决方案，对吗？只需调用`setInterval()`并每隔几毫秒检查一下 DOM，看看是否添加了元素。
2.  **设置长超时**
    “我知道，我会猜测`<script>`加载的最长时间，然后我会`setTimeout()`甚至*更长的时间*，这样我们就没事了！”

轮询解决方案通常采用以下形式，带有间隔或自动执行超时:

```
const myDynamicElementHandler = () => {
  /* ...do something... */
}// Poll the DOM every 100ms
const uglyDomCheckerInterval = setInterval(() => {
  if ($('#some-dynamic-element-id').length === 0) return clearInterval(uglyDomCheckerInterval) // Yay, our element now exists! ... do something with it
  myDynamicElementHandler()
}, 100)
```

超时“解决方案”与此类似，但更加草率……应该会让你感到惭愧:

```
// "I guess my element should exist after 10 seconds, so
// I'll just set a timeout... Problem solved!"setTimeout(myDynamicElementHandler, 10000)
```

如果这些解决方案没有让你内心感到一丝不快，那你就错了。

理想情况下，如果你正在处理第三方内容(无论是一个`<script>`还是一个插件，等等)。)您希望他们的代码执行一个您可以定义的回调，或者触发一个您可以挂接的事件；你可能只是想让代码以某种方式响应 DOM 中的特定变化。如果没有提供这样的选项，当我们*知道*相关内容确实存在于 DOM 中时，我们至少希望有一些其他的方法来执行我们的处理程序一次。

多亏了现代浏览器，我们肮脏的 DOM 轮询时代已经结束，我们基于承诺的 DOM 通知时代才刚刚开始。

## 突变观察者

曾经存在一系列 DOM 突变事件，这是一个伟大的想法，但执行不力，因为它们带来了性能问题和浏览器支持和实现的不一致性…然后又出现了 MutationObserver。

`[MutationObserver](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver)`允许您定义一个回调，当您感兴趣的特定类型的 DOM 突变发生时，将执行该回调。它有很多内容，所以我建议仔细阅读它，以防它对你有通用用途。值得注意的一点是，它在所有现代浏览器中都得到支持，甚至包括 IE11。

就我的意图和目的而言，MutationObserver 是一个很好的产品，帮助我很好地完成了我的目标。

## awaitSelector()

```
const awaitSelector = (selector, rootNode, fallbackDelay) => new Promise((resolve, reject) => {
  try {
    const root = rootNode || document
    const ObserverClass = MutationObserver || WebKitMutationObserver || null
    const mutationObserverSupported = typeof ObserverClass === 'function'let observerconst stopWatching = () => {
      if (observer) {
        if (mutationObserverSupported) {
          observer.disconnect()
        } else {
          clearInterval(observer)
        }observer = null
      }
    }const findAndResolveElements = () => {
      const allElements = root.querySelectorAll(selector)if (allElements.length === 0) returnconst newElements = []const attributeForBypassing = 'data-awaitselector-resolved'allElements.forEach((el, i) => {
        if (typeof el[attributeForBypassing] === 'undefined') {
          allElements[i][attributeForBypassing] = ''
          newElements.push(allElements[i])
        }
      })if (newElements.length > 0) {
        stopWatching()
        resolve(newElements)
      }
    }if (mutationObserverSupported) {
      observer = new ObserverClass(mutationRecords => {
        const nodesWereAdded = mutationRecords.reduce(
          (found, record) => found || (record.addedNodes && record.addedNodes.length > 0),
          false
        )if (nodesWereAdded) {
          findAndResolveElements()
        }
      })observer.observe(root, {
        childList: true,
        subtree: true,
      })
    } else {
      observer = setInterval(findAndResolveElements, fallbackDelay || 250)
    }findAndResolveElements()
  } catch (exception) {
    reject(exception)
  }
})const watchAwaitSelector = (callback, selector, rootNode, fallbackDelay) => {
  (function awaiter(continueWatching = true) {
    if (continueWatching === false) returnawaitSelector(selector, rootNode, fallbackDelay)
      .then(callback)
      .then(awaiter)
  }())
}
```

我使用了 MutationObserver 作为这个相当简单的`awaitSelector()`函数的基础，它允许您指定一个元素选择器，并返回一个承诺，该承诺将与插入到与您的选择器匹配的 DOM 中的*新的*元素一起解析。如果您的元素在调用`awaitSelector()`时已经存在，那么 Promise 将立即解析这些元素，否则，它将监听 DOM 中的元素插入，并解析是否有与您的选择器匹配的*新的*元素出现。下面是该功能的一个简单示例:

`awaitSelector()`接受以下三个参数:

*   `selector` —与元素匹配的 CSS 选择器字符串
*   [ `rootNode` ] —要在其中搜索元素的元素；*默认:* `document`
*   [`fallbackDelay`]—如果不支持 MutationObserver，则使用超时延迟；*默认值:*250 毫秒

为了彻底起见，我们不希望假设 MutationObserver 总是可用的，也不希望让需要支持旧浏览器的开发人员无法使用`awaitSelector()`，因此内置了一个后备方案，使用前面提到的旧轮询方法来继续检查 DOM 中的*新*匹配元素。

这里一个棘手的事情是，如果发现了与我们的选择器匹配的**新的**元素，我们只希望我们的承诺得到解决；我们希望函数是显式非幂等的。换句话说，如果我们用相同的参数第二次执行`awaitSelector()`，我们不希望它立即解析出与第一次执行时相同的元素。相反，我们希望它在返回新的匹配 DOM 元素之前等待任何新的匹配 DOM 元素。

另一个考虑是如何处理`awaitSelector()`的初始执行，以匹配 DOM 中已经存在的元素。它会像您可能期望的那样，立即解决任何这样的元素。

## watchAwaitSelector()

一个常见的用例是，每当插入任何匹配的元素时，您都希望在页面上无限次地执行回调，因此为了帮助实现这一点，还有一个名为`watchAwaitSelector()`的包装器函数。这个函数将回调作为它的第一个参数，其余的参数与`awaitSelector()`匹配。它将侦听与您的选择器匹配的新元素，并使用这些元素执行您的回调，然后继续侦听更多元素。如果你需要停止收听，你的回叫可以`return false`，它将停止观看。

## 结论

大多数开发人员仍然需要处理无穷无尽的遗留代码，但是随着现代标准和更广泛、更可靠的浏览器支持，越来越多的优秀解决方案成为了潜在的解决方案，而以前只有黑客和肮脏的变通方法。在还没有更好的解决方案的情况下，轮询可能仍然有它的位置，但是至少在等待 DOM 元素插入时，您可以很容易地创建一个有用的承诺，使您的手不会太脏。

为了完全透明，应该注意的是，当一个元素被插入到指定的 DOM 子树中时，MutationObserver 仍然会导致`awaitSelector()`的内部逻辑寻找所需的选择器，但这显然比任何基于轮询的方法都要好。

注意:由于我的 GitHub 中上面链接的默认 await-selector.js 脚本是用 ES6 编写的，所以我也为任何想在非 Babel 环境中使用它的人提供了一个 ES5 版本(尽管它仍然使用 Promise，所以如果有必要，您可能需要自己填充它)。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客们下午的开始。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)