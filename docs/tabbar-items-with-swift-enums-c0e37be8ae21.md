# 带有 Swift 枚举的 TabBar 项目

> 原文：<https://medium.com/hackernoon/tabbar-items-with-swift-enums-c0e37be8ae21>

## 管理子视图控制器的快速技巧

![](img/f08c707dc27454b752baa0d2e0378192.png)

在 iOS 中，标签式导航非常方便有趣。由于本机 UITabBarController，UIViewController 的一个子类，除了它的子 ViewControllers 之外，它实际上不需要太多设置。

但是，不可否认的是，在那之后，我们仍然发现自己在管理子视图控制器方面处于两难境地，特别是当我们需要从 TabBarController 访问它们的时候。有几种不同的方法来完成它，这取决于几个更多的因素，如任务的重量或意图，其中一些是:

1.  儿童视图控制器的位置应该很容易改变/切换，没有太多问题，但主观设计。
2.  除了需要沟通的情况外，父 TabBarControllers 不需要了解子 ViewControllers 太多信息。
3.  父 TabBarControllers 应该能够容易地访问并准确地区分子 ViewControllers。

跟踪子 ViewControllers 的一个简单方法是通过它们的**字符串** `tabBarItem.title`属性。如果名字比较是你所需要的，也许这就够了？

但还是要小心。字符串很容易实现和修改，但是如果你不严格注意的话，也是非常松散的。使用**字符串**作为比较点很容易在你不知道的情况下导致许多错误的标志，所以我现在会远离它。

另一种方式可以通过**数据类型**来实现。类似于标题比较，您可能会以大量的`if else`结束，正因为如此，它只适用于没有两个子视图控制器共享相同类型的情况。

与字符串比较不同，这可以避免您意外地误解错误的值。但是如果您走这条路，测试父 UITabBarController 将会非常棘手，因为依赖严格的数据类型来识别对象将会限制您的模拟能力。

## 通过协议和枚举描述我们的选项卡项目

我想与大家分享的另一种管理子视图控制器的方法是通过 Swift Enum 和协议实现的混合。通过为您的子 ViewControllers 创建一组有限的 TabBar 标识值，您还可以为它们提供一个模拟或测试类的良好基础，从而提高它们的可测试性。

首先，我们将通过一组表示我们期望的 TabBar 项目的枚举值来实现我们的有限的名为`TabBarType`的标识集。

```
enum TabBarType: String { case featured = "Featured" case accounts = "Accounts" case cart = "Cart" case search = "Search" case none = "None"}
```

这样我们可以锁定我们可能的值，以确保我们可以在编译时尽早捕捉到任何流氓。

其次，我们将创建一个名为`TabBarChild`的简短协议来描述一个包含代表它们的`TabBarType`值的对象。

```
protocol TabBarChild { var tabBarType: TabBarType? { get set }}
```

第三，我们将使我们指定的子 ViewController 类符合新的协议，这样他们的父母就知道如何作为 TabBar 的孩子与他们交流。这可以通过许多不同的方式来实现，取决于你的团队偏好的[设计](https://hackernoon.com/tagged/design)，但是在本文中我可以向你展示几个。

## 解决方案 1:扩展 UIViewController

更简单的方法是通过扩展 UIViewController 类来符合`TabBarChild`协议。你基本上只是假设每个 UIViewController 都有`TabBarChild`能力，缺点是必须设计 ViewController 来存储/计算`TabBarType`的值，不太符合常规。

```
extension UIViewController: TabBarChild { var tabBarType: TabBarType { get { return TabBarType(rawValue: tabBarItem.title ?? "" ) ?? .none } set { tabBarItem.title = newValue.rawValue } }}
```

在本例中，我选择将其值存储到 ViewController 的`tabBarItem.title`中。这样，您可以通过简单地更改`TabBarType`的值来轻松地修改 ViewController 的显示方式。这是一种方便的方法，因为它还允许您分配/删除`TabBarType`属性。下面是实施过程中的一个示例:

```
//somewhere in you code
let viewController = SearchViewController()
viewController.tabBarType = .search
tabBarController.viewControllers.append(viewController)func tabBarController(_ tabBarController: UITabBarController, shouldSelect viewController: UIViewController) -> Bool { if viewController.tabBarType == .search {
        //do something with the search tab here? } return true}
```

## 解决方案 2:自定义 UIViewController 扩展

不喜欢上面的解决方案？另一种方法是更进一步，删除`TabBarChild`的`set`访问，[给](https://hackernoon.com/tagged/leaving)留下一个不可变的`TabBarType`属性，这样它们的实现看起来就像这样:

```
protocol TabBarChild { var tabBarType: TabBarType { get }}extension AccountsViewController: TabBarChild { var tabBarType: TabBarType { return .accounts }}
```

这让您可以更安全地将`TabBarType`属性分配给子 ViewControllers。在您的 TabBar 管理层上，访问它们可能如下所示:

```
//sample UITabBarDelegate calls
extension HomeFlowController: UITabBarControllerDelegate { func tabBarController(_ tabBarController: UITabBarController,     shouldSelect viewController: UIViewController) -> Bool { if let tabBarChild = viewController as? TabBarChild, tabBarChild.tabBarType == .accounts { //do something with accounts tab here? } return true }}
```

由于它不知道您的子 ViewController 类型，您可以轻松地防止 ViewController 实现中的更改直接影响您的选项卡管理逻辑。

## 下一步是什么？

我在这里提出的解决方案肯定可以进一步扩展，所以尝试并解决其他可能性。看看哪一个最适合你和你的团队。给`TabBarChild`协议增加更多的属性/功能会对你有更好的帮助吗？或者一个更简单的版本能做得更好吗？

如果你有自己的解决方案，请在这里分享，我非常有兴趣听听他们的意见！

# 你大老远跑来了！

> 我希望当你打开这篇文章的时候，你已经得到了你想要的东西。如果是这样，如果你能把这个推荐给你的朋友^^，我将非常感激

如果您还有任何问题/反馈，请随时留言！