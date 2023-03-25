# 为什么使用嵌套连接(react-redux)组件是好的？

> 原文：<https://medium.com/hackernoon/why-using-nested-connect-react-redux-components-is-good-bd17997b53d2>

我们都知道用 [Redux](https://github.com/reactjs/redux) 管理状态有多简单。它在应用程序的根位置保留了一个真实的来源(状态树或存储),只能通过调度操作来更改，您可以订阅这些更改以反映 [UI](https://hackernoon.com/tagged/ui) 中的任何更改。

我主要使用 [Redux](https://hackernoon.com/tagged/redux) with [React](https://github.com/facebook/react) 当父对象的状态或道具发生变化时，父对象及其子对象会重新渲染，除非我们提供一些解决方案来阻止那些不必要的重新渲染。

现在，如果我在父级订阅商店中的更改，它将使整个应用程序重新呈现，即使实际上只有一个子组件需要重新呈现。

所以图中的 [**react-redux**](https://github.com/reactjs/react-redux) 库来了。它帮助我们使用**连接**方法以有效的方式连接 react 和 redux。

Connect 方法允许您将组件连接到 redux store，并提取组件所需的属性。

这里有一个如何使用 connect 的示例。

How you use connect method from react-redux for connecting your components to redux store.

> 从现在开始，每当我使用“连接”这个词时，我指的是连接到 redux store。

在使用这个库时，我遇到了一种情况，我想连接一个已经连接的组件的子组件。我就想“*这是个好主意吗？*“，”*那性能和那些额外的重新渲染呢，如果有的话？*”。

因此，在搜索了 github repo 上的 redux 和 react-redux 库的问题并进行大量谷歌搜索后，我得出了一个结论:在一个已经连接的组件中使用连接的组件完全没问题。此外，它实际上提高了你的应用程序的性能。

连接已连接组件的子组件有几个优点。

1.  假设您有一个包含 100 个子组件的连接组件。现在，父组件必须为这 100 个组件提供道具，并且必须使用 **mapStateToProps** 函数从商店中提取并返回这些道具。现在你的应用程序要高效，你的 mapStateToProps 函数应该高效并尽快返回，为这 100 个孩子提取道具可能是一项巨大的任务。现在，为了帮助家长，你可以连接一个孩子，让它提取自己的道具和道具的孩子。这样，你就减轻了父母的负担，并把其中的一部分留给了孩子。
2.  你可能会遇到这样的情况，你不想让父母知道孩子将收到的道具。您可以通过连接子组件并使用 mapStateToProps 提取它自己的属性来实现这一点。
3.  Connect 方法实际上经过了大量优化，它检查不同的条件，并决定父级是否应该更新和重新呈现。这样可以避免不必要的重新渲染，否则会严重影响性能。

connecting to redux store

> 我目前正在用 React 开发一些很酷的东西。你可以在推特上找到我。

> 如果你喜欢这篇文章，请点击拍手图标。谢了。