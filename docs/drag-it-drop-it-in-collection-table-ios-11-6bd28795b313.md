# iOS 11 编码:如何拖放到收藏和表格中

> 原文：<https://medium.com/hackernoon/drag-it-drop-it-in-collection-table-ios-11-6bd28795b313>

![](img/d73b6a8f8912698279050b461ac06850.png)

随着 **iOS-11** 的发布，苹果在`UITableView`和`UICollectionView`中引入了**拖放**，以及一个基于**交互**(类似手势)概念的专门 API。

表格视图和集合视图有相似的 API 来支持拖放，只是有一些小的不同。

在 **iPhone** 和 **iPad** 中都支持拖放。在 iPad 上，用户可以在多个应用程序中拖放。但对于 iPhone 来说，这种支持只在一个应用程序中提供。

我们将讨论拖放在集合视图中是如何工作的，这同样适用于表格视图。我会根据需要指出表格视图中的不同之处。所以不用担心。您会发现它既适用于集合视图，也适用于表格视图。

# 我们开始吧🚀

在集合视图中采用拖放是一个多步骤的过程。我们将讨论在集合视图中集成拖放元素所需的每一个细节。

## UICollectionViewDragDelegate

要使**拖动**，自定义对象必须符合`[UICollectionViewDragDelegate](https://developer.apple.com/documentation/uikit/uicollectionviewdragdelegate)` 协议。

它包含多种方法，可以实现这些方法来定制集合视图的拖动行为。

该协议中唯一需要用来支持拖动行为的方法是:

```
[collectionView(_:itemsForBeginning:at:)](https://developer.apple.com/documentation/uikit/uicollectionviewdragdelegate/2897389-collectionview)
```

我们将在接下来的章节中研究该方法的细节。

另外，在`viewDidLoad()`中设置收藏视图的`dragDelegate`委托对象，以管理从中拖动项目。

```
collectionView.dragDelegate = self
```

## UICollectionViewDropDelegate 协议

要启用 **drops** ，自定义对象必须符合`[UICollectionViewDropDelegate](https://developer.apple.com/documentation/uikit/uicollectionviewdropdelegate)` 协议。

本协议唯一需要的**方法**是:

```
[collectionView(_:performDropWith:)](https://developer.apple.com/documentation/uikit/uicollectionviewdropdelegate/2897304-collectionview)
```

您可以根据需要实现其他方法来自定义集合视图的放置行为。

此外，将您的定制委托对象分配给`viewDidLoad()`中集合视图的`dropDelegate`属性。

```
collectionView.dropDelegate = self
```

## 启用拖动交互

要启用/禁用拖动，您可以自定义收藏视图的`dragInteractionEnabled`属性。

该属性的默认值在 iPad 上为 true，在 iPhone 上为 false。

因此，如果您在 iPhone 中提供拖放支持，将它设置为 **true** 以启用从收藏视图拖动内容。

```
collectionView.dragInteractionEnabled = true
```

## 拖动单个项目

既然我们已经配置了集合视图来支持拖放，那么是时候实际编写实现它的代码了。

让我们首先介绍一些支持拖放所需的重要类。我们开始吧:

1.  [nsiteprovider](https://developer.apple.com/documentation/foundation/nsitemprovider)**—**拖拽过程中在进程间传递数据或文件的项目提供者。
2.  [UIDragItem](https://developer.apple.com/documentation/uikit/uidragitem) **—** 底层数据项从一个位置拖到另一个位置的表示。

每一个需要拖动的项目都必须表示为一个`UIDragItem`的对象。

为了允许从集合视图中拖动项目，实现唯一需要的方法`UICollectionViewDragDelegate`，并在指定的`indexPath`返回项目的一个或多个`UIDragItem`对象。

OMG！😯那是什么？太多了，无法理解..！！！

别担心。我们将在下面的代码中完成每个步骤:

让我们看看代码是怎么说的..

1.  第 1 行—实施`UICollectionViewDragDelegate`的`collectionView(_:itemsForBeginning:at:)`方法。
2.  第 3 行——获取与所选项目的`indexPath`相对应的数据。使用作为集合视图数据源的模型。
    这里我使用了一组`String`对象作为集合视图的数据源。因此，项目将对应于一个`String`值。
3.  第 4 行——使用第 3 行提取的`item`创建一个对象`NSItemProvider`。`item`被强制转换为`NSString`，因为`**NSItemProvider**` **接受一个对象**，而 swift `String`是值类型而不是对象类型。
4.  第 5 行—从`itemProvider`创建一个`UIDragItem`对象。
5.  第 6 行— `localObject`是与拖动项相关联的自定义对象。它仅适用于启动拖动活动的应用程序。
    这是**可选的**，但是可以更快地在同一个应用程序中拖放内容。
6.  第 7 行—返回一个`dragItem`的**数组**。

如果你想忽略拖动，返回一个空数组。

## 拖动多个项目

我们已经看到了如何从集合视图中拖动单个项目。如果我想一次拖动多个项目怎么办？

嗯，我们有一个`UICollectionViewDragDelegate`方法来实现这一点。

```
[collectionView(_:itemsForAddingTo:at:point:)](https://developer.apple.com/documentation/uikit/uicollectionviewdragdelegate/2897367-collectionview)
```

此方法将指定的项添加到现有的拖动会话中。

点击**一次**即可将项目添加到活动拖动会话中。如果不实现此方法，在收藏视图中点击会触发项目选择或其他行为。

其实现与`collectionView(_:itemsForBeginning:at:)`类似。

您可以根据需要添加自己的约束。

**示例**:如果项目已经存在于您正在拖放的收藏视图中，则忽略添加项目。在这种情况下，返回一个空数组。

## 删除建议 UICollectionViewDropProposal

我们选择了项目，然后拖动它。现在，我们不能只是把这个项目拖得太久。现在该怎么办？放在哪里？当我放下物品时会发生什么？

所有这些问题都指向一个非常具体的问题:
“你打算如何处理在指定位置的投递？
您是要复制该项目，还是只是将其移动到新位置？
还是想在某些特定条件下禁止运动？哦，不..！！我可以直接取消吗？”

嗯嗯嗯..！！！您的所有问题只有一个答案— **撤销提议。**drop proposal 顾名思义就是当用户举起手指时，你打算如何处理特定位置的 drop 的提议。

[uidroproposal](https://developer.apple.com/documentation/uikit/uidropproposal)—一个拖放交互行为的配置，如果视图接受拖放活动，则需要该配置。

每个提议都定义了一个**操作**—[enum uidropooperation](https://developer.apple.com/documentation/uikit/uidropoperation)，它决定了当用户放下一个拖动项时，拖放活动如何解析。由定义的操作可以是以下类型:

1.  `cancel` —不应传输任何数据，取消拖动。
2.  `forbidden` —虽然在这种情况下移动或复制操作通常是合法的，但是不允许放置活动。
3.  `copy` —拖动项目所代表的数据应复制到目标视图。
4.  `move`-拖动项目所代表的数据应该移动，而不是复制。

[uicollectionviewdroposal](https://developer.apple.com/documentation/uikit/uicollectionviewdropproposal)—uidroproposal 的一个子类，专门用于处理 DropProposal 的集合视图。

集合视图建议还定义了一个可选的**intent**—[enum UICollectionViewDropIntent](https://developer.apple.com/documentation/uikit/uicollectionviewdropintent)，它决定了如何将内容合并到集合视图中。您可以在项目之间插入内容，或将其添加到现有项目中。意向的可能值包括:

1.  `unspecified` —未指定撤销提议。
2.  `insertAtDestinationIndexPath` —在指定的索引路径插入拖放的项目。
3.  `insertIntoDestinationIndexPath` —将拖放的项目合并到指定索引路径的项目中。

集合视图使用`intent`信息向用户提供适当的**视觉反馈**。

现在我们知道了什么是撤销提议，我们现在需要弄清楚的是如何以及在哪里实现它。

`UICollectionViewDropDelegate`提供了一种方法，您可以在其中指定想要使用的删除建议，即:

```
[collectionView(_:dropSessionDidUpdate:withDestinationIndexPath:)](https://developer.apple.com/documentation/uikit/uicollectionviewdropdelegate/2897375-collectionview)
```

当用户拖动内容时，集合视图会重复调用此方法，以确定如果放置发生在指定位置，您将如何处理放置。

因为这个方法在用户拖动集合视图时被重复调用，**您的实现应该尽快返回**。

在上面的代码中，我使用了两个属性:

1.  [localDragSession](https://developer.apple.com/documentation/uikit/uidropsession/2890985-localdragsession) —如果拖动活动在不同的应用程序中启动，则本地拖动会话为`nil`。
2.  [hasActiveDrag](https://developer.apple.com/documentation/uikit/uicollectionview/2897415-hasactivedrag) —一个布尔值，指示项目是否已从集合视图中被提升且尚未被丢弃。

以上代码说明了这一点:

1.  第 16 行—如果拖动活动是在另一个应用程序中开始的，**禁止**拖放。
2.  第 7 行——否则，如果该项是从您正在拖放它的同一个集合视图中被提升的，那么**将它从源索引路径移动(重新排序)**到目标索引路径。
3.  第 11 行——否则，如果您将它放到另一个集合视图中，那么**将该项复制到目标索引路径。**

## 处理丢弃—复制

在明确了我们打算如何处理放置之后，让我们来看看一旦实际放置完成，我们需要做什么的实现细节。

要允许在集合视图中拖放项目，实现唯一需要的方法`UICollectionViewDropDelegate`。

也就是:
`collectionView(_:performDropWith:)` —告诉您的代理将拖放数据合并到集合视图中。

这个方法为您提供了一个[UICollectionViewDropCoordinator](https://developer.apple.com/documentation/uikit/uicollectionviewdropcoordinator)对象，您可以使用它来处理拖放操作。使用`coordinator`，您可以获取以下项目来更新您的收藏视图的数据源:

1.  `items` —被拖动的项目
2.  `destinationIndexPath` —在集合视图中插入项目的索引路径。这是一个**可选的**值。如果项目被插入到空的集合视图或集合视图的末尾，则返回`nil`。
3.  `proposal` —当前关于如何合并已删除项目的建议

此外，当合并项目时，使用`coordinator`对象的`drop(_:to:)`或`drop(_:toItemAt:)`方法来制作从拖动项目的预览到集合视图中相应项目的动画。

现在让我们看一些代码。

上面的代码非常简单:

1.  `destinationIndexPath`取自`coordinator`。在是`nil`的情况下，集合视图的最后一个索引路径被用作放置项目的目的地。
2.  根据撤销建议的`operation` — `move/copy/forbidden/cancel`采取适当的措施来更新集合视图。

如果您的丢弃建议是`**cancel/forbidden**`，将不会调用`collectionView(_:performDropWith:)`来处理丢弃，因此您不需要对其做任何特殊处理。

如果建议的处理方式是`**move**`，我们需要重新排序项目。我们将在下一节讨论更多关于重新排序的内容。

在这一节中，让我们看看如果我们打算`**copy**`这些项目，如何处理 drop。

您可以使用收藏视图的`[performBatchUpdates(_:completion:)](https://developer.apple.com/documentation/uikit/uicollectionview/1618045-performbatchupdates)`在收藏视图中进行更改。

如果希望在一个动画操作中对集合视图进行多次更改，而不是在几个单独的动画中进行更改，则可以使用此方法。

在批处理操作中，删除是在插入之前处理的。这意味着相对于批处理操作之前集合视图状态的索引来处理删除的索引，相对于批处理操作中所有删除之后状态的索引来处理插入的索引。

你可以这样做:

要获取与拖动项目相对应的数据，您可以使用以下选项之一:

1.  `localObject`如果设置了拖拽项的属性，则可以使用。如果内容来自您应用程序中的其他地方，它将可用。
2.  此外，您可以使用拖动项的`itemProvider`属性来获取数据。

## 处理丢弃-重新排序

当 drop proposal 被指定为— **move** 时，将对单元格进行重新排序。

**重新排序** —根据您的要求，在相同/不同的集合视图或表格视图中将项目从源索引路径移动到目标索引路径。

我在这里明确地指的是表视图，因为表视图和集合视图处理重新排序的方式有点不同。我们将在这里讨论这两个问题。

**在表格视图**中，重新排序功能早就可以使用了。好的一面是我们可以继续使用它😅。不需要做任何特殊的事情来支持表视图中的重新排序。你只需要:

1.  返回`move`作为您在`[tableView(_:dropSessionDidUpdate:withDestinationIndexPath:)](https://developer.apple.com/documentation/uikit/uitableviewdropdelegate/2897302-tableview)`中的丢弃建议，以支持重新订购。
2.  实现`UITableViewDataSource`的`[tableView(_:canMoveRowAt:)](https://developer.apple.com/documentation/uikit/uitableviewdatasource/1614927-tableview)`和`[tableView(_:moveRowAt:to:)](https://developer.apple.com/documentation/uikit/uitableviewdatasource/1614867-tableview)`方法。

如果实现了上述`UITableViewDataSource`方法，将不会调用`[tableView(_:performDropWith:)](https://developer.apple.com/documentation/uikit/uitableviewdropdelegate/2897427-tableview)`来处理掉落。

**在收藏视图**中，重新排序的处理方式与复制相同。唯一的区别是——**在复制**时，您在目标索引路径插入了一个全新的项目，但是**在重新排序**时，您需要从源索引路径删除一个项目，并将其插入到目标索引路径。

逻辑稍有改变，其余一切保持不变。

**注意:**从`collectionView(_:performDropWith:)`中的`coordinator`获得的`destinationIndexPath`可能是集合视图中的项目数，如果您尝试将项目重新排序到集合视图的末尾。在重新排序的情况下，集合视图中的项目数量保持不变。所以你需要明确地处理它，因为它可能会抛出`Array index out of bounds`运行时错误。

哦等等！我有`destinationIndexPath`，但是我从哪里得到`sourceIndexPath`？

我们对此也有答案。

使用`coordinator`可以获得与每个项目相对应的`[sourceIndexPath](https://developer.apple.com/documentation/uikit/uicollectionviewdropitem/2897392-sourceindexpath)`。如果该项源自同一个集合视图，则此属性包含该项的原始索引路径。

在上面的代码中，单个项目被重新排序。您可以根据自己的需求对多个项目进行测试。

## 重新排序速度—重新排序节奏

在重新排序的情况下，当您将项目拖动到接受拖放的集合视图上时，集合视图会向用户提供适当的视觉反馈——移动/重新排序单元格以容纳新的单元格。

我们可以在集合视图对象上使用类型[enum uicollectionviewrorderingcadence](https://developer.apple.com/documentation/uikit/uicollectionviewreorderingcadence)的 [reorderingCadence](https://developer.apple.com/documentation/uikit/uicollectionview/2897347-reorderingcadence) 来控制重新排序的速度，以获得更好的用户体验。

`reorderingCadence`可以有这些可能的值:

1.  `immediate` —项目会立即重新排序到位。(**默认值**)
2.  `fast` —商品会很快重新排序，但会有短暂的延迟。
3.  `slow` —项目在延迟后重新排序。

在`viewDidLoad()`中为您的集合视图对象设置适当的`reorderingCadence`值。

```
collectionView.reorderingCadence = .fast
```

## 拖动预览 UIDragPreviewParameters

当某项被提升时，默认情况下，集合视图使用该项的可见边界来创建预览。如果您想要自定义项目的外观，可以通过预览参数来实现。

[UIDragPreviewParameters](https://developer.apple.com/documentation/uikit/uidragpreviewparameters)—一组用于调整拖动项目预览外观的参数。

这些参数指定预览的不同视觉方面，包括背景颜色和与预览相关联的视图的可见区域。

如果您不想对拖动预览进行任何更改，请不要实现该方法，或者如果实现了该方法，请返回`nil` 。

要定制拖动预览，实现`UICollectionViewDropDelegate`的`[collectionView(_:dragPreviewParametersForItemAt:)](https://developer.apple.com/documentation/uikit/uicollectionviewdragdelegate/2897459-collectionview)`方法。

就是这样。这就是在集合和表格视图中使用拖放功能所需的全部内容。继续..试试看！！

# 示例项目

你可以从[这里](https://github.com/pgpt10/DragAndDrop-CollectionView)下载示例项目。

![](img/7cfee7e2813040235f6ca6b0744659de.png)

# 促销

别忘了阅读我的其他文章:

1.  [Swift 4 中关于 Codable 的一切](https://hackernoon.com/everything-about-codable-in-swift-4-97d0e18a2999)
2.  [你一直想知道的关于 iOS 通知的一切](https://medium.freecodecamp.org/ios-10-notifications-inshorts-all-in-one-ad727e03983a)
3.  [用渐变给它上色——iOS](https://hackernoon.com/color-it-with-gradients-ios-a4b374c3c79f)
4.  [关于 iOS 10 中的今日扩展(Widget)你需要知道的一切](https://hackernoon.com/app-extensions-and-today-extensions-widget-in-ios-10-e2d9fd9957a8)
5.  [UICollectionViewCell 选择变得简单..！！](https://hackernoon.com/uicollectionviewcell-selection-made-easy-41dae148379d)

如果你有任何疑问，请随时发表评论。