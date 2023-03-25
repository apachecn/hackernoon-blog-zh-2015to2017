# 关于 React 你需要知道的 13 件事

> 原文：<https://medium.com/hackernoon/13-things-you-need-to-know-about-react-d2e6a6422552>

![](img/982583780dae78fd2356c6bb3511056e.png)

*Image by* [*Franco Folini*](https://www.flickr.com/photos/livenature/)

我已经使用 React 一年多了。我也在进行培训，帮助人们从零开始学习。我注意到在每次训练中，我都在一遍又一遍地解释同一套概念。我认为如果你想“说话反应”，这些概念是必不可少的。如果你正在学习它，你可能会有兴趣阅读这篇文章。

# 1)它不是一个框架

Angular 或 Ember 是已经为你做了一些决定的框架。React 只是一个库，你需要自己做所有的决定。它侧重于帮助您使用组件构建用户界面。

它对你的服务器通讯、翻译、路由等没有帮助。一些人认为这是一个弱点。我同意一位智者曾经说过的话:

> *“框架解决了它的创造者的问题”~我的一个导师*

React 很薄，很容易与其他第三方库混合使用。丰富的 JS 生态系统，什么都有一个库。你可以选择你最喜欢的一个，然后把它插上，而不用处理框架的设计决策/限制。

当然还有[大名鼎鼎的 JS 疲劳](/@ericclemmons/javascript-fatigue-48d4011b6fc4#.ovtpj2ypn)。如果你也有同样的感觉，并且不想自己做每一个决定，做一个 30 分钟的研究，选择一个[固执己见的初学者工具包/样板](http://andrewhfarmer.com/starter-project/)，改变你想要的，然后使用它。

# 2) JSX

在看 React 例子的时候，你可能已经看到过 [JSX](https://facebook.github.io/react/docs/introducing-jsx.html) 在工作。但是 React 代码也可以用普通的 JS 编写:

```
const rootElement =
 React.createElement(‘div’, {},
   React.createElement(‘h1’, {style: {color: ‘red’}}, 
     ‘The world is yours’),
   React.createElement(‘p’, {}, 
     ‘Say hello to my little friend’)
 )ReactDOM.render(rootElement, document.getElementById(‘app’))
```

有些人不喜欢将整个标记代码写成函数调用。这可能就是为什么脸书的人想出了 JSX——一个“React.createElement(组件，道具，…子元素)函数的语法糖”。

这就是为什么我们可以将上面的例子重构为:

```
const RootElement = (
  <div>
    <h1 style={{color: red}}>The world is yours</h1>
    <p>Say hello to my little friend</p>
  </div>
)ReactDOM.render(RootElement, document.getElementById('app'))
```

在构建过程中 [Babel](https://babeljs.io/docs/plugins/preset-react/) 会将标记转换成普通的 JS。

# 3)是 JavaScript

要在 React 中动态生成标记，还可以使用 JS:

```
<select value={this.state.value} onChange={this.handleChange}>
  {somearray.map(element => <option value={element.value}>
                              {element.text}
                            </option>)}
</select>
```

在上面的例子中，使用`map`函数将`somearray`数组映射到一列`<option>`元素。这里唯一不同于普通 HTML 的是`<select>`元素上的`value`，它为您设置了`selected`属性。

但是并不要求使用它:

```
<select onChange={this.handleChange}>
  {somearray.map(element => (
      <option
        value={element.value}
        selected={this.state.value === element.value}
      >
        {element.text}
      </option>
    ))}
</select>
```

许多流行的框架使用模板语言进行类似上面的操作。每个框架都有自己的框架。所以每次你想使用一个框架的时候，你都需要学习一种新的模板语言，它的特点和缺点。

> *上面的代码显示了一个关于缺少*键*属性的警告。要了解原因和解决方法，请访问* [*本页*](https://facebook.github.io/react/docs/lists-and-keys.html) *或阅读您的开发工具中的错误消息；).*

我记得我第一次在 Angular 1 中使用`<select>`控件。有一个特殊的`ngOptions`指令会为你生成所有可能的`<options>`。

```
<select
  ng-model="selectedItem"
  ng-options="item as item.name for item in items">
</select>
```

直到今天我也不知道`item as item.name for item`是什么意思。

有比我更聪明的人，他们记住了如何使用各种模板语言。我经常从后端切换到前端开发。有很长一段时间我根本不做前端。如果你不使用它，知识就会消失。这就是为什么死记硬背对我不起作用。我对`map()`功能和 HTML 很熟悉，所以 React 方式对我很有吸引力。

另一个好处是静态代码分析是免费的。lint JS 肯定比定制模板标记更容易。

对我来说，模板是字符串驱动的开发。没有严肃的林挺。只是 HTML 中的一些魔术，没有以任何方式检查。JSX 给 JS 添加了更多的东西，使它更加强大。这也是我不同意人们称 JSX 为模板语言的原因。

# 4)它是陈述性的

在 React 中，您使用声明式风格来编写组件。让我们来看看前面的例子`<select>`:

```
<select value={this.state.value} onChange={this.handleChange}>
  {somearray.map(element => <option value={element.value}>
                              {element.text}
                            </option>)}
</select>
```

在这个`<select>`示例中，您没有使用`for`循环来手动创建映射集合。你不是在说*应该做什么*，而是说*应该是什么样子*。

# 5)你将关注点分开

在 React 中，你将 HTML、JS 和 CSS 作为一个组件放在一起。如果您想在网格上显示一行，您可以创建一个`Row`组件，并将 HTML、逻辑和行为放在一个文件中。

多年来，我们将 JS、HTML 和 CSS 分成不同的文件。皮特·亨特称之为技术分离。它不应该与关注点分离相混淆。

如果我认为这种“缺乏分离”很奇怪，我经常会受到质疑。但让我感到奇怪的是，人们经常举一些例子来捍卫将 HTML 和 JS 分开的策略:

“如果你把 HTML 和 JS 放在不同的文件中，你可以很容易地替换 HTML 并保持 JS 不变”。

如果你仔细想想，事情并不是这样的。对 HTML 结构的大多数更改都需要重构 JS 逻辑。

> *“显示逻辑和标记不可避免地紧密耦合”~皮特·亨特*

如果您将文本输入更改为复选框，您需要重写您的逻辑。不要逃避。

# 6)数据下降

在 React 中，数据沿着组件树向下。如果你想将数据从父组件传递到子组件，你需要使用 [**道具**](https://facebook.github.io/react-native/docs/props.html) 。从 JSX 的观点来看，道具是 HTML 的属性。

父组件:

```
<div>
  <Greetings color={red} text='Hello' />
</div>
```

在子组件中，`this.props`下有道具。

子组件:

```
const Greetings = React.createClass({
  render () {
    const {color, text} = this.props
    const divStyle = {padding: 10, backgroundColor: 'black'}
    const headingStyle = {color: color} return (
      <div style={divStyle}>
        <h1 style={headingStyle}>{text}</h1>
      </div>
    )
  }
})
```

# 7)状态

到目前为止，我们只讨论了静态组件，静态数据沿着组件树向下传递。通常，需要创建一个状态随时间变化的 [**有状态组件**](https://facebook.github.io/react/docs/state-and-lifecycle.html) 。

让我们考虑一个`<input>`,您可以在这里输入文本，文本将显示在下面。

```
const InputBox = React.createClass({
  getInitialState () {
    return {
      text: ''
    }
  }, changeText (event) {
    this.setState({text: event.target.value})
  }, render () {
    return (
      <div>
        <input type='text' onChange={this.changeText} 
          placeholder='text' value={this.state.text} />
        <span>{this.state.text}</span>
      </div>
    )
  }
})
```

开始时，您设置组件的默认状态。在这种情况下，我们希望有一个空的`text`值。为此，您使用组件方法`getInitialState()`，该方法必须返回组件的状态对象。

为了更新状态，一个事件处理器`changeText()`被分配给`onChange`事件。要更新状态，React 期望您使用内置的`[setState()](https://facebook.github.io/react/docs/react-component.html#setstate)`方法。

状态更新将被调度，组件将在更新完成后重新呈现。`setState()`需要使用 call 来通知 React 挂起的状态更改，以便它可以应用这些更改。没有任何类型的循环来跟踪是否发生了变化。

你需要记住`setState()`是异步的。结果不会立竿见影。以下示例显示了应用更改后立即访问状态的好方法和坏方法:

```
// BAD:
// ...
someFunction (value) {
  this.setState({someValue: value})
  // may not be changed at this point
  console.log('New value: ', this.state.someValue)
}
// ...// GOOD:
// ...
someFunction (value) {
  this.setState({someValue: value}, () => {
    // do stuff with new state
    console.log('New value: ', this.state.someValue)
  })
}
// ...
```

好吧，但是如果需要将状态更改传播给父组件呢？

# 8)事件上升

假设我们有一个`Parent`组件，它需要从前面例子的子组件`InputBox`中获取数据。

```
const Parent = React.createClass({
  gimmeThatState (textFromInput) {
    console.log(textFromInput)
    // or this.setState({text: textFromInput})
  },render () {
    <InputBox pushChangesUp={this.gimmeThatState} />
  }
})
```

`Parent`组件向下传递一个函数(作为道具)，这个函数可以被`InputBox`用来向上推一些数据。

更新后的`InputBox`可能是这样的:

```
const InputBox = React.createClass({
  propTypes: {
    pushChangesUp: React.PropTypes.func.isRequired
  }, getInitialState () {
    return {
      text: ''
    }
  }, changeText (event) {
    this.setState({text: event.target.value})
  }, pushChangesUp () {
    this.props.pushChangesUp(this.state.text)
  } render () {
    return (
      <div>
        <input type='text' onChange={this.changeText} 
          placeholder='text' value={this.state.text} />
        <span>{this.state.text}</span>
        <button onClick={this.pushChangesUp}>
          Push changes up
        </button>
      </div>
    )
  }
})
```

您首先看到的是组件声明开头的一个`propTypes`属性。这是用来验证道具的。对我来说，它的作用类似于面向对象编程中的接口。通过查看`propTypes`，我立即知道我需要向组件提供什么来使其工作。

接下来，本地`pushChangesUp()`事件处理程序被分配给按钮上的`onClick`事件。点击时，该函数使用 props 中的`pushChangesUp()`将数据向上推。

现在，`Parent`组件可以以自己的状态保存数据，并将其传递给不同的组件。

# 9)渲染的工作原理

每个`setState()`调用通知 React 状态变化。然后，React 调用`render()`方法来更新内存中的组件表示([虚拟 DOM](http://reactkungfu.com/2015/10/the-difference-between-virtual-dom-and-dom/) )并将其与浏览器中呈现的内容进行比较。如果有变化，React 会对 DOM 进行尽可能小的更新。

子组件知道它们需要重新渲染，因为它们的道具改变了。

我经常把它比作 Git 中的 diff 机制。有两个组件树快照，React 比较并交换需要交换的内容。

我在寻找一个聪明的图表来描述渲染流程，但是没有找到。不过你可以在这里了解更多。

# 10)构图是关键

拥有状态的父组件通常被称为**容器组件**。他们负责状态管理和渲染孩子(这听起来好奇怪)。子组件用于触发从父组件传递下来的事件处理程序(如前面示例中的`InputBox`组件)并显示数据。

负责显示数据的子组件被称为**表示组件**。

容器组件往往负责取数据、API 调用(见`[componentDidMount()](https://facebook.github.io/react/docs/react-component.html#componentdidmount)`生命周期方法)等等。您应该将它放在一个地方，以避免在表示组件中出现副作用。除了显示数据之外，这些应该尽可能地简单。

这种关注点和术语的分离是由《T4》的作者丹·阿布拉莫夫推广的。你可以在他的文章中了解更多。

你可以看到这一切都吻合在一起。当每个组件遵循单一责任原则时，它可以与其他组件组合并重用。

最大的挑战是弄清楚如何划分这些责任，以及将国家置于何处。如果你想了解更多关于这个话题的信息，请搜索[“在反应中思考”](https://facebook.github.io/react/docs/thinking-in-react.html)文章。

# 11)保持小政府

在我的训练中，经常有一个涉及某种列表过滤的练习。假设你有一个待办事项列表:

```
const initialState = [
  {id: 1, text: 'laundry'},
  {id: 2, text: 'shopping'}
  // ...
]const List = React.createClass({
  getInitialState () {
    return {
      todos: initialState
    }
  }, render () {
    return (
      <div>
        <ul>
          {this.state.todos.map(todo => <li key={todo.id}>{todo.text}</li>)}
        </ul>
      </div>
    )
  }
})
```

现在，您想添加一个搜索框。50%的人会选择这种方法的一些变体:

```
const initialState = [
  {id: 1, text: 'laundry'},
  {id: 2, text: 'shopping'}
  // ...
]const List = React.createClass({
  getInitialState () {
    return {
      todos: initialState,
      filteredTodos: null
    }
  }, search (searchText) {
    const filteredTodos = this.state.todos.filter(todo => 
      todo.text.indexOf(searchText) > 0) this.setState({filteredTodos: filteredTodos})
  }, render () {
    // get todos from state
    const {filteredTodos, todos} = this.state // if there are filtered todos use them
    const list = filteredTodos === null ? todos : filteredTodos return (
      <div>
        <SearchBox onChange={this.search} />
        <ul>
          {list.map(todo => <li key={todo.id}>{todo.text}</li>)}
        </ul>
      </div>
    )
  }
})
```

你在这里看到的是重复的状态，两个真实的来源和意大利面条式代码的介绍。假设您想要更新一个打开了搜索过滤器的待办事项。

什么更好？让州政府尽可能的小。如果某个东西可以被[即时计算](https://facebook.github.io/react/docs/thinking-in-react.html#step-3-identify-the-minimal-but-complete-representation-of-ui-state)那么它就应该是。

这里有一个更好的解决方案:

```
const initialState = [
  {id: 1, text: 'laundry'},
  {id: 2, text: 'shopping'}
  // ...
]const List = React.createClass({
  getInitialState () {
    return {
      todos: initialState,
      searchText: null
    }
  }, search (searchText) {
    this.setState({searchText: searchText})
  }, filter (todos) {
    if (!this.state.searchText) {
      return todos
    } return todos.filter(todo => 
      todo.text.indexOf(this.state.searchText) > 0)
  }, render () {
    const {todos} = this.state return (
      <div>
        <SearchBox onChange={this.search} />
        <ul>
          {this.filter(todos).map(todo => <li key={todo.id}>
                                           {todo.text}
                                         </li>)}
        </ul>
      </div>
    )
  }
})
```

这里只保留了用于在呈现之前过滤列表的`searchText`。更干净的方法，更小的政府，没有重复。

# 12)条件渲染

在前面的示例中，列表是根据一些条件逻辑呈现的。如果满足某些条件，通常需要呈现标记的一部分，如果不满足，则呈现另一部分。在 React 中，有几种方法可以做到这一点。

## 三元运算符

在 JSX 中，可以使用[三元运算符](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)来执行条件渲染:

```
React.createClass({
  getInitialState () {
    return {
      hideTodos: true
    }
  }, render () {
    return (
      <div>
      {
        hideTodos ? 'Sorry there is no data' : <TodoList />
      }
      </div>
    )
  }
})
```

可能的变化:

*   `return`表达式外的三元运算符
*   if/else 块在`return`表达式之外

## 助手功能

```
React.createClass({
  getInitialState () {
    return {
      hideTodos: true
    }
  }, renderTodos () {
    if (this.state.hideTodos) {
      return 'Sorry there is no data'
    } return <TodoList />
  } render () {
    return (
      <div>
      {
        this.renderTodos()
      }
      </div>
    )
  }
})
```

这是一个有用的方法，但是当组件更大时，你需要在助手和一个`render()`方法之间来回切换。

## 一个组件

这可能是最干净的方法，作为[功能切换](http://martinfowler.com/bliki/FeatureToggle.html)效果很好。

```
React.createClass({
  getInitialState () {
    return {
      hideTodos: true
    }
  }, render () {
    return (
      <div>
        <HideIf condition={this.state.hideTodos}>
          <TodoList />
        </HideIf>
      </div>
    )
  }
})const HideIf = React.createClass({
  render () {
    if (this.props.condition) {
      return <span>'Sorry there is no data'</span>
    } // children is what's inside <HideIf> element
    return this.props.children 
  }
})
```

# 13)你不需要助焊剂

React 新手最常见的误解可能是，您需要将它与 Redux 或其他 Flux 实现一起使用。

Redux 很棒。这是最受欢迎的 Flux 实现，这要归功于[优秀的教程](https://egghead.io/courses/getting-started-with-redux)，大量的特性和干净、实用、可测试的方法。但是你可能不需要它。

如果您正在学习 React，如果您的应用程序很小，如果您不需要全局状态或者您在跟踪应用程序中的状态变化方面没有任何问题— *不要使用它*。

如果你想了解更多，请阅读这里的。

# 总结

所以，这是我和人们讨论最多的反应概念列表。我强烈推荐阅读 [React docs](https://facebook.github.io/react/docs/hello-world.html) ，因为如果你从头到尾阅读，这是最好的知识来源。我还推荐观看早期的 React 视频(从 2013 年到 2014 年)[，描述脸书在创建 React](https://www.youtube.com/watch?v=x7cQ3mrcKaY) 时试图解决什么问题。这将有助于你认识到你是否有类似的问题*并做出反应，这将有助于你*或者你应该坚持使用其他技术。

【aimforsimplicity.com】原载于[](http://aimforsimplicity.com/post/13-things-you-need-to-know-about-react/)**。**

*[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)**[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)**[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*

*![](img/be0ca55ba73a573dce11effb2ee80d56.png)*