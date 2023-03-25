# Swift 中的反应式编程和绑定

> 原文：<https://medium.com/hackernoon/bindings-and-reactive-data-sources-in-swift-ee1208f882c9>

## 满足粘合和反应套件

![](img/ae4080274ff261fc18299fc675c7516f.png)

移动应用程序的消费者在浏览应用程序时提高了他们的期望值。现在，每个人都希望看到平滑的过渡，像素完美的应用程序，以及更少的步骤。即使一个小小的视觉干扰也会让用户删除你的应用。换句话说，**你的应用必须是完美的**。

## 您如何处理数据变更？

还有另一件重要的事情，那就是**你如何处理 UI** 的数据变化。我遇到过很多对数据变化处理不好的应用程序。例如，您正在拉动以刷新表格视图内容，突然整个内容消失，并在 2-3 秒后重新出现。在大多数情况下，您将获得与“拉至刷新”之前相同的结果，因此实际上没有什么变化。听起来很熟悉？

让我们避免这种情况，并通过使用**绑定和反应式数据源**来正确处理数据更改。👇

## 反应式编程

对于那些不熟悉这个术语的人来说，反应式[编程](https://hackernoon.com/tagged/programming)是一种异步编程范例，涉及[数据流](https://en.wikipedia.org/wiki/Dataflow_programming)和变化的传播。例如，对模型的所有更改都将自动反映在关联的视图中。📡
*如果您有兴趣了解更多关于无功编程的信息* [*请点击此处*](https://en.wikipedia.org/wiki/Reactive_programming) *。*

# 用粘合剂粘合

[**Bond**](https://github.com/ReactiveKit/Bond) 是一个 [Swift](https://hackernoon.com/tagged/swift) 绑定框架，将绑定概念提升到了一个全新的水平。它简单、强大、类型安全和多范例——就像 Swift 一样。它通过使用绑定和反应式数据源简化了状态更改。

这个图书馆一直是我的最爱。您可以将它附加到任何您喜欢的组件上。我将用几个例子向你展示如何使用 Bond，这样你就可以知道它是如何工作的。

# 装置

对于安装，我使用[椰子](https://cocoapods.org/)。我强烈建议你也这样做。只需在您的 Podfile 中添加以下代码并运行`pod install`。👇

```
target 'YOUR_TARGET' douse_frameworks!pod 'Bond', '~> 6.4.3'end
```

从我写这篇文章的那天起，我就在使用最新版本的框架。版本变更，请在这里 *勾选* [*。*](https://github.com/ReactiveKit/Bond/releases)

将`import Bond`添加到你的类的顶部，这样你就可以使用所有的绑定方法。

[](https://theappspace.com/google-maps-sdk-ios-using-swift-4/) [## 使用 Swift 4 的 iOS 版谷歌地图 SDK 应用空间

### 许多 iOS 应用程序使用谷歌地图。这是一个非常普遍的特征，所以我决定在谷歌上准备一个终极指南…

theappspace.com](https://theappspace.com/google-maps-sdk-ios-using-swift-4/) 

# 事件

我将首先向您展示如何摆脱烦人的`IBAction`过程，转而使用来自 Bond 的简化方法。

```
_ = yourButton.reactive.tap.observeNext { print("Button tapped.") }
```

这将观察你的按钮点击。它监听`touchUpInside`，因为这是最常用的事件。如果您想处理其他事件，可以使用:

```
yourButton.reactive.controlEvents(.touchUpInside).observeNext { e in
    print("Button tapped.")
}
```

另一个例子是观察`UITextField`中的文本变化。知道没有本地的方法来处理这种情况，Bond 为我们提供了一个简单的解决方案。

```
_ = yourTextField.reactive.text.observeNext { text in
    print(text!)
}
```

现在，让我向您展示一个简单的将输入的文本绑定到`UILabel`的过程。文本将通过使用`.map`闭包进行转换，并传播到标签。`$0`是类型化的字符串，所以您可以添加任何您需要的字符串转换。

```
yourTextField.reactive.text.map{ $0?.capitalized }.bind(to: yourLabel)
```

或者，您可以使用上面的示例，但提供一个条件。假设当有人在文本字段中键入内容时，您需要更改按钮的启用状态。

```
txtField.reactive.text.map { $0!.characters.count != 0 }.bind(to: yourButton.reactive.isEnabled)
```

# 可观察量

可观察值是信号和反应套件`Property`类型的别名。无论何时`Observable`的状态发生变化，观察者都会得到通知。

```
let animal = Observable("Dog")animal.observeNext { value in
  print("Hi \(value)!")
}
```

从例子中可以看出，每当动物的可观察值改变时，就会触发一个观察器。要更改该值，只需调用`animal.value="Cat"`。要将一个可观察对象与一个 UI 组件绑定，只需使用`animal.bind(to: yourLabel)`。

Bond 还通过使用`bidirectionalBind`支持双向绑定。这意味着如果视图值改变，它将更新可观察值，反之亦然。

```
animal.bidirectionalBind(to: animalTextField.reactive.text)
```

[](https://theappspace.com/instagram-api-authentication-using-swift/) [## 使用 Swift 的 Instagram API 认证-应用空间

### 本教程是关于使用 Swift 的 Instagram API 认证，将教你如何检索 ACCESS_TOKEN…

theappspace.com](https://theappspace.com/instagram-api-authentication-using-swift/) 

# 反应式数据源

通过使用反应式数据源，我们可以轻松地观察表或集合视图中的变化。这意味着无论何时数组发生变化，数据源方法都会自动更新。不需要在每次数组更改时添加`reloadData()`，它将只更新已做的更改。🤘🤘🤘

我们需要使用`MutableObservableArray`或`ObservableArray`来观察变化，并将变化自动传递给数据源方法。

```
let array = MutableObservableArray([“John”,”Michael”,”Steven”])
```

我现在将向您展示实现一个表视图的数据源是多么容易和简单。您不需要将 UITableViewDataSource 与表视图连接，因为 Bond 会为我们处理这些。

强制`numberOfRowsInSection()`和`cellForRowAtIndexPath()`，都是在`bind()`阵法下“打包”的。在闭包中，您可以添加自己的单元格逻辑。请参见下面的示例。👇

```
array.bind(to: tableView, animated: true) { dataSource, indexPath, tableView inlet cell = UITableViewCell(style: .subtitle, reuseIdentifier: “cell”)cell.textLabel?.text = dataSource[indexPath.row]return cell}
```

现在，假设数组需要更新。在这一点上，我们需要**只关注数组**，忘记表视图以及它是否会更新。

下面是一些可以用来对数组进行更改的方法。

```
array.append("Brad") /* appends array with a new object */array.insert("Brad", at: 2) /* inserts new object at the given index */array.replace(with: [“John”,"Brad",”Michael”,”Steven”], performDiff: true) /* replaces the array but updates only rows that weren't present in the previous array (in our case index 1) */array.moveItem(from: 1, to: 2) /* items in array will change places */array.remove(at: 2) /* removes an item from the array */array[1] = "David" /* replaces the value at the given index */
```

通过正确使用这些方法，我们可以提供最佳的用户体验。数据将被更新，用户不会以任何方式分心。

## 我只介绍了 Bond 提供的众多特性中的几个。绑定和反应范式可以让你的应用更上一层楼，所以我强烈建议使用这种方法。如果你想传播信息，请不要忘记👏或者分享一下这个故事。

## 感谢您的关注！🚀

[![](img/79c8aca42472a7e427ec524f7bc604fb.png)](http://bit.ly/2KkkwGL)

## 查看我的最新项目:

[](https://apps.apple.com/app/football-score-prediction-bets/id1517623538) [## 足球比分预测赌注

### 我们提供专业的足球比分预测投注提示，胜率高。每个比赛模拟包含一个…

apps.apple.com](https://apps.apple.com/app/football-score-prediction-bets/id1517623538) [](https://play.google.com/store/apps/details?id=betting.tips.goals) [## 足球博彩技巧和比分——赌进球 Google Play 上的应用程序

### 我们提供高胜率的专业足球技巧。每个比赛模拟包含一个预测，代表…

play.google.com](https://play.google.com/store/apps/details?id=betting.tips.goals) [](https://apps.apple.com/app/zoom-profile-picture-stories/id1514925024) [## 缩放个人资料图片和故事

### ZoomPic for Instagram 是匿名查看大型高清个人照片和故事的最简单、最方便的方式…

apps.apple.com](https://apps.apple.com/app/zoom-profile-picture-stories/id1514925024) [](https://apps.apple.com/app/long-video-story-for-instagram/id1511499427) [## Instagram 的长视频故事

### Instagram 的 Long Story 是一个非常棒的工具，允许您录制或选择任意长度的视频并进行转换…

apps.apple.com](https://apps.apple.com/app/long-video-story-for-instagram/id1511499427) 

## 阅读更多我在媒体上的作品:

[](https://hackernoon.com/introducing-clean-swift-architecture-vip-770a639ad7bf) [## 引入干净的 Swift 架构(VIP)

### 忘了 MVC 吧，现在！

hackernoon.com](https://hackernoon.com/introducing-clean-swift-architecture-vip-770a639ad7bf) [](https://medium.freecodecamp.org/how-you-can-use-the-google-maps-sdk-with-ios-using-swift-4-a9bba26d9c4d) [## 使用 Swift 4 在 iOS 上使用 Google Maps SDK 的终极指南

### 许多 iOS 应用程序使用谷歌地图。这是一个非常普遍的特点，所以我决定准备一个终极指南…

medium.freecodecamp.org](https://medium.freecodecamp.org/how-you-can-use-the-google-maps-sdk-with-ios-using-swift-4-a9bba26d9c4d) [](/theappspace/swift-custom-uiview-with-xib-file-211bb8bbd6eb) [## SWIFT —带有 XIB 文件的自定义 UIView

### 用 XIB 文件定制 UIView 是 iOS 开发中非常常见的做法。自定义 UIView 类不包含 XIB 文件…

medium.com](/theappspace/swift-custom-uiview-with-xib-file-211bb8bbd6eb) [](https://hackernoon.com/how-to-add-spotlight-support-to-your-ios-app-4a89054aff89) [## 如何将 Spotlight 支持添加到您的 iOS 应用程序

### Swift 教程将使您的应用程序在 Spotlight search 中可用

hackernoon.com](https://hackernoon.com/how-to-add-spotlight-support-to-your-ios-app-4a89054aff89) [](https://hackernoon.com/core-data-relationships-d813ed66ba8c) [## 核心数据关系

### 理解一对一和一对多关系

hackernoon.com](https://hackernoon.com/core-data-relationships-d813ed66ba8c) [](https://hackernoon.com/understanding-auto-layout-in-xcode-9-2719710f0706) [## 了解 Xcode 9 中的自动布局

### 所有你需要知道的关于自动布局

hackernoon.com](https://hackernoon.com/understanding-auto-layout-in-xcode-9-2719710f0706) 

## 订阅我的时事通讯: