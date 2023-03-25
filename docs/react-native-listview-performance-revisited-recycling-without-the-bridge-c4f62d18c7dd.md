# 重新审视 React 本机 ListView 性能—无需桥接即可回收

> 原文：<https://medium.com/hackernoon/react-native-listview-performance-revisited-recycling-without-the-bridge-c4f62d18c7dd>

![](img/4a129ae5f1060119814c86795b65fdd5.png)

airplanes take off against the wind

*ListView 表现在*[*React Native*](https://hackernoon.com/tagged/react-native)*是我最喜欢的开放议题之一。正在取得重大进展，但这个问题似乎没有一个单一的一站式解决方案，可以处理每一个用例。阻碍我们前进的一个因素是 React 本地桥的异步性。有没有可能把它从等式中剔除？*

## 这么多伟大的思想聚集在一个房间里

我在从 2017 年 [ReactConf](http://conf.reactjs.org/) 回来的飞机上。与我们行业的许多巨头一起度过整整 3 天是我经历过的最鼓舞人心的经历之一。我终于可以在我在 Twitter 上关注的所有个人资料上放上真实的面孔了！

我一直对 React Native 中列表视图的挑战很感兴趣。你知道什么..我和这个领域最有影响力的三个人在一起:[*Brent Vatne*](https://twitter.com/notbrent)——Expo 的领导者之一，React 本地社区背后的驱动力。 [*斯潘塞阿伦斯*](https://twitter.com/sahrens2012)——来自[脸书](https://hackernoon.com/tagged/facebook) React 原生核心团队，他们最近发布了 [FlatList](https://facebook.github.io/react-native/blog/2017/03/13/better-list-views.html) 。以及[*Brian Vaughn*](https://twitter.com/brian_d_vaughn)—[背后的人 React Virtualized](https://github.com/bvaughn/react-virtualized) ，这是我见过的针对网络这个问题的最佳实现。

## 那么，还需要做些什么呢？

列表视图有许多用例，不同的用例需要不同的优化。React Native 中的当前实现可以很好地处理具有各种各样单元格的复杂列表，这些单元格可以滚动而不会跳来跳去。将脸书的信息与你手机的联系人列表进行对比。

当一个用户有几百或几千个联系人，并且正在浏览试图找到一个特定的联系人时，他们使用列表的方式完全不同。滚动模式会更加不稳定，跳跃更大，滑动更快。如果我们从另一个世界借用，你可以说用户正试图进行*随机访问*而不是*顺序访问*:)

这也是 React Native 最难的地方。滚动发生在*本地领域*中，但是行的呈现异步发生在 *JavaScript 领域*中。它们之间的数据在网桥上排队。如果你滚动得足够快，渲染请求最终会排队等待，导致滚动中出现空白。

## 我们能帮上什么忙？

许多人认为 Fiber 是有前途的——它能够控制渲染优先级并取消不再需要的未决渲染。这肯定很有趣，但我们将尝试完全不同的东西。

我们将试着把桥从等式中完全去掉。我们以前在处理其他性能问题时也采用过这种方法。使用 [Animated](https://facebook.github.io/react-native/docs/animated.html) 的本地驱动程序，动画最终可以以 60 FPS 的速度运行，从而最大限度地减少桥接流量……我刚刚在 ReactConf 中展示了一种很酷的方法，用[声明式物理库](/@talkol/moving-beyond-animations-to-user-interactions-at-60-fps-in-react-native-b6b1fa0ba525)以 60 FPS 的速度进行用户交互……我们知道声明式 API 可以是一种强大的武器。

如果没有桥，当我们滚动的时候也不会有 JavaScript 的渲染。这意味着我们将不得不完全依赖回收旧行。这是 iOS [UITableView](https://developer.apple.com/reference/uikit/uitableview?language=objc) 等原生 ListViews 中的标准方法。幸运的是，联系人列表场景也非常适合回收——行几乎完全相同。我们可以利用这一点。

你们中的忠实追随者可能还记得，我已经在大约 9 个月前使用过这种方法(参见[为高性能 React 本地列表视图回收行](/@talkol/recycling-rows-for-high-performance-react-native-list-views-628fd0363861#.5c52s0n42))。之前尝试的问题是，我们仍然依赖 React 和 JavaScript 来协调行内容更新。

## 如何在不使用 React 的情况下更新行？

简单。我们只更新行，不做反应。

如果你仔细查看 React 原生文档，你会发现这种能力不久前被记录在“[直接操作](https://facebook.github.io/react-native/docs/direct-manipulation.html)”下。这个 API 从来没有找到自己的位置，几乎已经从世界上消失了。但是通过挖掘旧的 [NativeMethodsMixin](https://github.com/facebook/react/blob/e452e3374135c116ef687a8bb3a5d277e3cde8fb/src/renderers/native/NativeMethodsMixin.js) 代码可以让我们知道如何做到这一点。

## 这个计划

首先，在我们的库中，我们将使用 React 来呈现一个行池，这个行池仅够覆盖一个屏幕折叠。这只会在初始化期间发生，因此不会影响滚动性能。此时使用 React 将为开发人员提供使用 JSX 定义行模板布局的灵活性。

接下来，我们将要求开发人员在 JSX 中定义行模板，并明确声明数据源中的数据将如何绑定到它。这就是声明性 API 发挥作用的地方:

如果你仔细观察上面的例子，你会注意到我们使用 [TextInput](https://facebook.github.io/react-native/docs/textinput.html) 组件来显示字符串，而不是使用[文本](https://facebook.github.io/react-native/docs/text.html)。这背后有一个原因，我们稍后会深入探讨(这将得到解决，不用担心)。

我们的声明式 API 使用 [ref](https://facebook.github.io/react/docs/refs-and-the-dom.html) 来定义模板中的各种字段 id 如何绑定到特定组件的道具中。在这种情况下，我们将字符串模板字段绑定到 TextInput 的“text”属性。

我们要做的下一件事是将整个数据源传递给*本地领域*。这可能听起来很吓人，但实际上对性能的影响很小。即使有 5000 个联系人，数据源中的数据总量也很小。每个联系人都有一些附加条件。网桥具有非常高的吞吐量，因此在初始化期间一次发送它不会产生很大的影响:

接下来，我们将要求用户提供另一个声明。这一次展示了数据源中的字段如何映射到模板中的字段 id。我们将把它作为另一个属性添加给 BindingListView:

正如您所记得的，当我们在上面定义数据源时，数据源中的每一行都有两个字段:“姓名首字母”和“姓名”。

是时候转换到本地了。我们将在 iOS 上工作，并使用 Objective-C。由于我们需要一个可回收的原生 ListView，我们将简单地直接依赖 [UITableView](https://developer.apple.com/reference/uikit/uitableview?language=objc) 。它是原生 iOS SDK 的一部分，提供开箱即用的原生视图回收。

这是用于实现回收的 API:

每当 UITableView 无法回收时(这只会发生在第一次屏幕折叠时)，我们需要为它提供一个新的视图。我们从预先创建的池中获取此视图。

我们如何填充池？这其实是很酷的一招。我们在初始化期间定义的 JSX 行池作为 React 子对象提供给我们的 ListView。这将在 native 中为我们提供一个方便的挂钩点，我们可以将视图“偷”到池中，而不是实际将它们作为子视图添加:

记得以前，当我们不能回收一个视图而必须分配一个视图时，我们已经获取了一个在池中等待的未使用的单元。

如今，现在..主要表演时间到了。我们如何将数据源数据从本机绑定到本机视图？

秘诀是*synchronouslyUpdateViewOnUIThread*——一个用于直接操作的本地 API，它是 React Native [UIManager](https://github.com/facebook/react-native/blob/264d60b97925aa72e51d4f4e0074503235eca12e/React/Modules/RCTUIManager.m) 的一部分。

## 为什么之前我们用 TextInput 而不是 Text？

*的问题在于它只适用于道具。使用 [TextInput](https://facebook.github.io/react-native/docs/textinput.html) ，组件中的文本作为道具而不是子组件提供。这使得它更容易使用。*

对于[文本](https://facebook.github.io/react-native/docs/text.html)组件，原始文本是一个子节点，这使得一切都变得非常复杂。这种情况下的模板声明仍然很简单，与之前非常相似:

请注意，我们在这里使用的专有名称现在是“儿童”。

现在最大的问题是，我们如何支持从本地绑定到原始文本子节点？事实上，我花了很长时间试图解决这个问题。这是我迄今为止找到的最好的解决方案( *dispatch_async* ):

这并不完美，因为我们在进程中依赖于不同的本机线程(UIManager 影子队列)。这仍然比过桥效率高得多，但是，如果我们最终只从主线程做所有的事情，我会很高兴。如果你想出了一个从主线程来做这件事的方法，请告诉我！

## 包装东西

我的航班就要结束了..这是一个很好的消费方式:)

GitHub 上照常提供了一个完整的连接上述所有代码片段的工作示例:

[](https://github.com/wix/BindingListView) [## wix/BindingListView

### 支持直接视图绑定的本地 ListView 实验实现

github.com](https://github.com/wix/BindingListView) 

repo 包含一个包含 5000 个项目的示例联系人列表，您可以在手机上运行该列表。提示:在真实的设备上运行，而不是在模拟器上。我相信你会同意滚动性能是你见过的最好的，同时保持内存消耗恒定和最小。我相信这个例子是我们用 React Native 得到的最接近纯本机滚动性能的例子。

就用户体验而言，它绝对够好了，甚至当你快速滚动或者按下状态栏上的键滚动到顶部时，它也没有任何空白。

以下是回购中主要文件的分类:

*   由使用我们的 BindingListView 库的开发人员编写
*   [BindingListView.js](https://github.com/wix/BindingListView/blob/90c787be46ceaf35c255d1a444a4548af94fcb06/src/BindingListView.js) —库的 JavaScript 实现
*   [rctbindinglistview . m](https://github.com/wix/BindingListView/blob/90c787be46ceaf35c255d1a444a4548af94fcb06/ios/BindingListView/RCTBindingListView.m)—lib 的原生 iOS 实现

我希望这种方法能推动更多这方面的研究。

如果你对 ListView 的性能感兴趣，并且正在寻找一个挑战——试着想想如何增加对可变高度单元格的支持。也许我们可以自己直接驱动 yoga(原生布局引擎)在绑定过程中更新布局。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)