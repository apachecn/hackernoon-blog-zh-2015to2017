# 一个工作正常的 javascript 监视器

> 原文：<https://medium.com/hackernoon/a-javascript-watcher-that-works-just-right-38ae7d8f1e94>

![](img/bff01e693ffd8c4faaeb244fcdf14f63.png)

想象一个 javascript 观察者能够正确地完成它。它使用了 *Object.defineProperty* 和 *requestAnimationFrame。*它找到合适的时机来触发对象变化。它干净、高效、性能卓越。

是时候开始分享我在普遍性道路上的进步了。我正在做一个非常有趣的项目，这个项目将完全去掉“再水合”这个词，让初学者最终可以使用和理解通用框架。

但是灵活性伴随着脱钩而来。这就是为什么我未来的框架将由许多微小的解决方案组成。任何开发人员都可以重用它们！

让我们回到过去的双向绑定的好时光，这种绑定具有足够的性能和弹性。

现在，让我们从这里获得一些灵感:

[活生生的例子](https://plnkr.co/edit/fJFUrCs1577aFMlAVhtX?p=preview)

总而言之，一切都是基于 [**async-watch**](https://github.com/wiresjs/async-watch) 。当你传递一个模板字符串时，我们使用速度极快的 [extract-vars](https://github.com/wiresjs/extract-vars) 来提取应该是可观察的变量。下一步是绑定观察器，它将通知 [wires-reactive](https://github.com/wiresjs/wires-reactive) 模板中发生的变化。一旦收到，angular-expressions(这是我目前唯一明智的解决方案，我对其他选择持开放态度)将编译它，并将结果反馈给你！

那么这个图书馆实际上打开了什么门呢？绝对安全可靠的双向捆绑。监视库将负责整个对象层次结构，确保所有的监视器都完好无损。是的，我们不做脏衣服也不做脏检查。

我们也不需要担心时间。不需要在*$范围内。$apply()，Z* [*魔性*](http://blog.thoughtram.io/angular/2016/02/01/zones-in-angular-2.html) 成为多余。变更堆叠在“事务”中。一旦浏览器准备好绘制您的 DOM，就会触发一个回调！

让我们停止修补 javascript 对象，发明极其复杂的解决方案。让我们追求效率和简约！React-js 不是唯一的做事方式，事实上对于很多需求来说都不是正确的方式！

# 我们可以做得更好！

敬请关注更多更新。如果你喜欢上面的任何一个库，请不要忘记启动它们。如果您有想法、建议、问题，请在 Skype 上戳我！

*window . atob(" c 2t5 cgu 6 bmnoyw5 nzwq = ")*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)