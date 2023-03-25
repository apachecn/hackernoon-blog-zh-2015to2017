# 异步等待颂歌

> 原文：<https://medium.com/hackernoon/an-ode-to-async-await-7da2dd3c2056>

![](img/a1c16136edc82215d21d4c31dd3ee075.png)

the universe runs on one thread

*随着 async-await 的* [*消息*](https://blog.risingstack.com/async-await-node-js-7-nightly/) *即将在 Node 7 上发布(无需翻译)，我决定发表一篇帖子来庆祝这一美妙的语言构造。近年来，async-await 已经成为我最喜欢的实现异步业务逻辑的方式。这是一个很好的例子，说明了更高阶的抽象如何对我们的日常工作产生巨大的影响——代码更简单、可读性更强、包含的样板文件更少，但仍然和最好的替代方案一样高效。*

## 生活中有些事情需要时间

不是所有的事情都能立即完成。软件中的一些操作需要很短的时间才能完成，这给在为串行执行设计的系统上实现带来了非常有趣的挑战。如果你需要通过网络访问服务器，你必须等待它的响应。既然 CPU 被设计成一个接一个地运行操作码而不等待，那么它们在此期间做什么呢？

这就是*异步*和*并发*的基础。

## 为什么不干脆封杀？

假设我们可以暂停执行并阻塞，直到预期的响应到达。这通常不是一个好主意，因为我们的程序将对任何其他正在进行的事情保持不响应。如果我们正在实现一个前端应用程序——如果用户试图与它交互，而我们阻止了它，会发生什么？如果我们正在实现一个后端服务——如果一个新的请求突然进来会发生什么？

让我们从纯粹的开始，从不朽的`[selec](http://man7.org/linux/man-pages/man2/select.2.html)t`函数之类的最少抽象和低级 API 开始。如果我们不想阻塞，另一种选择是立即返回，或者换句话说，*轮询*。这也感觉不对，[忙等待](https://en.wikipedia.org/wiki/Busy_waiting)听起来从来都不是个好主意。

我们需要别的东西。我们需要抽象。

## 为什么多线程是邪恶的

这个问题的传统解决方案是在操作系统级别上提供的一个抽象— [*多线程*](https://en.wikipedia.org/wiki/Thread_(computing)) 。我们想阻塞，但不想阻塞主执行上下文。因此，让我们创建额外的可以并行运行的执行上下文。但是如果我们只有一个单核的 CPU 呢？这就是抽象的由来——操作系统将为我们在多个执行线程之间进行多路复用和透明跳转。

这种方法非常受欢迎，事实上，互联网上的大部分内容都是以这种方式提供的。Apache HTTP Server 是世界上最受欢迎的网络服务器，拥有超过 T2 40%的市场份额，传统上依靠 T4 的独立线程来处理每个并发的客户端。

依靠线程神奇地解决并发问题的问题是，线程通常在资源方面是昂贵的，并且在使用时还会引入显著的额外复杂性。

先说复杂。线程代码可能看起来更简单，因为它可以*同步*并阻塞，直到一切就绪。问题是，我们通常无法控制一个线程何时停止运行，另一个线程何时启动(上下文切换)。如果我们有几个线程依赖的共享数据结构，我们需要非常小心。如果一个线程开始更新数据，并在完成更新之前被切换，另一个线程可以从不一致的状态中恢复。这个问题引入了同步机制，例如[互斥](https://en.wikipedia.org/wiki/Lock_(computer_science))和[信号量](https://en.wikipedia.org/wiki/Semaphore_(programming))，它们从来都不是[喜欢使用的](http://blog.nahurst.com/thread-synchronization-issues-romance)。

第二个问题是成本，或者更具体地说是线程导致的资源开销。*调度器*是操作系统中负责[在线程运行时协调](https://en.wikipedia.org/wiki/Scheduling_(computing))的实体。线程越多，操作系统花在决定谁应该运行而不是实际运行它们上的时间就越多。更严重的是记忆力的问题。每个线程都有一个执行[栈](https://en.wikipedia.org/wiki/Call_stack)，通常会保留几兆内存，其中一些甚至不得不[非分页](https://blogs.technet.microsoft.com/askperf/2007/03/07/memory-management-understanding-pool-resources/)(所以[虚拟内存](https://en.wikipedia.org/wiki/Virtual_memory)不一定有帮助)。当运行大量线程时，这经常成为瓶颈。

这些不是理论问题，它们以一些非常实际的方式影响着我们周围的世界。首先，它们导致了当今互联网上可接受的非常差的负载标准。像 [Reddit 死亡拥抱](http://www.urbandictionary.com/define.php?term=reddit%20hug%20of%20death)这样的荒谬事情不断发生，因为许多服务器无法处理超过几千个并发连接。这就是所谓的 [C10K](http://www.kegel.com/c10k.html) 问题。这很荒谬，因为通过稍微不同的架构(不是基于线程)，这些相同的服务器可以轻松处理成千上万的并发连接。

## 所以线程是不好的——现在呢？

并不是线程真的不好，而是我们不应该依赖它们作为我们仅有的*并发抽象。我们必须开发即使在单线程系统上也能提供相同并发自由度的抽象。*

这就是我爱[节点](https://nodejs.org)的原因。 [JavaScript](https://hackernoon.com/tagged/javascript) ，由于[不相关的](http://stackoverflow.com/questions/39879/why-doesnt-javascript-support-multithreading)限制，迫使我们使用单个执行线程。乍一看，这似乎是这个生态系统的一大缺点，但实际上是塞翁失马，焉知非福。如果我们没有线程，我们必须开发不依赖线程的强大的并发工具。

如果我们有一个以上的 CPU 或者一个以上的内核会怎么样？如果 Node 是单线程的，我们如何利用它们呢？在这种情况下，我们可以简单地在同一台机器上运行 Node 的多个实例。

## 让我们玩一个现实生活中的例子

为了保持讨论的基础，让我们来看一个我们想要实现的现实场景。让我们构建一个类似于 [Pingdom](https://www.pingdom.com/) 的服务。给定一组服务器 URL，我们的服务将通过以 10 秒为间隔发出恰好 3 次 HTTP 请求来“ping”每个 URL。

该服务将返回未能回复的服务器列表以及它们没有正确响应的次数。没有必要并行 ping 不同的服务器，所以我们将逐个处理列表。最后，当我们等待服务器响应时，我们不会阻塞主执行线程。

我们可以通过实现下面的`pingServers`函数来总结我们的整个服务:

## 用线程实现伪代码

如果我们使用多线程并允许自己阻塞，实现的伪代码应该是:

为了确保我们不会意外地依赖线程，在下一节中，我们将使用*异步代码在节点上实现服务。*

## 第一种方法—回访

节点依赖于 JavaScript 事件循环。因为它是单线程的，API 调用通常不会阻塞执行。相反，没有完成的命令会在完成后立即发布一个事件。我们可以指定一个回调函数在事件被发布时运行，并将我们业务逻辑的剩余部分放在那里。

关于回调的标准抱怨是著名的*末日金字塔*，在那里你的代码最终看起来像缩进的[烂摊子](http://callbackhell.com/)。我对回调的最大问题实际上是不同的，是它们不能很好地处理*控制流*。

什么是控制流？正是这些`for`循环和`if`语句需要实现基本的业务逻辑规则，比如对每台服务器精确地执行 ***3 次*** ，如果 失败，则只在结果中包含这台服务器。试着用一个`[forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)`和`[setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setTimeout)`来实现这个逻辑，你会发现它并不像你想象的那样容易处理回调。

那我们该怎么做呢？我所知道的用回调实现非平凡控制流的更灵活的方法之一是构建一个[状态机](https://en.wikipedia.org/wiki/Finite-state_machine):

这是可行的，但不像我希望的那样简单。让我们探索一个使用额外依赖项的替代实现——一个名为 [async](https://github.com/caolan/async) 的回调控制流专用库:

这个稍微好一点，短一点。这是不是直截了当，一目了然？我认为我们可以做得更好。

## 第二种方法——承诺

我们对第一种方法并不十分满意，改进的方法是使用更高层次的抽象。[承诺](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise)持有尚未确定的[未来](https://hackernoon.com/tagged/future)值。它是一个立即返回的占位符，即使定义它的异步操作尚未完成。关于承诺的有趣的事情是，它们允许我们立即开始处理这个未来的价值，并在它最终解决的时候继续将实际发生的行动与它联系起来。

我们将把`pingServers`改为 return a promise，并将其用法改为:

大多数现代异步 API 支持回调承诺。在我们的例子中，我们将把 HTTP 请求建立在基于承诺的[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) 上。

我们仍然有控制流的问题。我们简单的逻辑怎么可能用承诺来实现？在我看来，[函数式编程](https://en.wikipedia.org/wiki/Functional_programming)最适合承诺，在 JavaScript 中这通常意味着拔出 [lodash](https://lodash.com/) 。

如果我们想并行 ping 服务器，事情会变得非常简单，我们可以使用类似于`[map](https://lodash.com/docs#map)`的操作将我们的 URL 数组转换成一个承诺数组，该数组解析每个 URL 中的失败次数。因为我们想按顺序 ping 服务器，所以事情有点复杂。因为每个承诺都需要与前一个承诺的`[then](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then)`联系起来，所以我们需要在不同的迭代之间传递数据。这可以通过`[reduce](https://lodash.com/docs#reduce)`或`[transform](https://lodash.com/docs#transform)`操作中的*蓄能器*来实现:

嗯..我不得不说这对眼睛来说也不容易。实际上，我很难在写完 5 分钟后跟上里面的内容。为了帮助澄清这种混乱，我认为如果我们将同一个精确的实现分成两个独立的更小的函数会更容易:

这稍微清楚了一点…但是累加器仍然使整个事情变得复杂。

## 第三种方法——异步——等待幸福

拜托，我们只是试着按顺序连接几台服务器。前两种方法为我们提供了有效的实现，但是它们并不简单。这是为什么呢？也许是因为我们人类倾向于发现程序性思维对于商业逻辑来说更直观一些。

我第一次见到[T2*异步等待*模式是在我做微软 Azure 的一个附带项目的时候，并且学了一点点](http://stackoverflow.com/questions/18498942/why-shouldnt-all-functions-be-async-by-default) [C#和。NET](https://msdn.microsoft.com/en-us/library/mt674882.aspx) 通过代理。我立刻被震撼到了。这是两全其美的方法——直接的过程思维，没有线程阻塞的代价。这些家伙干得太棒了！

我很高兴看到这种模式渗透到许多其他语言中，如 [JavaScript](https://github.com/tc39/ecmascript-asyncawait) 、 [Python](https://www.python.org/dev/peps/pep-0492/) 、 [Scala](http://docs.scala-lang.org/sips/pending/async.html) 、 [Swift](https://github.com/yannickl/AwaitKit) 等等。

我认为对 async-await 最好的介绍就是直接进入代码，让它自己说话:

现在我们在谈话。易于书写，易于阅读。代码正在做的事情很快就一目了然了。而且是完全*异步*。耶。我说不出比杰克·阿奇博尔德的话更好的了:

> 他们很聪明。他们很聪明，我想修改法律，这样我就可以和他们结婚了。

请注意，这个实现与我们以前只能使用线程和阻塞实现的同步流是多么相似。它是怎么做到不堵的？幕后有很多神奇的事情发生。我不会深入讨论它，但是关键字`await`不会阻塞，它会让事件循环中的其他事情执行。当等待的结果准备好时，执行可以从这一点恢复。

另外，这个版本的`pingServers`的调用方式和之前那个有承诺的版本是一样的。一个`async`函数返回一个`promise`，使得与现有代码的集成尽可能简单。

## 摘要

我们已经切断了对并发线程的依赖，并尝试了 3 种不同风格的*异步*代码。*回调*、*承诺*和*异步等待*是为类似目的设计的不同抽象。哪个更好？这是个人品味的问题。

很高兴看到历史上这三种风格是如何代表三代 JavaScript 的。回调规则在早期直到 ES5。在 ES6 时代，JavaScript 作为一个整体向现代语法迈出了一大步。当然，我们庆祝的主题——async-await，在 ES7 的前沿。这是一个了不起的工具，使用它！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)