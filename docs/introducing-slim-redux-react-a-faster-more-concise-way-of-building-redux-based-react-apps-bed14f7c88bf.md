# 介绍 slim-redux-react——一种构建基于 redux 的 react 应用程序的更快、更简洁的方法

> 原文：<https://medium.com/hackernoon/introducing-slim-redux-react-a-faster-more-concise-way-of-building-redux-based-react-apps-bed14f7c88bf>

## 将您的 action 和 reducer 定义捆绑到变更触发器中，并通过订阅让您的组件快速访问状态

## 🢂Don't 想读无聊的文字吗？你很幸运！在 Youtube 上观看演示:[https://youtu.be/JvN2Jr9dMSE](https://youtu.be/JvN2Jr9dMSE)(45 分钟)

是时候说出一个有点尴尬的事实了:从理论上来说，我喜欢 redux 的简单，然而不知何故，redux 的制作设置经常让我感到困惑。

我认为让我困惑并使我个人难以使用 redux 产品代码的主要原因是构建和维护一个正常工作的 redux 系统通常需要大量的开销。

以官方的 [React](https://hackernoon.com/tagged/react) Redux TodoMVC 为例:通过一个组件向存储添加 todo 的简单行为涉及到 *6 个单独的文件*:

1.  **在`constants/ActionTypes.js`中添加 _TODO** 动作常数
2.  **addTodo()** 动作创建函数`actions/index.js`
3.  减速器在`reducers/todos.js`
4.  `reducers/index.js`中的根减速器
5.  `containers/App.js`的集装箱组件
6.  允许用户在`components/Header.js`中添加待办事项的实际可视组件

*(* [*看看这个要点*](https://gist.github.com/aGuyNamedJonas/619bb650bf137ff8834ec99e486e478b) *我从文件中创建了高亮使用的代码)*

让我感到沮丧的是，我想要完成的只是两个非常简单的任务:
**【1】**让我的可视组件调用一个带有一些数据的函数，将 todo 项添加到存储中
**给整个过程命名——更具体地说:让 ADD_TODO 操作出现在我的 redux-devtools 中，这样我就知道发生了什么**

# **slim-redux-react 的设计目标**

**因此，为了解决这些问题，我创造了[slim-redux](https://github.com/aGuyNamedJonas/slim-redux#slim-redux)&[slim-redux-react](https://github.com/aGuyNamedJonas/slim-redux-react)三个具体的设计目标和 redux 的[基本原则](http://redux.js.org/docs/introduction/ThreePrinciples.html):**

## **设计目标#1:减少样板文件**

**在通读 redux 背后的三个[核心原则时，我意识到有可能重新包装我们与 redux 的工作方式，而不损害其最初的设计目标。](http://redux.js.org/docs/introduction/ThreePrinciples.html)**

**[slim-redux](https://github.com/aGuyNamedJonas/slim-redux#slim-redux) 通过让您将动作和缩减器定义捆绑到**变更触发器**(具有可选的有效负载验证)中来减少样板文件。**

**[slim-redux-react](https://github.com/aGuyNamedJonas/slim-redux-react) 让您的 react 组件通过**订阅**快速访问商店，并让它们通过**更改触发器修改状态。****

## **设计目标#2:更快地编码**

**特别是对于小的状态变化，常规的 redux-react 方法需要很长时间。我发现自己不得不设置相当多的代码来改变状态中的属性或向数组中添加元素。**

**[slim-redux](https://github.com/aGuyNamedJonas/slim-redux#slim-redux) 和 [slim-redux-react](https://github.com/aGuyNamedJonas/slim-redux-react) 的构建方式使得定义商店交互并将其传递给 react 组件变得非常快速和简单。**

## **设计目标#3:更容易推理代码**

**通过将 action 和 reducers 捆绑到 change 触发器(具有可选的有效负载验证)中，您需要了解的关于特定存储交互的所有信息都存在于单个 API 调用中的一个文件中。**

**当将 slim-redux 连接到 react 组件时，一个 API 调用就可以为可视化组件提供更改触发器和存储订阅，这样就可以从同一个文件中导出可视化组件和容器组件(大多数情况下)**

# **演示**

**一个演示说一千多个字，那么我们来看看用 [slim-redux-react](https://github.com/aGuyNamedJonas/slim-redux-react) 搭建的一个计数器 app:**

**这段代码是 [*的浓缩版 slim-redux-react 反例*](https://github.com/aGuyNamedJonas/slim-redux-react/tree/master/examples/counter) *。*可以直接下载，`npm install`然后`npm start`就可以了:)**

***(你也可以* [*在一个新的标签*](https://gist.github.com/aGuyNamedJonas/095bf4d332f9b90bb0be62544c456066) *中打开要点，跟随前进)***

## **第一步:创建 slim-redux 商店**

**与常规 redux 代码一样，我们首先需要创建一个商店。Slim-redux 是一项很好的运动，它与你已经存在的 redux 代码很好地共存——你可以在**中传递你已经存在的根 reduce**和**任何你已经拥有的中间件**到`createSlimReduxStore()`。**

**关于如何将 slim-redux-react 纳入您现有 redux 应用程序的更多详细信息，请参见[该配方](https://github.com/aGuyNamedJonas/slim-redux-react#integrating-into-existing-apps)和 `[createSlimReduxStore()](https://github.com/aGuyNamedJonas/slim-redux#createslimreduxstoreinitialstate-existingrootreducer-enhancer)`的 [API 参考。](https://github.com/aGuyNamedJonas/slim-redux#createslimreduxstoreinitialstate-existingrootreducer-enhancer)**

## **步骤 2:创建我们的计数器组件(只是表示部分)**

**这一步没有什么特别的——我们在这里做的只是创建计数器组件。花点时间看看计数器组件消耗的道具:**

*   **`props.counter`我们的反州是哪个**
*   **`props.increaseCounter`这是一个增加计数器的回调**
*   **`props.decreaseCounter`这是一个减少计数器的回调**

## **步骤#3:创建变更触发器定义**

**这就是你遇到 slim-redux-react 背后的**第一个基本概念**的地方:**动作和 reducer 定义被捆绑在一起成为变更触发器**。**

> **变更触发器捆绑了您的 action + reducer 定义。在一个地方，您可以了解关于特定商店互动的所有信息。**

**更改触发器的妙处在于，您可以在一个地方了解关于特定商店交互的所有信息:
**动作类型**、**缩减器代码**和 [**可选动作有效负载验证**、](https://github.com/aGuyNamedJonas/slim-redux#use-payload-validation)、一览无余。**

**你还能想象你能多快地添加新的动作+缩减器吗？无需在数千个文件中导航，只需将对象添加到您的更改触发器定义中，并在需要时将其导入。**

## **步骤 4:为计数器创建容器组件**

**在这一步中，我们将 slim-redux 存储连接到计数器组件。
注意界面有多小！**

> **订阅:状态树🢂道具的地图部分**

****订阅**是**状态树的一部分和一个道具**之间的**简单映射**。在这种情况下，我们将整个状态映射到`props.counter`。
在更复杂的状态树中，您可以将状态树中的路径映射到您想要传递给组件的道具，例如:**

**`subscriptions: { completeTodos: 'state.todos.completed'}`**

**[使用](https://github.com/aGuyNamedJonas/slim-redux-react/blob/master/src/selector.js)[重新选择库](https://github.com/reactjs/reselect)来实现订阅，以获得最佳性能。**

****变更触发器**也非常简单，但让我快速解释一下后台发生了什么:**

**当调用`createSlimReduxStore()`时，slim-redux 创建了一个标准的 redux 商店，但是在其中注入了 slim-redux 功能。在初始化存储时，slim-redux 还将`store.createChangeTrigger()`函数注入到存储中(更多信息参见 [API 参考](https://github.com/aGuyNamedJonas/slim-redux#storecreatechangetriggerparameters))。**

**仅在 slim-redux 中使用**时，以下是注册和使用更改触发器的方法:****

**当将变更触发定义传递给 [slim-redux-react](https://github.com/aGuyNamedJonas/slim-redux-react) 的`slimReduxReact()`函数时，`store.createChangeTrigger()`将在后台被调用，**产生的变更触发函数**将被**传递给你的组件**。**

**所以在我们的例子中，计数器组件获取`props.increaseCounter()`和`props.decreaseCounter()`作为它可以调用来修改(计数器)状态的函数。**

# **额外资源**

**我意识到这个帖子到目前为止可能已经有很多新的信息了，所以让我通过为你提供资源来了解更多关于[slim-redux](https://github.com/aGuyNamedJonas/slim-redux#slim-redux)&[slim-redux-react](https://github.com/aGuyNamedJonas/slim-redux-react)的信息来结束这个介绍:**

## **github 上的🢂 [slim-redux 库](https://github.com/aGuyNamedJonas/slim-redux)([NPM 包](https://www.npmjs.com/package/slim-redux))**

*   **[快速启动](https://github.com/aGuyNamedJonas/slim-redux#quick-start)**
*   **[API 参考](https://github.com/aGuyNamedJonas/slim-redux#api-reference)**
*   **[示例](https://github.com/aGuyNamedJonas/slim-redux#examples)**
*   **[动机](https://github.com/aGuyNamedJonas/slim-redux#motivation)**
*   **[目录](https://github.com/aGuyNamedJonas/slim-redux#table-of-contents)**

****食谱:****

*   **[在一个文件中捆绑更改触发器定义](https://github.com/aGuyNamedJonas/slim-redux#bundle-change-definitions)**
*   **[名称空间更改触发器定义](https://github.com/aGuyNamedJonas/slim-redux#namespace-change-triggers)**
*   **[在现有 redux 设置中使用 slim-redux](https://github.com/aGuyNamedJonas/slim-redux#existing-redux-setup)**
*   **[使用有效载荷验证](https://github.com/aGuyNamedJonas/slim-redux#use-payload-validation)**
*   **[构建定制中间件来捕捉有效负载验证错误](https://github.com/aGuyNamedJonas/slim-redux#middleware-validation-errors)**

## **github 上的🢂 [slim-redux-react](https://github.com/aGuyNamedJonas/slim-redux-react) 库( [npm 包](https://www.npmjs.com/package/slim-redux-react))**

*   **[快速启动](https://github.com/aGuyNamedJonas/slim-redux-react#quick-start)**
*   **[API 参考](https://github.com/aGuyNamedJonas/slim-redux-react#api-reference)**
*   **[例句](https://github.com/aGuyNamedJonas/slim-redux-react#examples)**
*   **[动机](https://github.com/aGuyNamedJonas/slim-redux-react#motivation)**
*   **[目录](https://github.com/aGuyNamedJonas/slim-redux-react#table-of-contents)**

****食谱:****

*   **[异步代码&变化触发](https://github.com/aGuyNamedJonas/slim-redux-react#bundle-change-definitions)**
*   **[简单与复杂的容器组件](https://github.com/aGuyNamedJonas/slim-redux-react#heavy-light-container-components)**
*   **[在现有的 react-redux 应用中使用 slim-react-redux](https://github.com/aGuyNamedJonas/slim-redux-react#integrating-into-existing-apps)**
*   **[减肥食谱](https://github.com/aGuyNamedJonas/slim-redux-react#slim-redux-recipes)**

# **结束语**

**我生命中的最后两周专门致力于这个项目，所以我真的希望这对你有帮助。如果你喜欢这两个项目，请成为我的新宠人类，在 Github 上给他们一颗星！:)**

**还有丹·阿布拉莫夫，如果你正在读这篇文章:我没有盗用你的名字“苗条身材”，我保证——我只是在完成这篇博文后，看到你前几天用这个名字发表了一个相当广泛的主旨。很抱歉！:)**

**我也很想听听你们的想法，回答你们的问题，听听对未来发展的建议。所以请不要犹豫，通过推特联系我们。**

**最后一个无耻的插播:我目前正在寻找一份 react 开发人员的全职工作，最好是在柏林。知道谁有兴趣雇用我吗？请联系:[jonas.peeck@googlemail.com](mailto:jonas.peeck@googlemail.com)**

**谢谢！:)**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**