# 更简单的 Android 应用，无流量 1.0-RC2(或流量 1.0-alpha2)

> 原文：<https://medium.com/hackernoon/simpler-android-apps-with-flow-1-0-alpha2-or-flowless-1-0-rc1-228a9ae8a5a2>

![](img/7ea7bf9c25c3afe575280749ee5bf905.png)

**注意:我已经弃用了 Flowless(并且已经放弃了使用 Flow)而支持我从头开始写的:** [***简单堆栈***](https://github.com/Zhuinden/simple-stack) **。**

**所以你应该记住，我不再使用它了，因为简单堆栈解决了我不喜欢的 Flow 设计的奇怪之处。**

— — — — — — — — — — — — — — — — — — — — — — — — —

这是[对片段和活动说不:用流创建视图驱动的应用](/@Zhuinden/saying-no-to-fragments-and-activities-creating-view-driven-applications-with-flow-8f7d02315442#.7v27hfa5h)的后续文章。

虽然我声称那篇文章会切中要点，但它更多的是一个关于最近使用 [Flow 1.0-alpha2](https://github.com/square/flow) (和 [Flowless](https://github.com/Zhuinden/flowless/) )、[与过去使用 Flow 0.8](/square-corner-blog/simpler-android-apps-with-flow-and-mortar-5beafcd83761#.3lhst05ys) 的情况的比较。因此，有人可能会说，它毕竟没有直奔主题。

我们开始吧，好吗？

# 流程:幕后经理

正如[在上一篇文章](/@Zhuinden/eli5-what-does-squares-flow-library-do-ecd497b61dc3#.iibimfys2)中所述，Flow(以及`Flow`实例本身)的主要任务是管理 [backstack](https://hackernoon.com/tagged/backstack) 。它真正关心的是`History`(本质上是一个`List<Object>`，并公开允许[操作](https://hackernoon.com/tagged/manipulating)它的操作符。

# 键:应用程序状态的表示

与只使用活动相比，使用流的主要区别在于，它没有由一系列意图创建的隐式 backstack 来打开一堆活动，而是有一个“关键”对象的历史，这些对象只是一般的可打包对象，用`hashCode()`和`equals()`来表示应该显示什么视图。

*(事实上，它不一定是一个观点，如果你是一个异教徒，你甚至可以用片段来描述它。不，真的；只需使用新增加的* `*FragmentManager.commitNow()*` *，而不要使用* `*addToBackStack()*` *，推迟 backstack 操纵改为 Flow。理论上应该可以，虽然我必须承认我还没试过。)*

一个可能的密钥示例如下:

但是如果您不喜欢从注释中提取值，那么您可以只使用接口和方法调用。看你的了！

这允许对应用程序的状态进行简单的初始化。

它还允许您轻松地设置任何您想要用插装测试来测试的视图。

你可能会想，“等等，这是一大堆方法，我以为这会让我的生活更简单？!"

事实上，确实如此。确切地说，因为这种编码在键中的“行为”曾经被随机地扔进片段中，直接操纵活动中的工具栏标题。

在这里，这是您可以在 dispatcher 实现中全局执行的操作，并且它也可以在 back press 上以最小的工作量正确设置(事实上，它开箱即用)。

# 自定义视图，并倾听生命周期

这是一个[无流](https://github.com/Zhuinden/flowless/)特有的特性，部分依附于原始流中的`InternalLifecycleIntegration`片段之上。

您通常需要知道您的视图什么时候准备好，它的状态什么时候恢复。您还必须知道视图何时被终止，以便您可以取消订阅，或者从事件总线中取消注册。

以前的流示例曾经显示`onAttachedToWindow`和`onDetachedFromWindow`作为要使用的回调，但这实际上是不可靠的。有调用`onFinishInflate()`而不调用`onAttachedToWindow()`的情况。在这种情况下，您不会收到对`onDetachedFromWindow()`的任何回调，这意味着您可能会丢失您的状态！

如果你很好奇，这很可能就是为什么 [**广场/协调者**](https://github.com/square/coordinators) 库存在的原因。

相反，Flowless 引入了`FlowLifecycles.ViewLifecycleListener`接口，如果你实现了这个接口，那么它将为你提供回调:`onViewRestored()`和`onViewDestroyed()`。

## 额外的生命周期回访

在其他情况下，比如写一个`CameraView`，你可能需要监听许可结果，或者活动结果。

对于`onActivityResult`和`onPermissionResult`，你需要手动将这些回调委托给调度器。

对您隐藏这一点的一个可能的基本活动如下:

添加许可结果委托后，调度程序会将该事件转发给当前活动视图。

这样，您可以在视图中处理任何您需要的生命周期事件回调。

# ServiceProvider:在视图层次结构中共享服务

这是无流中的一个新类，它的对应物曾经是`Flow.Services`。与其相对应的`ServiceFactory`。`ServiceProvider`实际上只是一个`Map<Object, Map<String, Object>>`，它存储在 Flow 的`InternalLifecycleIntegration`中，以在配置变化时保存它们，这与本质上是一个`Map<String, Map<String, Object>>`的 Mortar 非常相似。当然，这种情况下的初始键是`Key`本身，而不是指定为字符串的“作用域名称”。

不同之处在于，在最初的流 1.0-alpha2 中，`Flow.Services`是基于引用计数、基于诸如`TreeKey`或`MultiKey`之类的接口在内部管理的。这可能会导致问题，即使你不使用它，这是导致崩溃，使我分叉流，并创建无流摆在首位。

相反，在 Flowless 中，`ServiceProvider`希望您在 Dispatcher 中以您认为合适的方式设置它。

通过[重新定义](https://github.com/Zhuinden/realm-book-example/commit/619546401dcade1fbc02001e8b12d71bfcec2952) `[getSystemService()](https://github.com/Zhuinden/realm-book-example/commit/619546401dcade1fbc02001e8b12d71bfcec2952)`的魔力，`ServiceProvider`允许你从你的上下文中获得任何服务，就像你可以通过`Flow.get(context)`获得流量一样。

## 共享 Dagger2 组件

在 MVP 示例中，这就是如何在视图层次结构中向下提供作用域组件的。这允许我们从任何上下文中获取 Dagger2 组件—主要是在自定义视图的视图层次结构中。

# Dispatcher:它决定了状态改变时会发生什么

基于流的应用程序的最后也是最重要的组件是它的 Dispatcher 实现。这是全局处理，当你从 A 键到 b 键时应该发生的事情。

最典型的实现将保持前一视图的状态，膨胀新视图，将状态恢复到新视图，移除前一视图，添加新视图，并回调以发出完成信号。

以下示例还通过`DaggerService.TAG`添加 Dagger2 组件，并将其与给定的键相关联，从而使该组件在视图层次结构中可访问。

在中间，您可以看到为当前键设置的服务:Dagger2 组件。

这基本上就是“奇迹”发生的地方。在本例中，它有 60 行注释，而 FragmentManager 有 2000 行。我想简单就是胜利。

# 结论

希望这有助于理解如何使用[无流](https://github.com/Zhuinden/flowless/)，以及如何创建一个简单的基于 MVP(或者甚至是基于 MVVM 的数据绑定或 RxJava！)应用程序，除了明确的背景说明之外，不使用片段或任何种类的附加“神秘民间传说”。

该应用程序仅由简单的可打包 POJOs 驱动，你只需编写一次如何在状态之间切换。比`FragmentTransaction`和`Intent`都简单

本文中多次引用的例子在 [**这里**](https://github.com/Zhuinden/flowless/tree/master/flowless-mvp-example) 可用。流主要负责`presentation`层的工作方式。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)