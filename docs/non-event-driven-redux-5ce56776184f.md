# 非事件驱动的冗余

> 原文：<https://medium.com/hackernoon/non-event-driven-redux-5ce56776184f>

## 如果对前端来说通量的核心思想是错误的呢？

![](img/ab783f0732945cec14e9097d1d1d9536.png)

Photo by [NeONBRAND](https://unsplash.com/photos/-Cmz06-0btw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

当你第一次听说通量时，你可能会感到困惑。回过头来看，这么简单的东西竟然让我花了这么多文章和教程来理解，这有点令人惊讶。我非常感谢 Redux 是 Flux 最受欢迎的衍生产品之一，因为它确实是 Flux 核心方面的一个非常简单的表达——一个单向的数据流，涉及到调度一个动作来改变存储以改变视图的过程。

> 动作是简单的对象，包含新数据和一个标识性的*类型*属性。—[https://Facebook . github . io/flux/docs/in-depth-overview . html # content](https://facebook.github.io/flux/docs/in-depth-overview.html#content)

脸书给出了这个动作的具体定义，一个具有特定属性的普通 javascript 对象。尽管脸书从未明确地将动作改变商店的过程描述为事件驱动的架构，但当使用普通的 javascript 对象来改变商店时，很难想象有任何其他的设计模式。

事件驱动架构中有一些常见的关键模式。事件采购和事件通知是马丁·福勒[在他的博客](https://martinfowler.com/articles/201701-event-driven.html)中谈论的两个话题。事件源是关于存储一系列事件，以便可以用来重建系统状态。事件通知是关于从一个不知道谁使用这些动作的系统中分派动作。事件通知的优势之一是它提供了解耦。发出一个事件，任何进程都可以使用该事件，而不需要知道还有谁在使用该事件。我认为 Flux 肯定展示了事件源和事件通知，但是后者的质量有潜在的问题。

事件通知的一个问题是，很难跟踪到事件处理程序的事件流，因为该流是隐式的，而不是显式的。一旦一个事件被发出(或者一个动作被分派)，它就进入一种黑盒，返回改变后的状态。在 Redux 世界中，已经尝试采用某些标准来使这些关系更加明确(例如[像鸭子](https://github.com/erikras/ducks-modular-redux))，但是真正的问题是为什么有必要使这种关系更加明确？对于使用事件通知来说，这应该是一个可以接受的折衷，但是相反，没有人真正理解他们为什么要首先做出这样的折衷。

我认为 Flux 的部分问题在于脸书开发它是为了自己的应用。脸书有非常复杂的应用程序，我猜这些应用程序的前端组件非常复杂，以至于他们实际上需要多个团队来监管特定的领域。我认为 Flux 在这种情况下是有意义的，但就我个人而言，我从未参与过任何需要多个独立团队的项目。我说的不是 3 或 4 个人一起工作来完成前端的某些方面，我说的是 15 个以上的开发人员组成的专门部门，他们很少看到任何重叠的工作。

有可能通量对脸书来说并不理想。我们知道他们在开源技术之前几年就采用了 Graphql，如果他们使用 Relay 或一些等效的库来管理状态，那么他们并没有真正使用 Flux。是的，Relay 受到 Flux 的启发，但是 Relay 中调度动作的过程是如此的隐蔽，只有在使用调试工具时才可见。仔细想想，中继甚至可能不是调度动作。从官方的中继文档“commitUpdate 类似于在流动中调度一个动作”，但是为什么它是类似的而不是简单地调度一个动作呢？

流量和事件通知的真正问题是前端耦合的问题。我们听说了很多关于微服务和更小范围的无服务器功能的事情，但是在前端并没有相同的概念。前端的某些特性使得它几乎不可能分成更小的封装。可能是因为前端是所有东西汇集在一起的地方。也许是因为前端的体验应该贯穿整个应用程序。无论是什么，前端都很难分成更小的组件，因此当我们不可思议地从一个系统发出事件时，我们实际上只是在向自己发出事件。

Dan Abramov 谈到了 Redux 的[极限，以及它并不适用于每个项目。](/@dan_abramov/you-might-not-need-redux-be46360cf367) Redux 并不完美，但它很好地满足了自己的目的。两年前，它风靡全球，除了 Relay，它是 React 最流行的状态管理工具。也许是时候换一种方式了。也许是一种非事件驱动的 Redux 方法。

```
import {createStore} from 'noredux'const initialState = 1const store = createStore(initialState)const reducer = (state)=>state + 1store.dispatch(reducer)console.log(store.getState())// 2
```

不仅 Redux (noredux)是一个库，它用不同的方法来引起状态的变化。不是调度一个动作，而是调度一个缩减器。这是一种明确的状态变更方法。

但是不要被愚弄了，这种方法有它自己的局限性。以类似于传统 Redux 的 combineReducers 的方式实现关注点分离并不容易。

```
import {scopeReducer} from 'noredux'const pretodosReducerCreator = () => state => state + 1
const selector = state => state.todos
const setter = (state, result)=>({...state, todos: result})const todosReducerCreator = scopeReducer(selector, setter, pretodosReducer)
```

或者

```
import {defaultScopeReducer} from 'noredux'const pretodosReducerCreator = () => state => state + 1const todosReducer = defaultScopeReducer('todos', pretodosReducerCreator)
```

我个人认为，图书馆背后的理念比图书馆本身更重要。让 Redux 更显式。对于任何想要试用 Noredux 的人，你可以在 [noredux github repo](https://github.com/l2silver/noredux) 中看到一个例子。如果你决定尝试使用 Noredux，目前还没有太多的基础设施，所以你不能使用 chrome redux devtools 或 react-router-redux(但 react-redux 可以很好地与 noredux 配合使用)。

也就是说，我创建了一个 [redux-noredux](https://github/com/l2silver/redux-noredux) 包，这样你就可以在 redux 项目中使用 noredux， [github repo](https://github.com/l2silver/redux-noredux) 中有一个示例应用程序来演示它的用法。目前，这是 Noredux 的首选实现。

```
import {createStore} from 'redux'
import {noreduxAction, enableNoredux} from 'redux-noredux'
import reducers from './reducers'const store = createStore(enableNoredux(reducers), {todos: 0})const noreduxReducer = (state)=>({...state, todos: state.todos + 1})noreduxReducer.type = 'exampleType'
noreduxReducer.args = []store.dispatch(noreduxAction(noreduxReducer))/*
{
  type: '@@redux-noredux/exampleType'
  args: [],
  reducer: noreduxReducer,
  noredux: true,
}
*/
```

如果您使用 redux 的 combineReducers，这种方法有一点需要注意。CombineReducers 不允许没有附属于它们的 reducer 的状态属性，所以你需要伪造它。

```
import {combineReducers} from 'redux'
import {fakeReducer} from 'redux-noredux'
import soonToBeDones from './soonToBeDones'import {initialState as todosInitialState} from './todos'export default combineReducers({
  soonToBeDones,
  todos: fakeReducer(todosInitialState)
})
```

干杯。