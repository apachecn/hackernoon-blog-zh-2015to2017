# saz erac:JavaScript 的数据驱动测试

> 原文：<https://medium.com/hackernoon/sazerac-data-driven-testing-for-javascript-e3408ac29d8c>

![](img/be754c6a0b330aced4ea5f78eb402f0b.png)

我最近构建了 [**Sazerac**](https://github.com/mikec/sazerac) 来解决我在编写 [JavaScript](https://hackernoon.com/tagged/javascript) 测试时经常遇到的一个问题。我发现，为了创建一个全面的、可维护的、没有大量重复代码的测试套件，我最终使用了一种数据驱动的方法。在[**Rocket Insights**](http://www.rocketinsights.com)**使用这种方法有助于我们交付测试代码，这些代码既有助于捕捉回归，也易于阅读和理解。**

> **“数据驱动”测试归结为一组给定的输入和预期的输出。**

**将您的应用程序代码封装到 [**纯函数**](/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976) 中，将有助于促进这种输入/输出测试风格，这通常是一个值得遵循的良好实践。**

**虽然最新的 JS 栈(像[**React**](https://facebook.github.io/react/)+[**Redux**](http://redux.js.org/))鼓励纯函数式方法，但是最广泛使用的测试框架( [**Jasmine**](https://jasmine.github.io/) 、 [**Mocha**](https://mochajs.org/) 和 [**Jest**](https://facebook.github.io/jest/) )并没有按照这种方法设计。他们经常假设您将测试有副作用的代码。认为代码“刺探”，并(在一定程度上)嘲讽。这些特性是为测试不纯的函数而创建的，但是没有专门用于测试纯函数的东西。**

**我强烈感觉到需要一个以数据驱动方法为核心的测试框架。构建这个框架是一项艰巨的任务，所以我选择了一个更简单的方案。我将 Sazerac 设计成一个助手库，可以与 Jasmine、Mocha 和 Jest 一起工作。**

**我可能会考虑把 Sazerac 变成一个独立的框架，但是我想先看看人们是否觉得它有用。**

# **言归正传**

**让我们看一些代码。**

**这个纯函数`isPrime()`根据给定参数`num`是否为质数，返回`true`或`false`。**

**下面是给定数字`1`、`2`、`3`、`4`的`isPrime()`、的一些测试。**

**那要写很多代码。如果我想添加更多的测试用例，我必须做一些复制和粘贴。这感觉不太对。**

**这里是相同的测试，使用 [Sazerac](https://github.com/mikec/sazerac) 。**

**[**Sazerac**](https://github.com/mikec/sazerac) **将使用这些测试用例来运行与 Jasmine、Mocha、Jest 一起工作的** `**describe**` **和** `**it**` **函数。****

**describe/it 消息是基于给定的输入和预期的输出设置的，因此测试报告非常一致:**

```
isPrime()
  when given 1
    ✓ should return true
  when given 2
    ✓ should return true
  when given 3
    ✓ should return true
  when given 4
    ✓ should return false
```

# **在现实世界中**

**事情通常会更复杂。**

**您的代码并不总是像`isPrime()`函数那样简单。以这个 React 组件为例。**

**该组件需要一个具有 3 个属性的`product`对象，`title`、`price`和`inventory`。如果缺少这些属性中的任何一个，它就会在根元素处呈现一条错误消息和一个`error`类。如果所有的道具都有值，它会呈现一个成功消息，并且没有`error`类。**

**我要用 [**酶**](http://airbnb.io/enzyme/) (测试 React 组件的牛逼)来帮助测试这个。在我的规范文件中，我将创建一个函数来挂载组件。**

**这个函数的预期返回值比较复杂，所以之前用于`isPrime()` 的`.expect()`方法在这里不能用。我就用萨泽拉克的`.assert()`*T22 的方法来代替。***

**在这个例子中，`p`对象是给定`{}`的`ProductComponent()`的返回值。如果`product`元素(在组件的根)有类`error`，上面的测试将通过。**

****在同一个测试用例上使用额外的** `**.assert()**` **调用可以增加更多的期望。****

**每个`.assert()`调用使用给定的输入`{}`为`ProductComponent()`运行一个新的测试。在本例中，运行了 3 个测试:**

```
ProductComponent()
  when given {}
    ✓ should render `error` class
    ✓ should display error message
    ✓ should not display success message
```

# **而且越来越好了**

**我们刚刚测试了“空”对象的情况，但是其他可能导致类似状态的情况呢？例如，如果`title`、`price`或`inventory` 缺失，我们会期望组件呈现相同的错误状态。**

**Sazerac 的 `**forCases()**` **方法可以让你将具有相同期望的测试用例分组。****

**导致以下测试:**

```
ProductComponent()
  when given {}
    ✓ should render `error` class
    ✓ should display error message
    ✓ should not display success message
  when given {"price":"1.11","inventory":"111"}
    ✓ should render `error` class
    ✓ should display error message
    ✓ should not display success message
  when given {"title":"p1","inventory":"111"}
    ✓ should render `error` class
    ✓ should display error message
    ✓ should not display success message
  when given {"title":"p1","price":"1.11"}
    ✓ should render `error` class
    ✓ should display error message
    ✓ should not display success message
```

**这比用`describe`、`it`、`beforeEach`等编写的同等测试要简洁得多。它还使添加新案例或根据需要更新现有案例变得非常容易。**

# **尝试一下**

**我建议采用一些现有的测试，并对它们进行重构，以使用 [Sazerac](https://github.com/mikec/sazerac) 。我希望它能降低测试的复杂性，增加可读性。**

**如果有反馈，在 twitter 上联系我 [@MikeCalvanese](https://twitter.com/MikeCalvanese)**

**[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)****[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)****[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**

**![](img/be0ca55ba73a573dce11effb2ee80d56.png)**