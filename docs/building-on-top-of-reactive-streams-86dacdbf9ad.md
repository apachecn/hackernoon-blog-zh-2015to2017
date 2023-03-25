# 建立在反应流之上

> 原文：<https://medium.com/hackernoon/building-on-top-of-reactive-streams-86dacdbf9ad>

# 反应流

*反应流*规范允许我们创建以异步方式通信的应用程序组件，同时使用背压机制维护安全性。

*反应流*正在每一种现代编程语言中实现，比如 JavaScript 和 Scala。

让我们看一个简单的例子，它将一个 ***生产者*** 连接到一个 ***订户*** 来看看流程是如何进行的。

```
**val** publisher = *Subject*[Int]

**val** subscriber = publisher.subscribe(message => *println*(message))

publisher.onNext(5)
```

在这里我们已经创建了一个 ***主题【Int】***所以我们可以调用 ***。onNext* 上的**将一条消息(**IntT25)推送到*流*中。在管道的另一端，我们有一个 ***订阅者*** ，即 ***将*** 订阅给 ***发布者*** ，并指定每个新消息都将使用一个函数进行处理。**

如您所见，我们需要两个部分，一个发布者和一个订阅者，我们需要将它们连接在一起。

## 流上的操作

有时我们不需要处理每一条消息，所以我们可以*过滤*管道。

```
**val** subscriber = publisher
  .filter(_ % 2 == 0)
  .subscribe(message => *println*(message))
```

其他时候，我们需要应用转换，所以我们*在流上映射*。

```
**case class** EvenNumber(n: Int)**val** subscriber = publisher
  .filter(_ % 2 == 0)
  .map(EvenNumber)
  .subscribe(message => *println*(message))
```

到目前为止，我们可能已经意识到流的操作与我们在任何集合中发现的操作相似。这是一个强大的功能，因为我们不必学习新的 API，我们可以使用我们已经知道的东西。

# 一个小问题

我们的问题是，要创建一个完整的管道，我们需要将所有部分连接在一起。这意味着当创建一个*订阅者*时，我们需要一个已经创建的*发布者*，并且我们必须能够访问它。

```
**val** subscriber = publisher.subscribe(message => *println*(message))
```

*订阅者需要知道发布者*，*所以我们需要将发布者传递到每个要创建订阅者的地方。*

# 共同的终点

我们希望创建一种机制来避免上述问题。解决这个问题的思路一点也不复杂。它实际上来自于一个艺术系统的状态，*阿帕奇卡夫卡*。

Kafka 使用基于*主题*的方法来划分流。我们可以在主题`A`中放置一条消息，这样只有那些收听主题`A`的人才能接收和处理这条消息。

另一方面，*发布者*和*订阅者*完全解耦。他们不了解对方。他们使用一个中心位置来发布和订阅消息。

# 建筑迷你小屋

***MiniKaf*** 是我们为了克服这个问题而开发的一个库。它公开了一个由最少的类型集支持的非常干净的 API。

让我们看看一些主要组件。

首先，我们为发布到系统的每个事件/消息提供了一个接口。

```
**trait** Event[A] {
  **def** value: A
}
```

然后，我们有了一种机制，可以在事件被发送到管道之前将类型转换为事件。

```
@typeclass **trait** ToEvent[A] {
  **def** event(a: A): Event[A]
}
```

当然，这是进行转换的默认方式。

```
**object** ToEvent {

  **implicit def** toEvent[A]: ToEvent[A] = **new** ToEvent[A] {
    **override def** event(a: A) = *E*(a)
  }
}
```

或者我们可以通过函数*使用用户特定的方式进行转换。*

```
***object** ToEvent {

  **def** event[A](a: A)(f: A => Event[A]): Event[A] = f(a)

  **implicit def** toEvent[A]: ToEvent[A] = **new** ToEvent[A] {
    **override def** event(a: A) = *E*(a)
  }
}*
```

*我们现在准备开始发送消息。*

```
*val publisher = Publisher()
publisher.publish(5)
publisher.publish("hello")*
```

## *但是等等！我们如何消费这些信息？*

*使用消息就像订阅您想要使用的消息类型一样简单。*

```
*val subscriber = Subscriber()
subscriber.subscribe[Int](e => println((e.topic, e.value)))*
```

*在这里，我们订阅了类型为`Int`的消息。下次通过流发送一个`Int`时，函数`e => println((e.topic, e.value))`将被执行。注意，`e`不是一个`Int`，而是一个`EventRecord`，其中`topic`是`"Int"`，`value`是由 ***发布者推送的实际消息。****

*这里需要注意的最重要的部分是，我们没有将 ***订阅者*** 连接到 ***发布者*** 。我们只是创建了 ***订户*** 并指定了订阅。这与 Kafka 的解耦水平相同，但我们依赖于底层实现的反应流。*

# *结论*

***然而，我们认为它们不应该被直接使用，而是应该通过我们自己的 API 来使用。***

# *招聘广告*

*[***MiniKaf***](https://github.com/anicolaspp/minikaf)如今正处于它的最开始阶段。我们正在运行时做一些不安全的工作，这些工作可能会被编译时机制取代(可能使用`Shapeless`)。此外，API 并不完整，并且缺少一些部分。任何帮助都将不胜感激。*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*