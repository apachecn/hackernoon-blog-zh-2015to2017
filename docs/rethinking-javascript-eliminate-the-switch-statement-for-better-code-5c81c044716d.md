# 重新思考 JavaScript:删除 switch 语句以获得更好的代码

> 原文：<https://medium.com/hackernoon/rethinking-javascript-eliminate-the-switch-statement-for-better-code-5c81c044716d>

![](img/c056fe1c4498c7b546db1248af360064.png)

在我的前 3 篇文章中，我说服你[删除 if 语句](/p/rethinking-javascript-the-if-statement-b158a61cd6cb)，[删除 for 循环](https://hackernoon.com/rethinking-javascript-death-of-the-for-loop-c431564c84a8)，并且[永远不要使用 break](https://hackernoon.com/rethinking-javascript-break-is-the-goto-of-loops-51b27b1c85f8) 。现在我要试着说服你删除 switch 语句。

虽然 switch 语句很有用，但它不太适合我们的其他功能代码。不是不可变的，不能和其他函数合成，有点副作用。

Switch 也用`break`，也是反函数。(在下面的文章中找出`break`为什么这么恐怖)

[](https://hackernoon.com/rethinking-javascript-break-is-the-goto-of-loops-51b27b1c85f8) [## 重新思考 JavaScript:用函数化取代 break

### 在我的上一篇文章《for 循环之死》中，我试图说服您放弃 for 循环，转而使用一个更具功能性的…

hackernoon.com](https://hackernoon.com/rethinking-javascript-break-is-the-goto-of-loops-51b27b1c85f8) 

我真正想要的是不变的，没有副作用的东西。我可以传入一个值，传出一个值。你知道，一些功能性的东西！

让我们深入研究一下直接来自 [redux 网站](https://github.com/reactjs/redux)的一个例子。

```
function counter(state = 0, action) {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1
    case 'DECREMENT':
      return state - 1
    default:
      return state
  }
}
```

我试着玩了一下，最后把它转换成了嵌套的三元组。

```
const counter = (state = 0, action) =>
  action.type === 'INCREMENT'   ? state + 1
  : action.type === 'DECREMENT' ? state - 1
                                : state
```

(如果你觉得这种语法令人困惑，那么看看这篇文章，它将解释`if`的和嵌套三元。)

[](/@joelthoms/rethinking-javascript-the-if-statement-b158a61cd6cb) [## 重新思考 JavaScript:if 语句

### 功能性思维打开了我对编程的思维。

medium.com](/@joelthoms/rethinking-javascript-the-if-statement-b158a61cd6cb) 

这更好，但有一点我不喜欢的是`action.type ===`的重复。所以我想我可以用一个对象字面量。我还可以创建一个函数来包装对象文字，同时添加对默认情况的支持。

```
function switchcase (cases, defaultCase, key) {
  if (cases.hasOwnProperty(key)) {
    return cases[key]
  } else {
    return defaultCase
  }
}
```

现在让我们来对付这个坏小子，把`if`转换成三进制，再提高一点！

```
const switchcase = cases => defaultCase => key =>
  cases.hasOwnProperty(key) ? cases[key] : defaultCase
```

性感！

现在我们的减速器看起来像这样…

```
const counter = (state = 0, action) =>
  switchcase({
    'INCREMENT': state + 1,
    'DECREMENT': state -1
  })(state)(action.type)
```

这个早期版本的`switchcase`的问题是整个对象文字在被传递给`switchcase`函数之前被评估。这意味着`state +1`和`state -1`都被评估。这可能非常危险…不好。

嗯……如果对象文字中的值是函数，那么它们只有在匹配的情况下才能被执行。

因此，让我们创建一个名为`switchcaseF` (F 代表函数)的配套函数，它就像`switchcase`一样，只是它的值是函数。

```
const switchcaseF = cases => defaultCase => key =>
  switchcase(cases)(defaultCase)(key)**()**
```

现在，缩减器的值可以改为函数，并且只计算匹配的情况。

```
const counter = (state = 0, action) =>
  switchcaseF({
    'INCREMENT': **() =>** state + 1,
    'DECREMENT': **() =>** state -1
  })(**() =>** state)(action.type)
```

…但我有点希望这些功能是可选的。所以让我们试着加上这个…

```
const executeIfFunction = f =>
  f instanceof Function ? f() : fconst switchcaseF = cases => defaultCase => key =>
  executeIfFunction(switchcase(cases)(defaultCase)(key))
```

现在我们的 redux reducer 可以看起来像这样(注意默认值)…

```
const counter = (state = 0, action) =>
  switchcaseF({
    'INCREMENT': () => state + 1,
    'DECREMENT': () => state -1
  })**(state)**(action.type)
```

好吧，没有很大的不同，但我们可以这样做。(添加了重置)

```
const counter = (state = 0, action) =>
  switchcaseF({
    **'RESET': 0,**
    'INCREMENT': () => state + 1,
    'DECREMENT': () => state -1
  })(state)(action.type)
```

# 另一个例子

这个例子取自[http://www.w3schools.com/js/js_switch.asp](http://www.w3schools.com/js/js_switch.asp)

```
var day;switch (new Date().getDay()) {
    case 0:
        day = "Sunday";
        break;
    case 1:
        day = "Monday";
        break;
    case 2:
        day = "Tuesday";
        break;
    case 3:
        day = "Wednesday";
        break;
    case 4:
        day = "Thursday";
        break;
    case 5:
        day = "Friday";
        break;
    case 6:
        day = "Saturday";
}
```

因为我们已经像好男孩和好女孩一样适当地训练了`switchcase`,我们可以将这种转换分解成多个可重用的方法，我们都知道…

> 让我们的代码可重用应该是我们的目标之一。

```
const getDay = switchcase({
    0: 'Sunday',
    1: 'Monday',
    2: 'Tuesday',
    3: 'Wednesday',
    4: 'Thursday',
    5: 'Friday',
    6: 'Saturday'
  })('Unknown')const getCurrentDay = () =>
  getDay(new Date().getDay())const day = getCurrentDay()
```

这里，我们将所有的逻辑从不纯的`getCurrentDay`函数中分离出来，变成了一个纯的`getDay`函数。这将使测试和**功能组合**更加容易。

[](https://hackernoon.com/javascript-functional-composition-for-every-day-use-22421ef65a10) [## 函数 JavaScript:日常使用的函数组合。

### 函数组合是函数式编程中我最喜欢的部分。我希望给你提供一个好的真实的…

hackernoon.com](https://hackernoon.com/javascript-functional-composition-for-every-day-use-22421ef65a10) 

# 额外学分

现在我们可以做一些很酷的事情，比如用程序创建一个`switchcase`！

```
const days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']const getDay = switchcase(
  days.reduce((acc, value) =>
    (acc[value] = `The day is ${value}.`, acc), {})
  )("I don't know what day it is.")console.log(getDay('Monday'))// 'The day is Monday.
```

这还不是最伟大的例子。有明显的和更好的方法来生成这个句子，我只是想演示一下`switchcase`能做什么，所以放我一马吧！

# 代码

这是我们最终的、纯粹的、不可变的功能性`switchcase`代码。

# 第三方库

对于那些对深入研究函数世界感兴趣的人，我想推荐一些很棒的库。

[**Ramda**](http://ramdajs.com/) — Ramda 是一个类似于 lodash 和下划线的函数库。看看 Ramda 的`cond`功能。功能类似于这个`switchcase`和一个伟大的替代品！

[**lodash/FP**](https://github.com/lodash/lodash/wiki/FP-Guide)—lodash/FP 是 lodash 的功能版本。值得一看。

[**幻境**](https://github.com/fantasyland/fantasy-land) —比较流行的 JavaScript 函数库之一。

# 摘要

switch 语句不适合我们的函数范式，但是我们可以创建一个具有类似特征的函数。这样做让我们将交换机分解成更小的和可重用的部分。我们甚至可以通过编程生成一个！

我知道这是一件小事，但当我在媒体和 Twitter ( [@joelnet](https://twitter.com/joelnet) )上收到这些后续通知时，我感到非常高兴。或者你觉得我满嘴屁话，在下面的评论里告诉我。

干杯！

注意:在你对`switchcase`不支持‘失败’抓狂之前，这是因为我的项目不需要它。它并不意味着是`switch`的精确复制品。 [YAGNI](https://martinfowler.com/bliki/Yagni.html) 什么的。

# 相关文章

[](https://hackernoon.com/rethinking-javascript-death-of-the-for-loop-c431564c84a8) [## 重新思考 JavaScript:For 循环的死亡

### JavaScript 的 for 循环为我们提供了很好的服务，但是它现在已经过时了，应该被更新的函数所取代…

hackernoon.com](https://hackernoon.com/rethinking-javascript-death-of-the-for-loop-c431564c84a8) [](/@joelthoms/functional-javascript-resolving-promises-sequentially-7aac18c4431e) [## 函数式 JavaScript:顺序解析承诺

### 我喜欢 ES6 附带的新的 Promise 库，尽管有一点被遗漏了，那就是一个按顺序…

medium.com](/@joelthoms/functional-javascript-resolving-promises-sequentially-7aac18c4431e) [![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)