# 精灵行动指南

> 原文：<https://medium.com/hackernoon/a-guide-to-spritekit-actions-c20b079f5398>

![](img/5701e7710f1ee017158c79334ee676be.png)

继续我最近关于 [SpriteKit](https://hackernoon.com/tagged/spritekit) 的帖子，我想我会经历[框架](https://hackernoon.com/tagged/framework)的另一个有用的方面:SpriteKit 动作。由`SKAction`类表示的 SpriteKit 动作允许你在一个`SKNode`对象上制作某些属性的动画，比如位置和速度。

既然有许多类型的 SpriteKit 动作([参见](https://developer.apple.com/reference/spritekit/skaction) `[SKAction](https://developer.apple.com/reference/spritekit/skaction)` [参考](https://developer.apple.com/reference/spritekit/skaction))，让我们采取一个更实际的路线来使用节点上的动作，而不是经历不同类型的动作。

## 创建操作

与许多不同的 SpriteKit 对象不同，你不用初始化器来创建一个`SKAction`。相反，您可以调用与您想要执行的操作相对应的类方法之一。

在这个例子中，`moveBy`方法创建了一个动作，在 800 毫秒的时间内将一个节点向右移动 10 个像素，向下移动 15 个像素。其他动作方式有`rotate`、`scale`、`fadeOut`、`applyForce`。

## 运行操作

要在一个节点上运行这个操作，在您想要应用操作的节点上调用`run(SKAction)`方法。

基于前面的例子，这将通过添加传递给`moveBy`调用的值在 800 毫秒内更新玩家的位置。`run`方法的一个变体接受在动作完成后调用的完成回调。

关于`SKActions`的一个有用之处是它们是“写时复制”，这意味着无论何时动作被改变，值都被复制，因此同一个`SKAction`可以在多个节点上使用。

## 组和序列

有时，需要同时或按顺序运行多个操作。为了实现这一点，方法`SKAction.group([SKAction])`和`SKAction.sequence([SKAction])`分别创建动作的组和序列。例如，我们需要同时移动和旋转我们的玩家节点。

在上面的例子中，我们可以将`group`调用改为`sequence`，这将首先运行`moveAction`，等到它完成，然后运行`rotateAction`。

## 重复

多次运行一个动作同样简单:调用`SKAction.repeat(SKAction, count: Int)`传递要重复的动作和应该运行的次数。这与任何其他操作一样使用。

现在我们的玩家节点上下移动五次。要永远重复一个动作，可以用要无限重复的动作调用`SKAction.repeatForever(SKAction)`。

## 时机

几乎每种类型的动作都有一个持续时间参数，它是一个以秒为单位的`TimeInterval`(一个`Double`的类型别名)。这告诉引擎运行该操作一定的秒数(或几分之一秒)。还有另一种类型的动作，`wait(forDuration: TimeInterval)`，它会等待动作的持续时间。这在动作序列中很有用，可以在动作之间留出时间。

## 块和自定义操作

还有另一个执行代码块的类方法`run(_: () -> Void)`。`run`的参数是一个不带任何参数并返回`Void`的函数。这允许你做常规`SKAction`范围之外的事情，比如更新 UI 或者改变分数。

自定义操作稍微有点复杂。方法`customAction(withDuration: TimeInterval, actionBlock: @escaping (SKNode, CGFloat) -> Void)`创建一个在指定持续时间内运行的动作，同时不断调用`actionBlock`的节点(`SKNode`)和运行时间(`CGFloat`)直到持续时间。

这段代码片段创建了一个动作，将标签节点的文本从`0s`向上计数到`5s`。

## 停止操作

有两种基本方法可以在动作运行时停止它们:在节点上调用`removeAllActions()`或`removeAction(forKey: String)`。前者将停止当前在节点上运行的所有动作，而后者将在调用`run(SKAction, withKey: String)`时，根据赋予动作的唯一键删除动作。

在我最近的 SpriteKit 游戏[蓝移](https://itunes.apple.com/us/app/blueshift-tiles/id1205081032?mt=8)中，我大量使用了`SKActions`来移动瓷砖。去 App Store 看看吧！此外，看看我的其他 SpriteKit 帖子，[基础](https://hackernoon.com/swift-spritekit-basics-94b1798ab639#.l6s6k2pha)和[物理](/@jjacobson/spritekit-physics-14331398b308#.ut1673lie)。如果您有任何问题，请在此评论或在 Twitter 上发推文或 PM 我。快乐的精灵！

[](https://itunes.apple.com/us/app/blueshift-tiles/id1205081032?mt=8) [## App Store 上的蓝移磁贴

### 阅读评论，比较客户评级，查看截图，并了解更多关于 Blueshift 瓷砖。下载蓝移磁贴…

itunes.apple.com](https://itunes.apple.com/us/app/blueshift-tiles/id1205081032?mt=8) [![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)