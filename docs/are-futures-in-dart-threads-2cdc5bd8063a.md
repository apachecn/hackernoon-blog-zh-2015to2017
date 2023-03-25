# 期货是飞镖线吗？

> 原文：<https://medium.com/hackernoon/are-futures-in-dart-threads-2cdc5bd8063a>

嗯，*没有*。Dart 有一个执行的[单线程，或事件循环](https://webdev.dartlang.org/articles/performance/event-loop)。然而，通常你会看到使用 *Future* 的代码有一个注释，大意是*“//避免竞争条件:检查…*。那是什么意思？

> **期货新手？**阅读 Dart 网站上的[异步编程:未来](https://www.dartlang.org/tutorials/language/futures)。

虽然 *Dart* 只有一个执行线程，但它可以与运行在单独线程中的其他代码(Dart 或其他代码，如服务器端代码)进行交互。*未来< T >* 作为 API 的意思简单来说就是“*在一个* [*未来*](https://hackernoon.com/tagged/future) *时间点*获取一个值，T”。那次？它可能在微任务循环的末尾，可能在一秒钟之后，可能在从磁盘读取文件之后，可能在 RPC 完成之后。也可能永远不会(例如，由于超时)。

> 你可以在 GitHub 上查看这篇博文中的所有例子。

我们来介绍一个简单的例子:

我们打印字符串“1”、“2”、“3”和“4”——看似按顺序，但实际上它将打印“1”、“4”、“2”和“3”。那是因为“1”是同步*打印*的，而“2”是打印在*微任务循环*的末端，“3”是打印一个未来*事件循环。*

您还可以使用*完成器* API 来改编非*未来* API:

# 但是，[隔离](https://api.dartlang.org/stable/1.23.0/dart-isolate/dart-isolate-library.html)呢？

Dart 能够产生独立的进程，称为*隔离*(dart 2 js 中的 web workers)，它们在主程序运行时不共享内存，但是能够异步运行，在另一个进程(实际上是一个线程)中能够在不阻塞主线程的情况下进行计算。

在下面的例子中，我们计算斐波那契数列两次——一次在同一线程中同步进行，一次在另一个线程中异步进行。

在这个例子中，仍然使用了*Future*——它只是表示“将在未来执行”，还记得吗？在这种情况下，我们产生另一个隔离，要求它计算，当我们有结果时，未来完成。例如，如果您的计算机负载过重，可能需要一段时间。

看起来很困惑？*隔离* API 是相当低级的。当在您自己的代码中使用时，我建议查看[包:从包:stream_channel](https://pub.dartlang.org/packages/isolate) 中隔离或 [*IsolateChannel* 类。](https://www.dartdocs.org/documentation/stream_channel/1.6.1/stream_channel/IsolateChannel-class.html)

再举一个例子，这次是文件 I/O。我们可以*使用隔离来模拟[文件#readAsStringSync](https://api.dartlang.org/stable/1.23.0/dart-io/File/readAsStringSync.html) 和[文件#readAsString](https://api.dartlang.org/stable/1.23.0/dart-io/File/readAsString.html) 之间的差异:*

在这种情况下，我们读取 *"file.json"* 三次—一次同步，一次异步(即在 Dart VM 管理的另一个线程中)，最后一次同步，但在另一个隔离中。

从 Dart 1.22.0 开始，我们引入了一个有限的联合类型，[*Future or<T>*](https://api.dartlang.org/stable/1.22.1/dart-async/FutureOr-class.html)——它代表*未来<T>或 *T。例如，看一下缓存未来的结果:**

我希望你喜欢这篇简短的博文，如果你对在 Dart 中使用 futures 或异步 API 有任何疑问，请在这里或 twitter 上发表评论。谢谢！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！