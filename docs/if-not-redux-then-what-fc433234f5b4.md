# 如果不是 Redux 那是什么？

> 原文：<https://medium.com/hackernoon/if-not-redux-then-what-fc433234f5b4>

Redux 的创始人丹·阿布拉莫夫(Dan abra mov)最近发表了一篇文章《 [**你可能不需要 Redux**](/@dan_abramov/you-might-not-need-redux-be46360cf367#.zehy2d7a9) 》，他在文章中指出，开发者在构建 React 应用程序时选择 Redux 作为事实，甚至没有考虑他们是否真的需要它。

Redux 不应该受到指责。像任何其他库一样，它提供了一些折衷，比如通过接触三个文件来完成简单的任务，将状态和动作描述为简单的对象和数组，以及通过纯函数来处理逻辑。这些约束使得调试更容易，测试性更好，保证用户界面的状态是可预测的，以及更多现成的东西。

> 但是，写这么多做这么少带来了一些烦恼，并减缓了整体发展。

# 我们真的需要一个状态管理库吗？

难道我们不能将普通的 ES6 类定义为模型，并将它们的实例存储在一个数组集合中，而不使用库吗？是的，我们可以！但是，问题不在于数据存储或检索。

真正的问题是我们如何检测数据突变并处理它的副作用。数据突变的副作用包括以下行为

*   将用户界面与数据变化同步
*   重新计算已计算的变量
*   导航到不同的路线

如果我们使用赋值操作符(如 user.name = "John Doe ")改变数据，我们就没有机制来检测这种变化( [Object.observe()](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/observe) 已被否决)。

这正是我们需要状态管理库的地方。

> Redux 通过称为 reducers 的纯函数强制数据突变来检测这种变化。

# 其他框架如何解决？

*   **脏检查:** [Angular 1.x](https://angularjs.org/) 使用了[脏检查机制](https://docs.angularjs.org/guide/scope)，在连续的周期中对新旧数据树进行深度比较。一开始这很诱人，但是会在大规模中扼杀性能。
*   **反应式编程:** [Angular 2](https://angular.io/) 和 [Meteor](https://www.meteor.com/) 使用反应式编程的概念，使得数据传播和变化检测易于理解和扩展。
*   **观察者模式:**与反应式编程概念类似，观察者模式在数据突变时触发事件。 [Knockout.js](http://knockoutjs.com/) 和 [MobX](https://mobxjs.github.io/mobx/) 使用观察者模式。
*   **Setter 和 Getter:** 古老的 Setter 和 Getter 方法解决了多年来被[成员](http://emberjs.com/)和[骨干](http://backbonejs.org/)使用的变更检测问题。

# Redux 的替代品有哪些？

## this.setState()

React 自带组件级的变更检测方法。如果您是 React 新手，就坚持使用 this.setState()方法。它适用于小型应用程序。例如:[简单的待办事项应用](https://gist.github.com/caike/736d45e44fa5c7595adb)

## 使用 React 的反应模式和观察者模式

*   [**MobX**](https://mobxjs.github.io/mobx/) **(又名 mobservable** ) **:** 它的占地面积非常小。如果你习惯使用面向对象的模式，这一个是给你的。使用 ES6/7 装饰器，你几乎感觉不到你在使用任何库。
*   [**RxJS**](https://github.com/Reactive-Extensions/RxJS)**:**“JavaScript 的反应式扩展”。RxJS 本身就是野兽。简单来说，RxJS =可观测量+算子+调度器。尽管它作为一个独立的库而闻名，但它为 React 提供了一些简洁的[绑定。](https://github.com/fdecampredon/rx-react)

## 其他 Flux 实现

*   [**Alt**](http://alt.js.org/)**:**同构的 flux 实现哪个干净易懂。您需要像其他 flux 实现一样将监听器连接到组件。
*   [**回流**](https://github.com/reflux/refluxjs)**:**Flux 架构的早期实现之一，有一定的局限性，大部分使用回流的开发者已经[迁移到 Redux 或者其他库](https://www.quora.com/Is-it-wise-to-invest-into-RefluxJS-with-Facebooks-React)。
*   [**中继**](https://facebook.github.io/relay/)T4:脸书搭建的官方框架，在后端使用 React 和 GraphQL 协议。 [Relay 的 Container components 与使用 connect()方法创建的 Redux](https://github.com/reactjs/redux/issues/464) 的 Container components 直接比较。

# 我的个人选择: [MobX](https://mobxjs.github.io/mobx/)

*【2017 年 10 月 24 日更新】我已经开始使用* [*mobx 状态树*](https://github.com/mobxjs/mobx-state-tree) *，这是一个状态容器，使用 mobx。*

## 为什么是 MobX？

*   MobX 最大的优点是**省去了为数据更改添加监听程序的麻烦**，它透明地将反应式编程概念(TFRP)应用到数据模型中，并在需要时更新 UI 的一部分。
*   **面向对象概念为赢**:你可以使用好的旧的面向对象概念，并应用到你的应用程序中的数据模型。
*   **易于文档化和交流**作为技术负责人，MobX 让我的工作变得非常易于文档化所有类和存储，以及需要构建的属性和方法。
*   **快速开发:**相比其他库，你可以专注于应用程序的业务逻辑并以更快的速度交付产品。
*   **有反应时增加生命周期挂钩:**mobx-reaction 增加名为**component will reaction()的生命周期挂钩**进行反应性更新。此挂钩可用于路由器和导航器。

## 权衡？

如果你选择一个库而不是另一个库，你会得到一些东西，也会失去一些东西。以下是 MobX 提供的一些权衡:

*   由于我们使用类和派生对象而不是普通的 JavaScript 对象，因此数据不能立即序列化。您需要为每个类编写自己的 toJS()和 fromJS()方法
*   每次当数据离开执行环境时，都需要对数据进行序列化，例如在进行 API 调用、将其存储在 LocalStorage 中、从其他数据源填充后端中的存储等时。
*   与 Redux 不同，撤销/重做和数据更改的时间旅行等功能需要手动构建。

# 底线

您必须使用 React 的特定状态管理库，这并不是一成不变的。这是你和你的团队对范式的个人选择。社区有时会误导人。所以，在你包含一个库之前，要再三考虑库提供的约束和权衡。

我错过什么了吗？如有任何更正、意见和讨论，请随时回复文章。

如果你喜欢这篇文章，考虑推荐它。我是 [Sanket](http://geekyants.com/sanket) ，我们正在 [GeekyAnts](http://GeekyAnts.com) 建立 [NativeBase.io](http://NativeBase.io) 。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！