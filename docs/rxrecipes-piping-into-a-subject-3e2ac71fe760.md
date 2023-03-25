# RxRecipes:进入主题

> 原文：<https://medium.com/hackernoon/rxrecipes-piping-into-a-subject-3e2ac71fe760>

![](img/ea8fc30aa0bbddd83203d3e6be1a9c8e.png)

Image Credit: [wwardo@flickr](https://www.flickr.com/photos/wwward0/16205435108/)

> RxRecipes 系列一般指 Android 和 [RxJava2](https://github.com/ReactiveX/RxJava/tree/2.x) ，但同样的 Recipes 可以适用于任何 Rx 实现和平台( [RxJs](https://github.com/Reactive-Extensions/RxJS) 、 [RxSwift](https://github.com/ReactiveX/RxSwift) ，一个带有 RxJava 的后端等)。
> 
> 上一个 RxRecipe: [**绕到 Rx**](https://hackernoon.com/rxrecipes-wrap-your-way-to-rx-fd40eb5254b6#.p0zdyc9dn)

# 挑战:来自生命周期事件的被动来源

在我的[上一篇文章](https://hackernoon.com/rxrecipes-wrap-your-way-to-rx-fd40eb5254b6#.p0zdyc9dn)中，我谈到了一种将命令式 API 转换为反应式 API 的简单方法(使用 fromCallable)。然而，很多时候事情并不那么简单。

假设您需要一些组件来对一个[活动生命周期](https://developer.android.com/guide/components/activities/activity-lifecycle.html)事件做出反应。

*   活动停止时清理缓存。
*   每次活动暂停时记录特定的分析事件。
*   将一些 Android 特定的组件(如 TextWatcher)绑定到活动的开始。

但是，您马上会遇到将 Rx 代码放在哪里的问题。没有简单的方法或切入点。

See [The Official Android Activity Lifecycle Documentation](https://developer.android.com/guide/components/activities/activity-lifecycle.html)

这些挂钩发生在不同的点，并且可以根据用户输入、应用程序逻辑和操作系统事件以不同的顺序和部分发生多次(就像 facebook 消息弹出触发 onPause())。

我们想得到这样的东西:

All the activity lifecycle events in one simple stream. Sweet!

但是我们如何到达那里呢？

![](img/f768e86c14fce56b5409aa1c6c52fcfe.png)

The plan is coming together quite nicely… Muah-ha-ha-ha!

# 解决方法:用一个主语！

来自[官方文件](http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/subjects/Subject.html):

*同时代表一个观察者和一个可观察对象，允许从单个源向多个子订户多播事件。*

让我们回顾一下:主体既是观察者又是可观察的。

虽然主体有许多可能的用例，但它们通常被用作一种多源发射器。

我们将获取多个源(在这种情况下是多个方法回调),并将它们全部放入一个简洁的主题中。

题材很棒！他们可以隐藏输入的复杂性，并简化客户端 API，将其视为单一来源。

# 让我们看看代码

> 注意:在我们开始写代码之前，应该注意这是一种非常常见的 Android 生命周期回调方法。更多例子见 Trello 的 [RxLifecycle，或者最近开源的](https://github.com/trello/RxLifecycle) [Kickstarter Android 应用](https://github.com/kickstarter/android-oss/blob/888a3746835835016fc1a1bb32d8e2a90b8bffce/app/src/main/java/com/kickstarter/libs/BaseActivity.java)。

我们首先需要的是某种类型的发射。

我们可以创建一个包含一些状态或元数据的类(比如将保存的实例状态[包](https://developer.android.com/reference/android/os/Bundle.html)包含在创建的事件中)，但是为了简单起见，我们只使用一个简单的枚举。

现在我们只需要给活动一个自己的主题，并在合适的时间推出这些事件的合适实例。

*   我们用。初始化该类的新实例后立即创建()
*   我们在适当的时间用适当的事件对主题调用 onNext()

# 用哪个科目？

我们在这里使用了一个 [BehaviorSubject](https://github.com/ReactiveX/RxJava/blob/2.x/src/main/java/io/reactivex/subjects/BehaviorSubject.java) ,这样任何订阅者都可以立即获得活动最近发出的(当前)生命周期状态。

RxJava2 中包含的主要主题的简要概述如下:

*   [AsyncSubject](https://github.com/ReactiveX/RxJava/blob/2.x/src/main/java/io/reactivex/subjects/AsyncSubject.java) :发出最多的项，然后立即完成或出错。
*   [BehaviorSubject](https://github.com/ReactiveX/RxJava/blob/2.x/src/main/java/io/reactivex/subjects/BehaviorSubject.java) :发出最近的项目，然后是所有后续项目。
*   [发布主题](https://github.com/ReactiveX/RxJava/blob/2.x/src/main/java/io/reactivex/subjects/PublishSubject.java):发出所有后续项目。
*   [ReplaySubject](https://github.com/ReactiveX/RxJava/blob/2.x/src/main/java/io/reactivex/subjects/ReplaySubject.java) :发出到目前为止已经发出的所有项目。

# 别忘了。隐藏()

我们调用 hide()是因为我们不想弄乱行为主体本身的任何内部(比如底层的 [BehaviorDisposable](https://github.com/ReactiveX/RxJava/blob/2.x/src/main/java/io/reactivex/subjects/BehaviorSubject.java#L388) )。

当我们取消订阅时，我们只想处理主题的订阅者和主题之间的连接。而不是主题及其基础订阅。

## RxRecipes 系列的下一步:

![](img/19493eac1376e5034f79191ea305abe9.png)

TBD — I’ll update this with a link to the next post once it’s done.

喜欢这道菜吗？

不同意执行？

对上面的某句话有火热的看法吗？

我很乐意收到你的来信，请在下面留下你的评论！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)