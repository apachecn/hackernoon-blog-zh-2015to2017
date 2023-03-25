# 愤怒的角状不对称管道&邪恶的猫王操作员

> 原文：<https://medium.com/hackernoon/the-angry-angular-asyncpipe-the-evil-elvis-operator-89293e37e04d>

> `async`烟斗和猫王(？)运算符非常有用…

**但只有在你做原型| |开发的时候。**

这些看似良性且超级方便的功能实际上令人担忧。虽然它们是对模板的小改动，但实际上比您想象的要多得多。此外，它们是隐藏潜在问题的糟糕实践，糟糕的代码……但我们稍后会谈到。

首先，简单介绍一下 angular 实际使用你写的 html 模板的方式。Angular 必须编译 html 并把它转换成 javascript，非常非常漂亮的 javascript。至少编译器已经尽了最大的努力，你知道……使用了所有的 Elvis 操作符和异步管道。

编译器将这个模板变成一个 [ngFactory](https://hackernoon.com/tagged/ngfactory) 。如果你正在使用 AoT(提前)编译，那么这是一个在你的文件系统上生成的实际文件，你可以打开它来阅读、检查和编辑它。这是一次很棒的学习经历。

> 很开心，很多头疼，很多白头发。

否则，编译器会在运行时、内存中、浏览器中为每个客户端创建 ngFactory，但这是另一篇文章的主题。你现在需要知道的是，ngFactory 是渲染你的视图的东西。为了简单起见，可以把它想象成一堆带有一些事件监听器的`document.createElement` 调用，用于变化检测和其他魔法。

# 拆毁工厂

当您添加这些特性中的任何一个时，ngFactory 的代码都会发生相当大的变化，特别是在变化检测方法`detectChangesInternal`中传递给插值器的参数。

> 一个简单的字符串插值看起来像这样，所以我们有一个比较的基础

```
{{ response.email }}
```

I’ve added some (generous) white-space for readability. I’ve also removed a lot of things to highlight the important bits.

这很简单。如果有变化，`detectChangesInternal` 触发并插入字符串`this.context.response.email`，并将其设置为`currVal_0`的值。然后，它将`this._text_1`的文本值(用`renderer.createElement()`创建的元素)设置为我们的插值字符串。

# 猫王的，Elivs 的，Elvi 的还是精灵的？

这个操作符防止了许多运行时错误。如果你试图访问一个并不存在的嵌套很深的属性，你会有一段不好的时间。这就是这个运算符的作用。但是在你开始写这样的东西之前:

```
{{ user?.account?.service?.joined?.dateFormatted }}
```

> 这是我们得到的，我用几种不同的方式格式化了它

I’ve excluded the rest of the method, for brevity.

这只是在一个页面上呈现一个字符串。四个嵌套的三元运算符。现在想象一下，如果你的模板中有这样的东西，会是什么样子:

```
Service: {{ user?.account?.service?.name }}
Formatted: {{ user?.account?.service?.joined?.dateFormatted }}
Raw: {{ user?.account?.service?.joined?.dateRaw }}
```

在里面放一些函数，可能是一两个可见的…

`*ngFor="let user of users"`

然后乘以组件的数量…这就变成了一场噩梦。很快的。

人们想知道为什么他们的 ng2 应用程序运行缓慢且不稳定。

谢天谢地，有办法避免这种情况。详情如下。

# 异步-只有 5 个字符，对吗？

异步管道将`ValueUnwrapper` 中的`reset` 和`unwrap` 方法添加到 ngFactory 中，这些只是一些我们并不真正需要进入的帮助器函数。肉是来自`AsyncPipe`*的`transform`方法，它基本上将价值包装在一个可观察的(或者有时是一个承诺)中。*

> *让我们看看下面的代码编译成了什么*

```
*{{ (response | async) .email }}*
```

*编译器还在条件中添加了另一个检查，确保从`AsyncPipe.transform`返回的值是一个包装值(`value.wrapped`而不仅仅是`value`)。不是非常昂贵的操作，但仍然增加了一些复杂性。*

*我要重点介绍的部分是`AsyncPipe.transform`。如上所述，这种方法创建了一个可观察对象，并订阅它(魔术)。当可观察对象发出一个值时，`AsyncPipe`标记`ViewRef`进行检查，认为 AngularJS 脏检查。但是在 ng2 里就不一样了。*

*变更检测上升到根元素。这意味着带有`AsyncPipe`的组件和根组件之间的每个组件都触发了它们的变更检测方法(标记为 dirty/forCheck)。*

*还有更多的事情要做，但是你明白了。这需要执行大量代码，增加了复杂性。*

*如果这个管道被添加到一个 5 层深的组件中，它会对您的性能产生巨大的影响。尽管如此，angular 团队在变更检测的实现上做了一些正确的事情。如果在实际运行变更检测之前，异步管道的数量发出了一个新值，那么每个被标记的组件只被检查一次。*

*然而，可能发生完全相反的情况，其中每个订阅连续发出一个值，并且所涉及的组件被检查多次，这可能是非常昂贵的操作。*

# *结论(如何修复)*

*elvis 操作器和异步管道都可以一击解决。*

> *国家管理- 1 真理的源泉*

*您的状态管理解决方案是:*

*   *真理的终极源泉- `AppStore`*
*   *不变的*
*   *描述数据的形状-提供默认值*

*最后一点很重要。如果你的组件订阅了你的状态，或者它的一个属性，而不是一个 http 请求，那么`AppStore`可以立即以组件需要的确切形式向组件发送默认值。然后，组件可以在服务上触发一个方法，服务发出一个 API 调用，当它完成时，它将更新状态，最后，由状态变化触发，`AppStore`向组件发出新数据。*

*哇哦。如此简单。许多伟大的。*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*