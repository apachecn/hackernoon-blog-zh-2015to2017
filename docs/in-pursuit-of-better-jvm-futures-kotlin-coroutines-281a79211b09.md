# 追求更好的 JVM 未来——科特林协程

> 原文：<https://medium.com/hackernoon/in-pursuit-of-better-jvm-futures-kotlin-coroutines-281a79211b09>

![](img/b0a060dcc571b943c88aa6f60bbbf3f1.png)

我已经使用 C#的 TPL 有一段时间了，偶尔为 Android 或一些 web 东西回到 Java 总是让我讨厌它的每一点。不是 JVM 部分(在我看来 JVM 比 CLR 好得多)，而是它的编写部分(并不是说我到处都有 C#的问题)。线程和回调的实现方式，以及函数直到最近才成为一等公民。虽然 Java 8 带来了很多东西，但仍有一些问题需要解决，其中之一是未来(或异步/等待或承诺或延迟)，我强烈地感觉到 Kotlin 即使在其婴儿期也比预期的要成功。在这篇博文中，我将解释为什么你需要一个更好的异步编程模型，我与 Kotlin 的一点历史，以及 Kotlin 如何带来有趣的东西。

所有这些都是我**的个人观点**，绝不是说一个技术比另一个技术更好。然而，我将向您展示过去做错了什么，以及各种技术是如何相互启发并为范例添加新内容的。

## 使用 Android 构建案例

我将使用 Android 来构建我的案例，这是典型的开发人员的噩梦。引用 Swing 或 JavaFX 对大多数开发人员来说可能不太相关，因为现在有更多的人在使用 Android。如果你尝试过编写一个 Android 应用程序，在主线程(也叫 UI 线程)和后台线程之间来回转换，你知道我会怎么做。作为复习，我将引用[这篇教程](https://kylewbanks.com/blog/Tutorial-Android-Parsing-JSON-with-GSON)；你可以在 StackOverflow 和博客上找到大量这样的教程和技术。在任何类型的 io 或计算密集型进程的典型场景中，在完成一些调用回调或方法的操作后，您将启动一个后台线程，最终在 UI(又名`runOnUiThread`)上运行其余代码。

但是等等，有一些库可以为这样的场景编写*更好的代码*，比如 [RxJava](https://github.com/ReactiveX/RxJava) 、 [Otto](http://square.github.io/otto/) 等等。“为什么不用它们？”有人可能会问。如果你去过 UWP(通用视窗平台)的令人恐惧的地方，并且用 C#开发了一个应用程序，你可能已经尝到了我所说的简单。**任务并行库**使得大量任务的旋转变得轻而易举，并且控制哪个任务在哪个线程上运行，或者在什么上下文上执行将恢复。显然有一个学习曲线，但是一旦你掌握了它；你会在日常生活中的任何地方用任务来代替线程。虽然 Android 库提倡真正好的模式，但是没有一个模式比得上调用类似于在后台线程上运行的函数并像普通函数一样返回值的简单性。没有回调，没有事件跟踪，没有事件流，没有跟踪什么都没有。像调用这样简单的函数！这不仅简化了代码编写，也使调试变得更加容易。而不是猜测和搜索代码中的注释；您知道被调用的代码位于何处，以及调用完成后它如何恢复。

我这么说吧，你更喜欢什么？一个简单的`var user = await getUserInfo()`或(等等，我必须使用 Otto 并使用多行来显示问题的一部分):

```
// Somewhere in code
bus = **new** Bus(ThreadEnforcer.MAIN);
bus.register(this);
...// Somewhere else in code
**@Subscribe**
**public** **void** onUserInfo(User u) {
    Toast.makeText(this, this.getUserMessage(u), Toast.LENGTH_LONG).show();
} 
...// In a galaxy far far away
**public class** UserInfoFetcherRunnable **implements** Runnable {
    ...
    [@Override](http://twitter.com/Override)
    **public** **void** run() {
        /*
         * Code that does the fetching in background
         */
        ...         // carry the stupid bus around
        this.bus.post(user);
    }
    ...
}
```

仅此一点就足以让您相信代码中存在问题。如果用代码解释起来很难而且很分散，那么就很难调试。我可以用 RxJava 引用一个类似的例子。

同样，不要误解我的意思，*我并没有暗示 Otto 或 RxJava 是愚蠢的 duck tapes* ，它们完全有合法的用法。我现在能想到的一个方法是当服务器有推送事件时，在 UI 上提供一个更新。但这并不意味着它对每个场景都是完美的，对吗？另一个选项可以是[螺栓框架](https://github.com/BoltsFramework/Bolts-Android)。它的 API 几乎直接来自 C# TPL。虽然它确实为你提供了`continueWith`、`onSuccess`或`onSuccessTask`等。仍然有回调的税收，和一些`Continuation`初始化。

## 需要异步/等待

Android 的工作流只是一个例子，即使在语言层面上有异步编程结构，然后能够在一个线程池中复用它是至关重要的。不管是 HTTP 服务器，还是数据库引擎；随着时间的推移，异步范式已经证明了它的合理性。

虽然 Java 8 已经支持 [CompletableFuture](http://www.deadcoderising.com/java8-writing-asynchronous-code-with-completablefuture/) s，但 async/await 的奢华给了它某种我们都熟悉的直观性。像 Node.js 这样的回调函数也在向 async/await 支持发展，这是有原因的，Python 3.6 最近做了一个非常好的[实现](https://docs.python.org/3/library/asyncio-task.html)，C#有 TPL。

## 我和科特林的模糊约会

在尝试建立一个 Android 应用程序之前，我尝试了一两次 Kotlin。科特林那时才刚刚起步。虽然我确实很喜欢这个非常小的运行时、互操作性、简洁的语法和更简洁的方法，但我理解得很好。具体到 Android，能够传递像`fun onClick(listener: () -> Unit)`这样的回调，并像函数体`view.onClick { ... }`一样不带括号地连接它们。它还允许更简单的数据类、函数扩展和已经支持它的大量库。高兴但不满意的是，我仍然在寻找某种 async/await 支持，并尽可能使用 Kotlin 回到我的日常工作中。如果你是 Android 发烧友，想阅读更多关于为什么你应该使用 Kotlin 的文章，有很多好的文章。

# 遇见协程！！！

随着最近[kot Lin 1.1 的发布](https://kotlinlang.org/docs/reference/whatsnew11.html)似乎我的愿望就要实现了。虽然该功能还处于试验阶段，但我认为它离生产就绪不会太远，因为我能够完全表达我在应用程序开发体验中实际看到的所有场景。在我听说新版本后，为了尝试一下，我重写了一个服务，使用协程对 IP 进行地理定位。

我**不会**在这里进入关于协程的教程模式，因为你可以在[官方文档](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#composing-suspending-functions)上找到你需要的一切。对于 Android 用户来说，有一篇非常好的 [Kotlin 协程文章](/@haarman.niek/async-await-in-android-f0202cf31088#.ws939fkl8)和几个已经支持 Kotlin 1.1 协程的[库](https://github.com/metalabdesign/AsyncAwait)。在处理服务器代码时，我观察到一些关于协程的东西，我觉得它们超出了通常的 async/await 实现(甚至超出了 C#所做的)。它们不仅对您可能已经做的事情进行了语法清理，而且还为复杂场景提供了一些表达代码的强大方法。让我们来看看其中的几个:

## 通道和选择表达式

虽然 C# TPL 为您提供了任务的基本模块，并启发了许多现代异步实现；任务间的通信还没有得到很好的解决。有人可能会说你使用`BufferBlock` 或者使用这里描述的[技术](http://blog.stephencleary.com/2012/11/async-producerconsumer-queue-using.html)。但像 Golang 或 Erlang，频道或邮箱是一等公民，表达沟通更干净。Kotlin coroutines 从上面取了一页，并带有内置的通道支持([文档](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#channels))。这使您可以实现复杂的模式，如[管道](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#pipelines)、[扇出](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#fan-out)或[扇入](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#fan-in)等。将通道与选择表达式结合起来，您可以编写复杂的调度程序，如:

```
**suspend fun** chatMessageProcessor(users: ReceiveChannel<IMessage>, system: ReceiveChannel<IMessage>, timeout: ReceiveChannel<IMessage>) {
    **select**<**Unit**> {
        users.onReceive { value ->  ... }
        system.onReceive { value ->  ... }
        timeout.onReceive { value ->  ... }
    }
}
```

虽然这对于许多 Golang 人来说听起来很熟悉，但在 JVM 领域，等待多个事件中的一个，然后采取相应的行动，这确实是一种很好的方式(我已经可以看到一些 Akka 粉丝在拔头发)。这可以让您派生出一些协程，这些协程封装了一些状态，并根据来自环境的一些消息对它们进行操作。例如，等待来自 UI 的消息，然后对 DB 进行更新，或者对服务器进行 API 调用。另一个例子可能是向其他协同程序发布聊天消息，这些协同程序向同一聊天室中的其他用户推送聊天消息。这听起来熟悉吗？我们正在向演员靠拢。

## 演员

> 参与者是封装了状态和行为的对象，它们专门通过交换放入接收者邮箱的消息来进行通信。— [阿卡](http://doc.akka.io/docs/akka/current/general/actor-systems.html#actor-systems)

Kotlin coroutines 库提供了基本的 actors 支持，令人惊叹的是它的语法也很干净；您不必实现接口，也不必为礼仪目的定义所需的方法。它就像一个 go 例程或 Erlang 进程，一个简单的函数。这看起来很简单:

```
**fun** messageDeliveryActor() = actor<ChatMessage>(**CommonPool**) {
    // Do your stuff here...
}
```

像任何其他的`async`或`run`一样，它需要一个上下文，仅此而已！请记住，已经有成熟的 Java 库提供了 actor 支持。我没有破坏他们中的任何人！

## 透明上下文切换

因此，在编写服务器时，我遇到了现有包不支持或利用异步 API 的典型问题(没有 Java NIO，没有 netty，什么都没有)。Maxmind GeoIP2 库的`DatabaseReader`使用了`File`对象；现在调用任何读取文件的操作都会阻塞线程池中的线程。虽然一个解决方案是自己做一个异步实现，但这并不总是可行的。我使用的解决方案是为这种阻塞操作提供一个线程池，并让我的 IO 协程只绑定到那个上下文。这样我就不会阻塞我的请求处理线程。下面是我的查找类的内部结构:

```
findContext = newFixedThreadPoolContext(4)suspend private fun find(ip: String): CityInfo? = run(findContext) {
    // Do the blocking thread blocking lookups here
}
```

从我的请求中调用这个子例程确实是透明的。在我的例子中，我使用的是`jooby`，它支持异步 via `Deferred`。因此，我调用 find 的代码如下所示:

```
 **fun** getGeoInfo()= Deferred { req, def ->
            launch (CommonPool) {
                ...
                **val** ip = req.param("ip").value() ?: req.ip()

                // lookupEngine.find won't use CommonPool
                **val** info = lookupEngine.find(ip)
                ...
            }
        } 
```

调用`find(ip)`时，可以看到无缝转换。在没有公开使用不同线程池的知识的情况下，我在不同的上下文中调用了一个协程，并且将得到没有任何欺骗的结果。现在，由于 Kotlin 的语法和协程实现，这是可能的，并显示了它们如何携手提供这样一个透明的上下文切换。这不仅将知识抽象化，还带走了我可能不得不支付的编码成本。服务器的完整代码在这里有[。](http://chiselapp.com/user/mikeshinoda/repository/Kotlin-Maxmind/home)

## 包装它

我想我会就此打住。我会让你们自己尝试并建立自己的观点。有人可能会说 Scala 已经有了[异步/等待支持](http://docs.scala-lang.org/sips/pending/async.html)！科特林有什么让人激动或与众不同的地方？老实说，我已经尝试了很多次 Scala，但总是被语法淹没(我不知道为什么，有这么多方法可以做这么多事情)。我真的很想去 Scala town，在那里我可以混合使用函数式和面向对象的风格。但是现在，我很高兴看到一个语法上得体、干净的协程实现。Kotlin 可能很年轻，但它令人惊讶地令人愉快、稳定，有一个令人敬畏的社区，并为 JVM 带来了一直想要的协程支持。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客们下午的开始。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)