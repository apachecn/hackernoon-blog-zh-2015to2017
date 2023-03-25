# 组织反应道具

> 原文：<https://medium.com/hackernoon/organizing-react-props-e916e3c579a4>

随着[代码库](https://hackernoon.com/tagged/codebase)中组件数量的增长，道具的数量也在增长，拥有一个将道具从“容器”传递到“哑组件”的良好模式变得非常重要。我想介绍一下传递道具通常是如何开始的，以及一些改善体验的模式。

作为一个例子，我将使用一个 todo 应用程序，其功能在这篇文章中的[中列出，但是我们将把这个应用程序分成多个组件。我还假设所有的数据和处理程序都通过使用](/@scott.lively/a-react-journey-9f8c885cfbce) [redux](https://hackernoon.com/tagged/redux) 、recompose、RxJS 等作为道具传递给 TodosListPage 容器

# 开始时，我们通过了道具，这很好

一般来说，我们从一个装有所有道具的容器开始，然后把它们传给孩子们。这些孩子可能会将这些道具传递给他们的孩子，等等。这里的一个大问题是，如果我们需要向 TodosListPage 的任何子节点添加一个新道具，我们需要将它添加到每个组件，直到到达需要该道具的节点。如果这是一个深度嵌套的组件，那么以任何方式重构道具都将成为一大痛苦。

```
class TodosListPage extends Component {propTypes = {
  todos: PropTypes.array.isRequired, 
  newTodoText: PropTypes.string.isRequired,
  filterText: PropTypes.string.isRequired,
  fetchTodos: PropTypes.func.isRequired, 
  addTodo: PropTypes.func.isRequired, 
  setNewTodoText: PropTypes.func.isRequired, 
  setFilterText: PropTypes.func.isRequired
 };

 render() {
  const { 
   todos, 
   newTodoText, 
   addTodo, 
   filterText, 
   setFilterText 
  } = this.props;

  return (
   <div>
    <AddTodoForm newTodoText={newTodoText} addTodo={addTodo}/>
    <TodosFilter filterText={filterText} 
                    setFilterText={setFilterText}/>
    <TodoList todos={todos}
   </div>
  );
 }

 componentDidMount() {
  this.props.fetchTodos();
 }
}
```

# 然后我们的道具成型了，更好了

现在，我们将传递代表容器子项道具的对象，而不是将每个道具传递给容器。这意味着只需要在两个地方改变道具，一个是创建传入道具的对象(redux、reselect、RxJS 等)，另一个是接收组件。任何传递组件现在都只是简单地将道具传递给孩子们，而不关心道具包里是什么。它还有一个很大的副作用，可以帮助你快速了解哪些道具只被你正在查看的组件使用。来自 PropTypes.shape 的警告也更准确地描述了道具类型警告来自何处。转向这种模式对我来说是一个巨大的胜利。

```
class TodosListPage extends Component { propTypes = {
  fetchTodos: PropTypes.func.isRequired,  
  addTodoForm: PropTypes.shape(AddTodoForm.propTypes).isRequired,
  todosFilter: PropTypes.shape(TodosFilter.propTypes).isRequired,
  todosList: PropTypes.shape(TodoList.propTypes).isRequired
 }; render() {
  const { addTodoForm, todosList, todosFilter } = this.props; return (
   <div>
    <AddTodoForm {...addTodoForm}/>
    <TodosFilter {...todosFilter}/>
    <TodoList {...todosList}/>
   </div>
  );
 } componentDidMount() {
  this.props.fetchTodos();
 }
}
```

# [我是如何学会不再担心并爱上这个容器的](https://en.wikipedia.org/wiki/Dr._Strangelove)

最后，随着我开始使用 PropTypes.shape，越来越清楚的是，每个使用实例可能代表一个应该只是容器的组件。根据我的经验，这可能有点难以管理。您希望确保不要做得太过，这需要在 React 之外如何存储和处理状态的原则。如果不确定，我宁愿选择只有一个容器，但是在上面的例子中，将每个组件连接到商店(从而创建一个容器)可能是合理的。我认为这种方法如果做得正确是理想的。

```
class TodosListPage extends Component { propTypes = {
  fetchTodos: PropTypes.func.isRequired
 }; render() {
  return (
   <div>
    <AddTodoForm/>
    <TodosFilter/>
    <TodoList/>
   </div>
  );
 }componentDidMount() {
  this.props.fetchTodos();
 }
}
```

最后，我强烈建议尽快放弃第一种方法。它解决了许多令人头痛的问题，帮助您看到 UI 中出现的组件和模式，同时也使重构变得更加容易。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！