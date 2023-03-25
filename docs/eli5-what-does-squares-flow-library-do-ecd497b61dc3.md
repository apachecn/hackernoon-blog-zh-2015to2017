# Eli 5:Square 的流量库是做什么的？

> 原文：<https://medium.com/hackernoon/eli5-what-does-squares-flow-library-do-ecd497b61dc3>

![](img/7ea7bf9c25c3afe575280749ee5bf905.png)

[**流**](https://github.com/square/flow/) (或者我的叉吧，[无流](https://github.com/Zhuinden/flowless))是背刺。嗯，[技术上来说](https://hackernoon.com/tagged/tachnically)是持有[逆推](https://hackernoon.com/tagged/backstack)的东西，叫做**历史**。

`History`是——你可以猜到——应用程序在给定时间的历史。这个`History`实际上只是一个对象列表——尽管它的索引是反向的，其中`get(0)`返回最后插入的元素，而`get(size-1)`返回第一个插入的元素。一堆，如果你愿意的话。

```
A  ← top **element**, .get(0)
-
B
-
C
-
D
```

作为列表:

`[D,C,B,A]`

在这个列表中，对象`D`、`C`、`B`、`A`只是一个类的简单实例，带有一些数据字段和 hashCode/equals 方法；也称为“值对象”。这只是一个看起来像这样的类

```
**public class** A {
    **private final** String **data**;

    **public** A(String data) {
        **this**.**data** = data;
    }

    @Override
    **public boolean** equals(Object object) { *// auto-generated* **if**(object == **null**) {
            **return false**;
        }
        **if**(!(object **instanceof** A)) {
            **return false**;
        }
        **return** StringUtils.equals(**this**, **data**, ((A) object).**data**);
    }

    @Override
    **public int** hashCode() { *// auto-generated* **return** getClass().hashCode() + 31 * (**null** == **data** ? 0 : **data**.hashCode());
    }
}
```

所以，这个`A`完全简单的对象被称为`Key`，它只包含一个字符串并覆盖 equals/hashCode。这代表了您在应用程序中的位置。

在活动的情况下，这意味着您有以下活动:

`DActivity, CActivity, BActivity, AActivity`

在`onStop`中`DActivity`、`CActivity`、`BActivity`会在背景中(但不会被破坏)，而`AActivity`在前面(调用`onStart()`和`onResume()`)。

当你在`BActivity`中启动`AActivity`时，发生的事情是`BActivity`被放到`onPause()`中，`AActivity`启动并被放到……实际上，让我来抓取文档

> 执行活动 A 的 onPause()方法。
> 
> 活动 B 的 onCreate()、onStart()和 onResume()方法按顺序执行。(活动 B 现在有了用户焦点。)
> 
> 然后，如果 Activity A 在屏幕上不再可见，它的 onStop()方法就会执行。

但在这种情况下，我不得不:

1.)查看文档，了解发生了什么

2.)backstack 是隐式的，所以立即从`AActivity`到`CActivity`是非常非常困难的。你需要修补`CLEAR_TOP`意图标志，并希望它实际工作。如果没有，你甚至可以在你的活动上设置`singleTop` launchMode，以某种方式最终让它工作。

— — — — -

就心流而言，这并不复杂。当你打电话时

`Flow.get(this).set(A.create());`

那么你收到的就叫做“遍历”，是“分派”的。简而言之，它告诉你发生了一些事情，并显示你之前在应用程序中的位置:`[D,C,B]`，以及你正在前往的位置:`[D,C,B,A]`。

如果你在`[D,C,B,A]`中调用`Flow.get(this).set(C.create());`，那么你将得到一个遍历，表明你在`[D,C,B,A]`中，你将到达`[D,C]`。

发生了什么(我从文档中抓取的东西)完全由你来写*，所以你可以直接控制发生了什么。*

这通常意味着您最终会:

-检查您是否仍处于完全相同的状态，如果是，则不做任何事情(告诉流您已经处理了遍历)
-保持当前布局的状态
-移除当前布局
-膨胀新布局
-恢复新布局的状态(如果状态存在)
-添加新布局
-告诉流您已经处理了遍历

([这里有一个简单调度器的例子](https://github.com/Zhuinden/flowless/blob/master/flowless-sample-transitions/src/main/java/com/zhuinden/flowtransitions/TransitionDispatcher.java#L40-L62))

— — — — — —

这实际上很棒，原因有很多:

1.)在`onPause()`中，后台的视图不会被破坏，所以您不必确保在返回时“重新加载数据并刷新”

2.)视图被销毁，从而释放了内存，这更具性能

3.)活动转换有额外的开销，这使得它们比视图转换慢(同样，`TransitionEverywhere`对视图有效，但对活动/片段无效)

4.)现在，您可以直接访问“您在应用程序中的位置”，还可以访问“当我从这里到那里时会发生什么”，这样设置遍历实际上非常容易:

`[D,C,B,A]` = > `[C,E,B]`

因为这实际上只是

```
Flow.get(**this**).setHistory(
        History.newBuilder()
            .push(C.create())
            .push(E.create())
            .push(B.create())
        .build()
);
```

您打算如何使用活动堆栈和意图标志来实现这一点？没有该死的线索！

— — — — — — — — — — — — — — — — — — — — — — —

— — — — — — — — — — — — — — — — — — — — — — —

TL；DR: **Flow** 是一个 backstack 库，允许您轻松地保持/恢复视图的状态，并允许您处理在给定时刻的位置和前进方向之间的变化。这差不多就是它所做的一切。

*(Reddit 讨论线程:*[*https://www . Reddit . com/r/androiddev/comments/5h0er 6/Eli 5 _ what _ does _ squares _ flow _ library _ do/*](https://www.reddit.com/r/androiddev/comments/5h0er6/eli5_what_does_squares_flow_library_do/)*)*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)

[https://upscri.be/hackernoon/](https://upscri.be/hackernoon/)