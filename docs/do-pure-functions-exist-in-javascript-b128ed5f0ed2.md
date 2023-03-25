# JavaScript 中存在纯函数吗？

> 原文：<https://medium.com/hackernoon/do-pure-functions-exist-in-javascript-b128ed5f0ed2>

![](img/550317e12373d46eff8c862acd1549ab.png)

A shadow over our clean, elegant code? Photo by [Benjamin Bousquet](http://unsplash.com/@bixphotos?utm_campaign=photographer-credit)

最近，我参加了一个关于如何在 JavaScript 中确定一个函数是纯函数的讨论。纯粹的整个概念在这样一种动态的语言中似乎是模糊的。下面的例子表明，我们可能需要重新定义“纯函数”这个术语，或者——至少——在我们做出决定时要非常小心。

# 什么是纯函数

如果你是新学期的学生，我建议你先阅读一些介绍。[阿尔文·亚历山大](https://twitter.com/alvinalexander)和[大师 JavaScript 访谈:什么是纯函数？埃里克·埃利奥特](/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976)是很好的选择。

综上所述，一个函数如果满足两个条件就叫纯函数:

> 1)每次使用相同的参数集调用该函数时，它都会返回完全相同的结果。
> 
> 2)函数的求值除了返回值之外，不修改其范围之外的某些状态，也不与外界有可观察到的交互。(无副作用。)

有时，会添加第三个条件:“不依赖外部可变状态。”事实上，这是多余的，因为对可变变量的这种依赖将不可避免地导致违反第一个条件。

# 这些中哪一个是纯的

这里我写了四个示例函数。在继续之前，请回顾它们，自己决定哪些是纯净的，哪些是不纯净的。

完成了吗？太好了，我们来比较一下。

当我四处询问时，绝大多数人回答说函数 *doubleB* 是唯一不纯的，函数 *doubleA* 、 *doubleC* 和 *doubleD* 是纯的。

我们来看看条件。后一种是浑然不觉的；没有副作用。

第一个，更有趣。当使用相同的参数调用时，它们都返回相同的值(使用[等同于](https://facebook.github.io/jest/docs/expect.html#toequalvalue)来支持数组):

```
expect( doubleA(1) ).toEqual( doubleA(1) )
expect( doubleB(1) ).toEqual( doubleB(1) )
expect( doubleC(1) ).toEqual( doubleC(1) )
expect( doubleD([1]) ).toEqual( doubleD([1]) )
```

对吗？对吗？

Weeeell，这样写是的。但是，我的朋友[亚历山大](https://medium.com/u/dff9a83c7a3c?source=post_page-----b128ed5f0ed2--------------------------------)回复的这段代码呢？

```
doubleB(1) // -> 2two = 3doubleB(1) // -> 3
```

这是有效的。我用相同的参数运行了这个函数两次，得到了不同的值。这使得它不纯。不管中间发生了什么？

这让我思考。如果这就是证据，那其他人呢？如果我足够努力，他们会坚持吗？正如你所猜测的，不，他们没有。事实上，我现在说:

> 四个函数没有一个是纯的。

## 功能一流

在 JavaScript 中，函数是一级的，这意味着它们是一个变量的值，可以被传递、返回和重新分配。如果我可以改变变量*和两个*，我可以做如下操作:

```
doubleC(1) // -> 2getTwo = function() { return 3 }doubleC(1) // -> 3
```

需要强调的是，这与 Alex 在上面所做的并无不同。只是变量不是保存一个数字，而是保存一个函数。

## 阵列上的地图

[“贴图，过滤，缩小。重复。”](https://www.youtube.com/watch?v=RUXUXup1Ji0)这是我在[的一次现场编码会议](https://www.twitch.tv/robinpokorny)的名字。这三者是函数式编程中数据转换的核心。所以在纯函数中使用它们应该是安全的。

事实证明，在 JavaScript 中没有什么是一成不变的。或者应该说在*原型*里？

```
doubleD([1]) // -> [2]Array.prototype.map = function() {
  return [3]
}doubleD([1]) // -> [3]
```

等等。这肯定是不允许的。这是不对的。

这可能是错误的，也可能是愚蠢的。事实是，我只是用相同的参数调用了函数*double*两次，得到了不同的值。*无论在*之间发生了什么。

我们所做的就是在函数调用之间重新分配变量。就像我们以前做的一样。

因此，*翻倍*不纯。

## 乘法是纯粹的

无论动态性如何，在 JavaScript 中人们不能像在某些语言中那样覆盖内置的操作符。

此外， *n* 是一个只存在于该函数范围内的局部变量。没有办法从外面改变它。

或者是？

不，这真的不可能。如果你抱太大希望的话，你一定不看好 JavaScript😄

但是当我写下这四个函数没有一个是纯的时，我暴露了自己。我还有一个锦囊妙计。

虽然我不能改变操作，也不能改变通过后的参数，但我有选择通过什么的自由。数字、字符串、布尔、对象…

对象？它们能有什么用？一个数乘以一个对象是，呃，是…像在` 2 * {} `中，是… *南。*(去控制台检查一下。)(正如我所做的。)

不过，这没什么用。要是有一种方法能让运行时在相乘时将对象转换成数字就好了。

## 数字字符串

如果一个对象出现在一个字符串上下文中，比如与一个字符串连接，引擎将运行该对象的 *toString* 函数并使用结果。如果没有实现，它将回退到已知的由*Object . prototype . tostring*方法产生的*'【对象对象】'*。

虽然很少使用，但是当 JavaScript 需要一个数字(或者一个布尔值，或者一个函数)时，它也会调用对象的 *valueOf* 方法。剩下的就是让这个函数每次被调用时返回不同的值。

```
var o = {
  valueOf: Math.random
}doubleA(o) // -> 1.7709942335937932doubleA(o) // -> 1.2600863386367704
```

[在 JS Bin 中运行这个。](http://jsbin.com/lojupas/edit?js,console)

乌夫，是的。使用完全相同的(通过任何比较手段)参数调用了该函数两次，第二次返回的值与第一次不同。它不纯。

*注意:本文的前一版本使用了*@ @ top primitive*，或者更详细地说，*symbol . top primitive*。正如*[*Alexandre mor gaut*](https://medium.com/u/1dd97108b7fa?source=post_page-----b128ed5f0ed2--------------------------------)*所指出的，从 JavaScript 的第一个版本开始，* valueOf *就已经足够并得到支持。如果你不知道* @@toPrimitive *，你可能还是想* [*查一下*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/toPrimitive) *。*

# 什么是纯函数

我知道，我是小心翼翼的，邪恶的，而且我用了一些卑鄙的手段。我们使用纯函数来增强对代码的信心。以确保代码做它应该做的事情。任何时候，任何情况下。

如果我决定的话，我希望这四个函数都是纯的。是的，这包括像 *doubleB* 这样的功能。如果那个变量(*两个*)不应该被改变，它是一个数学常数 e，pi，或者 [phi](https://en.wikipedia.org/wiki/Golden_ratio) 呢？那应该是纯的。

我希望能够信任内置函数。如果我假设*数组.原型*或*对象.原型*中的任何东西都可以改变，我能创建什么样的程序？极其基础的；没有人会想使用它们。

由于这个小而有趣的练习，我相信**我们需要一个新的定义来定义 JavaScript 中的纯函数**。不幸的是，我认为这不可能仅限于技术术语。

在某种程度上，它必须考虑代码的预期用途。一个函数在一个项目中可能被认为是纯的，而在另一个项目中可能被认为是不纯的。这是可以的。只要程序运行正常。只要开发商有信心。

你对这个定义有什么想法吗？你如何决定一个函数是纯的？我错过了什么吗？你学到什么了吗？

# 评论

有*的一些*方法来抵御*的一些*上面使用的诡计。

通过将整个块封装到一个函数中，可以避免覆盖像 *two* 或 *getTwo* 这样的自由变量。使用[寿命](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression)或模块:

```
var doubleB = (function () {
  var two = 2 return function (n) {
    return n * two
  }
})()
```

更好的方法是使用 ES2015 中引入的[常量](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const):

```
const two = 2
const doubleB = (n) => n * two
```

防止滥用或*的*值也是可能的，但是很麻烦。比如像这样:**

```
function doubleA(n) {
  if (typeof n !== 'number') return NaN return n * 2
}
```

人们可以通过改变 *Array.prototype* 来避开这个技巧，只需避免这样的函数，并退回到 *for (for … of)* 循环。这是丑陋的，不切实际的，甚至是不可能的。抽象这些或者使用一个库本身就有缺点。

不要忘记，要使一个函数真正纯净，需要将所有这些反技巧结合在一起。想象一下，现在优雅的 doubleD 看起来会是什么样子，会有多长，会如何损害可读性。

*如果你喜欢这个帖子，请别忘了给个*👏*下面的*。每一个鼓掌通知对我来说都是一种激励。

如果你想了解更多，我最近在 YouTube 上开了一个关于 JavaScript 的频道。我每周都会发布新视频，所以可以考虑订阅。从一开始就在那里，帮助我变得更好。

[](https://www.youtube.com/c/robinpokorny?sub_confirmation=1) [## YouTube 上的罗宾·波科尔尼

### JavaScript 是我的激情所在:我喜欢写 JavaScript，我喜欢读 JavaScript，我喜欢说 JavaScript。

www.youtube.com/c/robinpokorny](https://www.youtube.com/c/robinpokorny?sub_confirmation=1) [![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果您喜欢这个故事，我们建议您阅读我们的最新科技故事和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实视为理所当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)