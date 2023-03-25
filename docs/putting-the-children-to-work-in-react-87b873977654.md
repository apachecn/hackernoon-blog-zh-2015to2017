# 让孩子们在反应中工作

> 原文：<https://medium.com/hackernoon/putting-the-children-to-work-in-react-87b873977654>

充分利用 React 组件中的子组件。

# 什么是反应型儿童？

根据 [React](https://hackernoon.com/tagged/react) 文档，Children (props.children)是一个“不透明”的数据结构。意思就是不要直接惹他们。幸运的是，React 提供了一些工具，可以更容易地与儿童一起工作。

React 儿童的基本[结构](https://hackernoon.com/tagged/structure)，看起来是这样的:

Basic React Child Structure

React 子对象有几个您可能想要使用的关键属性:

*   **$ $ type of**:React 用来验证子元素是否为有效元素。
*   **键**:由渲染组件设置。如果你渲染多个元素，你可能会使用这个。如果你不是，你应该。
*   **道具:**这是传给孩子的道具
*   **ref:** 分配给子节点的 ref 函数。这也可以是一个字符串，但是你应该把它变成一个函数。
*   **类型:**这可以是用于创建组件或字符串的函数。

**问**:是不是所有的孩子都长这样？

**答**:不可以，孩子可以是字符串、布尔值、数字、null 等。您可以使用 React.isValidElement(object)来验证它是否是有效的 react 元素。这将检查传递给它的变量是一个对象，并且它具有$$typeof 的正确符号。

# 儿童实用程序

React 提供了一些用于儿童的工具。你可以在这里阅读更多关于他们的内容: [React 儿童文档](https://facebook.github.io/react/docs/react-api.html#react.children)。

## 做出反应。儿童地图

首先是地图工具。这与 array . protype . map 非常相似，但有一个关键的区别。如果 children 为 null 或未定义，它将返回 null 而不是数组。这很有用，因为 react 不会渲染 and 数组或 undefined。如果你想给孩子们传递额外的道具，这是非常有用的:

## 做出反应。Children.forEach

forEach 实用工具的行为与 map 实用工具相同，但它没有返回值，类似于 Array.prototype.forEach。这可用于筛选出子组件或查找组件需要处理的特定子组件。

## 做出反应。儿童.计数

这个是不言自明的。它返回子节点的数量。

## 做出反应。仅限儿童

唯一的实用程序将返回 children 中的唯一子对象，如果有多个，将抛出。这对于限制可以传递给组件的直接子级的数量非常有用。

## 做出反应。儿童. toArray

另一个不言自明的实用程序。toArray 将 children 对象转换为数组。它还为每个子元素分配一个键，将它们限定在输入数组的范围内。如果需要重新排序或分割子对象，这很有用。

# 实践中的儿童事业

关于 Vue vs React 的文章中提到的一件事是 React 中缺少“插槽”。虽然这可以很容易地通过将“插槽”作为道具传递下来来完成，但也可以使用 React 的子实用程序来实现具有类似于 shadow DOM 或 Vue 的 API 的插槽。

注意:使用 props 可能更有效，因为我们将需要遍历子组件，任何传递了大量根级子组件的组件都可能导致性能下降。

上面的例子是如何使用子工具的一个简单例子。对于一个更深入的例子，你可以检查[反应槽](https://github.com/dmiller9911/react-slotted)。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！