# 对片段(和活动)说“不”:用流创建视图驱动的应用程序

> 原文：<https://medium.com/hackernoon/saying-no-to-fragments-and-activities-creating-view-driven-applications-with-flow-8f7d02315442>

![](img/7ea7bf9c25c3afe575280749ee5bf905.png)

**注:我已经弃用了 Flowless(并且已经放弃使用 Flow ),支持我从头开始写的东西:** [***简单堆栈***](https://github.com/Zhuinden/simple-stack) **。**

**所以你应该记住，我不再使用它了，因为简单堆栈解决了我不喜欢的 Flow 设计的怪癖。**

事实上，这篇文章可能已经不值得一读了。

改为看这个:[https://medium . com/@ zhuin den/simplified-fragment-navigation-using-a-custom-back stack-552 e 06961257](/@Zhuinden/simplified-fragment-navigation-using-a-custom-backstack-552e06961257)

— — — — — — — — — — — — — — — — — — — — — — — — —

事实上，我以前写过一篇关于流动的文章，但是相当平淡。是时候写一篇新的更好的文章了，更直截了当一点。我也写过关于使用 Flow 0.12 和 Mortar 的[，但是那应该被认为是过时的——Flow 1.0-alpha 的架构要好得多，也更可取(因为它管理状态持久性，而以前的版本没有)。](https://github.com/Zhuinden/MortarFlowSetup/wiki/Abandoning-the-Activity-Stack-using-Flow-0.12-and-Mortar)

# 基础知识

对于那些不知道有多少片段已经被讨厌了很长时间的人来说[——嗯，没有人喜欢生命周期，没有人喜欢它们如何奇怪地与 CoordinatorLayout 一起工作，也没有人喜欢 FragmentManager 事务如何混乱，阅读](/square-corner-blog/advocating-against-android-fragments-81fd0b462c97#.lldcwxoyq)[源代码并不能真正帮助](https://android.googlesource.com/platform/frameworks/support/+/refs/heads/master/v4/java/android/support/v4/app/FragmentManager.java#416)。

考虑到这一点， [**Flow**](https://github.com/square/flow) 被创建来接管 FragmentManager 想要做的事情——成为 **backstack** (就像`addToBackStack(null)`给你的一样)，确保视图在配置改变和进程死亡时得到正确的恢复；但是*没有*就没有人真正理解[的古怪 bug](https://www.google.hu/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=fragment+not+attached+to+activity)。

你可能会问，为什么没有这种奇怪的虫子？因为从“*状态 A* 到“*状态 B* 的所有过渡都是由**你**——流量的使用者——来处理的。

# 一些历史和新的东西

Flow 实际上非常古老，尽管由于缺乏文档和可怕的示例代码，它从未真正得到改编。甚至有关于它的文章[(嗯，方有点离开，挂在流量 0.8)](/square-corner-blog/simpler-android-apps-with-flow-and-mortar-5beafcd83761#.s21vs642b) 。

为了理解*流*是如何随时间变化的，让我们看一下那篇文章中关于*流 0.8* 的例子，以及我们如何对最新的流做同样的事情。

## 国家代表权

回到流程 0.8，显示的每个视图都由一个`Object`描述，其中这个对象用`@Screen`注释。此屏幕包含当此对象设置为 backstack 时应显示的布局。

```
@Screen**(**layout **=** R**.**layout**.**album_view**)**
**public class** **AlbumScreen** **{**
  **private** **final** **int** albumId**;** **public** **AlbumScreen(int** albumId**)** **{** **this.**albumId **=** albumId**;** **}

  public int getAlbumId() {
     return albumId;
  }
}**@Screen**(**layout **=** R**.**layout**.**track_view**)**
**public class** TrackScreen **implements** HasParent**<**AlbumScreen**>** **{**
  **private final** **AlbumScreen** albumScreen**;**
  **private** **final** **int** trackId**;** **public** **TrackScreen(AlbumScreen** albumScreen**, int** trackId**)** **{**
    **this.**albumScreen **=** albumScreen**;**
    **this.**trackId **=** trackId**;**
  **}** @Override **public** AlbumScreen getParent**()** **{**
    **return** albumScreen**;**
  **}** **public** AlbumScreen getAlbumScreen() {
    **return** albumScreen;
  } **public** int getTrackId() {
    **return** trackId;
  } **}**
```

这个其实没什么变化，除了`@Screen`(和`HasParent`)不再被图书馆提供给你。你只是被告知使用任何物体，任何你想要的物体；只要它有`equals()`和`hashCode()`方法。

当然，最有意义的是键仍然告诉您想要构建什么样的布局。如果你把你的钥匙做成可打包的(或者提供一个`KeyParceler`可以把它们变成可打包的，但是这需要更多的努力)，这也是最简单的。

事不宜迟，现在在 Flow 1.0 中，您可以这样做:

如果愿意，您还可以选择使用注释来提供视图的布局标识符，您只需要提供从中提取值的逻辑。

*(注意:对于当前的 Flow 1.0-alpha，如果我们想让 Flow 处理“托管服务”和引用计数，扩展* `*TreeKey*` *而不是进行* `*HasParent*` *注释可能也是一个好主意——尽管我个人不使用它，因为我对它有疑问。)*

## 导航处理

回到 Flow 0.8，你必须实现`Flow.Listener`接口，它给你新的对象的`Backstack`(用`@Screen`标注)，以及关于你是否后退、前进或替换的`Direction`。

```
@Override **public** **void** **go(**Backstack backstack**,** Direction direction**)** **{**
  Object screen **=** backstack**.**current**().**getScreen**();**
  setContentView**(**Screens**.**createView**(this,** screen**));**
**}**
```

这有一点改变，因为`Screens`和`@Screen`不再存在，更重要的是，流不再是同步的——它是基于回调的。这是为了允许处理动画，并且只有当动画实际完成时，状态改变才会被“提交”。更重要的是，您还会收到*以前的*和*新的*堆栈对象。

在 Flow 1.0-alpha 中，您需要用下面的签名实现`Dispatcher`接口(以前是`Flow.Dispatcher`):

```
public interface Dispatcher {  
  /** 
   * Called when the history is about to change.  
   * Note that Flow does not consider the Traversal to be finished, 
   * and will not actually update the history, 
   * until the callback is triggered. 
   * Traversals cannot be canceled. * @param callback - Must be called to indicate completion of the traversal.   
   */    void dispatch(@NonNull Traversal traversal, 
                  @NonNull TraversalCallback callback);
}
```

如果我们想保持文章的简单，应该是这样的:

至于中间的上下文魔术，[那正是](https://github.com/square/flow/blob/762864967765f0f3f7f9827609f5b97fdd963a76/flow/src/main/java/com/squareup/flow/Screens.java) `[Screens](https://github.com/square/flow/blob/762864967765f0f3f7f9827609f5b97fdd963a76/flow/src/main/java/com/squareup/flow/Screens.java)` [用来做](https://github.com/square/flow/blob/762864967765f0f3f7f9827609f5b97fdd963a76/flow/src/main/java/com/squareup/flow/Screens.java)的。

— — — — — — — — — — — — — — — — — — — — — — — — — — — —

然而，值得注意的是，过去，`Flow`没有处理视图状态持久性。在这方面，你完全靠自己。现在，Flow 提供了一个`State`类，您可以将视图的状态保存到这个类中，然后将视图的状态存储到 Flow 自己的`History`堆栈中。

考虑到这一点，我们应该创建一个视图组，包含我们当前显示的视图(我称之为`root`)，这样我们就可以轻松地直接访问视图，并获得/恢复它的状态。

记住这一点，现在我们不仅有一个 backstack，而且还有视图的状态持久性。旋转和导航离开我们的视图不会再移除我们的视图状态，Flow 在内部处理大部分工作。整洁！:)

这也是处理两个视图之间动画的地方——例如，如果`previousView`存在，我们可以使用`TransitionManager.beginDelayedTransition()`。

## 在应用程序状态之间切换

还记得这个吗？

```
FragmentManager fragmentManager = getFragmentManager();
FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
fragmentTransaction.replace(R.id.fragment_container, FeedFragment.newInstance());
fragmentTransaction.addToBackStack(null);
fragmentTransaction.commit();
```

回到流程 0.8，它曾经是这样的:

```
flow**.**goTo**(new** **Track(**albumScreen**,** trackId**));**
```

现在在流程 1.0-alpha 中，是这样的:

```
Flow.get(this).set(**new** **Track(**albumScreen**,** trackId**));**
```

使用`set`检查给定的键是否已经存在(使用`equals`，如果存在，那么它将状态设置回该视图——并且所有视图的历史记录都将被清除。如果它不存在，那么它是一个新的状态，并将其附加到历史堆栈的末尾。它本质上是 *CLEAR_TOP* ，除了它实际工作。这决定了您在遍历中看到的`destination`历史堆栈。

如果您想要更细粒度的历史修改，Flow 也可以满足您:

# 旧版本中可怕的东西:迫击炮/匕首

## 旧广场“大图”

流 0.8 的例子看起来很吓人，主要是因为迫击炮和匕首 1。如果你问我，Mortar 通过 ViewPresenter 的状态持久性在概念上是有缺陷的，Dagger1 被 Dagger2 取代。尽管如此，了解我们试图解决什么问题可能是值得的，即使它已经过时了，或者由 Dispatcher 实现在外部处理。

```
@Screen**(**layout **=** R**.**layout**.**album_view**)**
public class AlbumScreen **implements** **Blueprint {**
  final int albumId; public AlbumScreen(int albumId) { this.albumId = albumId; } ** @Override String getMortarScopeName() {
    return "AlbumScreen";
  }** **@Override Object getDaggerModule() {
    return new Module();
  }** **@dagger.Module(addsTo = AppModule.class)
  static class Module {
    @Provides Album provideAlbum(JukeBox jukebox) {
      return jukebox.getAlbum(albumId);
    }
  }**
}
```

然后，定义一个表示器，它接收从 Dagger 的 ObjectGraph 注入的依赖项，由 Mortar 的作用域保留:

```
@Singleton 
public static class Presenter **extends** ViewPresenter**<**AlbumView**>** **{**
  **private** **final** Album album**;** @Inject Presenter**(**Album album**)** **{** **this.**album **=** album**;** **}** 
```

和观点:

```
**public** **class** **AlbumView** **extends** FrameLayout **{**
  @Inject AlbumScreen**.**Presenter presenter**;** **private** **final** TextView newNameView**;** **public** **AlbumView(**Context context**,** AttributeSet attrs**)** **{**
    **super(**context**,** attrs**);**
    Mortar**.**inject**(**context**,** **this);**
```

*当然，这个* `[*Blueprint*](https://github.com/square/mortar/pull/2/files#diff-8c45305219e23e9aa32a516d557a98e4)` *也可以是类似* `*@Blueprint(scope = "AlbumScreen", module = AlbumScreen.Module.class)*` *的注释。后来，它作为一个图书馆被从迫击炮中移除。*

## 砂浆是什么？

BluePrint 回到了 [Mortar 0.17 API quake](https://github.com/square/mortar/blob/master/CHANGELOG.md#version-017-2015-04-27) 之前，当时 Mortar 与 Dagger1 捆绑在一起——它本质上是一个服务定位器，其中用于`LinkedHashMap`的*范围*由一个字符串键标识，这个*范围*在配置更改后仍然存在，而[通过覆盖](/@theMikhail/system-services-are-not-just-for-the-system-ce33aab4594a#.8ym79d4y0) `[getSystemService()](/@theMikhail/system-services-are-not-just-for-the-system-ce33aab4594a#.8ym79d4y0)`，它用于提供依赖关系的分层范围(想想具有 Dagger2 的`@Singleton`和`@ActivityScope`，并在整个视图层次中看到它们，同时还在轮换之间保留它们。

Mortar 还将自己视为视图的“呈现者”(`ViewPresenter<V>`)，并处理来自活动的`onSaveInstanceState()`的委托，以便将呈现者的状态持久化到活动的包中。

## 砂浆的问题

Mortar 根本没有融入心流——你在心流中向前导航，又向后导航；而 Mortar 无法持久化视图的状态，因为它从未收到来自`activity.onSaveInstanceState(Bundle)`的回调。在导航更改之间，所有视图状态都丢失了。

结果，Mortar 的 ViewPresenter 由于多种原因而存在缺陷——这正是为什么**流**本身开始处理状态持久性。

## 流和状态持久性(超越视图状态)

但是 Flow 只提供了一种保存视图状态的直接方法——尽管它在历史中有一个槽，允许将一个包与视图状态一起保存。而 Flow 并没有提供一种方式(还没有？)要访问这个包，可以将它与一些包内部的逻辑一起使用，这样您就可以[手动](https://github.com/square/flow/issues/181)将 presenter 状态通过视图保存到一个包中。

然后你可以打电话

现在，您的视图也能够在旋转时将其状态保存到一个包中。

## 作为服务定位器的 Mortar 和“托管服务”

Mortar 还提供了一种从绑定到上下文的范围中检索 Dagger 对象图的方法，该范围可以手动创建和销毁，与活动生命周期本身无关。

这个换成了 Square 的 Flow 1.0-alpha 作为`ManagedServices`和`ServiceFactory`，我个人根本不用这些。它们将解决创建和销毁服务的问题，如果键属于同一个“流”，则在视图状态之间共享它们，从而在视图之间共享资源。

如果这行得通，那就太好了，但是我遇到了“节点引用计数”和`TreeKey`的神秘错误——所以我跳过了它。

但是没错，[的](https://github.com/square/flow/blob/master/flow-sample-tree/src/main/java/flow/sample/tree/FlowServices.java) `[ServiceFactory](https://github.com/square/flow/blob/master/flow-sample-tree/src/main/java/flow/sample/tree/FlowServices.java)` [就是会取代迫击炮的东西。](https://github.com/square/flow/blob/master/flow-sample-tree/src/main/java/flow/sample/tree/FlowServices.java)

# 使用心流的新蓝图

既然我们有了状态持久化的 backstack，我们就能够创建表示活动窗口的视图，而不必依赖活动来完成。

我们还对这个 [backstack](https://hackernoon.com/tagged/backstack) 进行了细粒度的操作，而不是试图让 [Android](https://hackernoon.com/tagged/android) 用魔法意图标志做我们想要它做的事情。

我们的意图被键参数代替，片段事务也被键本身代替。所有状态更改都由调度程序管理，这提供了可预测的行为，而不是“未附加的陈旧片段”。我们的活动和片段被定制的视图组所取代，消除了复杂性并提供了更好的性能。

通过最少的配置，我们现在可以创建视图驱动的应用程序，就像这样。

*   活动

*   钥匙

*   视角

*   提出者

视图和表示之间的分离相当简单——比活动或片段更清晰。

当我们真正需要的是一个在配置改变后仍然存在的独立的状态表示时，为什么我们还在与碎片作战呢？

虽然你可以使用导体，但我还没有用过。我更喜欢 Flow 提供的细粒度控制——并直接了解底层发生了什么。

( *Reddit 讨论*:[https://www . Reddit . com/r/Android dev/comments/5a wo5 r/saying _ no _ to _ fragments _ and _ activities _ creating/](https://www.reddit.com/r/androiddev/comments/5awo5r/saying_no_to_fragments_and_activities_creating/))

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)