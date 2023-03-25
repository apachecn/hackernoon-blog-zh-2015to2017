# Redux TDD:深度潜水

> 原文：<https://medium.com/hackernoon/redux-tdd-a-deep-dive-344cd7682a54>

*这是对*[*redux-TDD*](https://github.com/lmatteis/redux-tdd)*项目及其 API 如何工作的描述。如果你正在寻找关于 redux 测试驱动开发的更高层次的解释，请查看* [*我的 freeCodeCamp 文章*](https://medium.freecodecamp.org/test-driven-development-with-react-and-redux-using-redux-tdd-3fd3be299918) *。*

[Redux TDD](https://github.com/lmatteis/redux-tdd) 是一个简单的库，让你以测试驱动的[开发](https://hackernoon.com/tagged/development) (TDD)方式测试 [React](https://hackernoon.com/tagged/react) 组件以及 Redux 数据流。

在 API 的几次迭代之后，我想到了一个简单的抽象，它不太关注组件或状态的实现细节，而是更多地关注分派的动作和组件应该接收的道具:

```
import **reduxTdd** from 'redux-tdd';

**reduxTdd**({ counter: counterReducer }, state => [
  <Counter
    onClick={incrementAction}
    counter={state.counter.count} />
])
.**action**(props => props.onClick())
.**toMatchProps**({ counter: 1 })
.**contains**(<span>1</span>)
```

在上面这段代码中，我们[浅渲染](http://airbnb.io/enzyme/docs/api/shallow.html)(来自 enzyme)了**计数器**组件，并向其传递映射到状态特定部分的道具。

**计数器**属性映射到状态值 **state.counter.count** 。事实上，`state => []`函数不仅充当我们的渲染函数，还充当我们的 **mapStateToProps** 函数。

作为第一个参数 reduxTdd 采用我们的 reducers，因此我们的状态的形状。

接下来我们称之为`.action(props => props.onClick())`,这是奇迹发生的地方。传递给 **action** 操作符的回调所返回的任何内容都将被调度(尽管我们没有使用 redux 的调度——稍后会详细介绍)。

因此，动作操作符将更新内部状态，并根据这种新状态更新组件的属性。

**toMatchProps** 和 **contains** 此时只需检查我们的组件在动作被调度后是否收到了正确的值。

# TodoList 示例

在下一个例子中，我们将尝试以 TDD 的方式实现一个简单的 TodoList 还有什么😄？

我们首先简单地将尚不存在的 TodoList 组件传递给 reduxTdd:

```
reduxTdd({}, state => [ <TodoList /> ])
```

我们的列表项可能存在于 state 中，所以接下来让我们引入一个`list`缩减器，并将其作为一个名为“list items”的道具传递给我们的组件。

```
reduxTdd({ list }, state => [ <TodoList listItems={state.list} /> ])
```

运行这个当然会失败，因为还没有实现任何东西，但是让我们在实现我们的逻辑之前写更多的测试。

todo-list 意味着有一个输入框，您可以在其中添加 todo，所以让我们也添加一个 AddTodo 组件(记住我们返回一个数组，以便我们可以测试多个组件):

```
reduxTdd({ list }, state => [ 
  <TodoList listItems={state.list} />,
  <AddTodo onAdd={addTodo} />
])
```

很好，现在让我们继续上面例子中的点链接，并通过调用`onAdd`动作创建器来实际模拟添加一个 todo:

```
.it('should add todo items')
.switch(AddTodo) // the next dot-chained calls will work on AddTodo
.action(props => props.onAdd('clean house')) // add 'clean house'
```

所以接下来我们应该会在 TodoList 看到“清洁之家”todo，对吗？让我们测试一下:

```
.switch(TodoList) // back to TodoList
.toMatchProps({ listItems: ["clean house"] })
```

我们甚至可以添加另一个 todo:

```
.switch(AddTodo).action(props => props.onAdd('water plants'))
.switch(TodoList).toMatchProps({ listItems: [
  "clean house",
  "water plants"
]})
```

很好，这就足够了，让我们运行这段代码，开始实现我们的组件、减少器和动作:

```
const TodoList = ({ listItems }) => 
  <div>{listItems.map(item => <div>{item}</div>)}</div>const AddTodo = ({ onAdd }) =>
  <button onClick={onAdd}>add todo</button>
```

(提示:AddTodo 中有意省略了文本输入逻辑)

我们的 addTodo 操作:

```
function addTodo(todoText) {
  return { type: 'ADD_TODO', payload: todoText }
}
```

最后是我们的减速器:

```
function list(state = {}, action) {
  switch (action.type) {
    case 'ADD_TODO':
      return { ...state, [action.payload]: {} }
    default:
      return state
  }
}
```

如果我们再次运行我们的代码，有些事情会失败:

```
TypeError: listItems.map is not a function
```

这是因为我们的 state.list 是一个对象，但 listItems 属性需要一个数组。让我们使用一个选择器来解决这个问题(如果你愿意，你甚至可以使用[重新选择](https://github.com/reactjs/reselect))❤️

在我们最初的 reduxTdd 定义中，让我们改变状态映射到 TodoList 组件的 props 的方式:

```
reduxTdd({ list }, state => [ 
  <TodoList listItems={getVisibleItems(state)} />,
```

选择器:

```
function getVisibleItems(state) {
  return Object.keys(state.list).length
    ? Object.keys(state.list).map(key => key)
    : []
}
```

现在，如果我们再次运行测试，我们会得到:

```
TodoList
    ✓ should add todo items (1ms)
```

# 🎉

让我们也 TDD 一下“将待办事项设置为完整”的想法。

为此，我认为应该有一个 TodoItem 组件，是可点击的吧？让我们将它添加到我们的初始列表中:

```
reduxTdd({ list }, state => [
  <TodoList listItems={getVisibleItems(state)} />,
  <AddTodo onAdd={addTodo} />,
  <TodoItem onClick={completeTodo} />
])
```

但是有个问题！TodoItem 必须知道它必须完成哪一项。它必须对单个项目起作用，但是在我们的状态中，我们保留一个对象！要解决这个问题，我们可以简单地从 state.list 对象中传递一个条目:

```
reduxTdd({ list }, state => [
  <TodoList listItems={getVisibleItems(state)} />,
  <AddTodo onAdd={addTodo} />,
  <TodoItem onClick={completeTodo} **item**={state.list['water plants']}    
/>
])
```

接下来，我们将其标记为完成:

```
.switch(TodoItem)
.action(props => props.onClick('water plants'))
```

此时，我们意识到我们的 TodoList 组件无法显示该项是否完整(应该有一条删除线)。因此，我们传递一个新的“completedItems”属性，它是一个完整项目的数组:

```
.switch(TodoList)
.toMatchProps({ 
  listItems: ["clean house", "water plants"] 
  **completedItems**: ["water plants"]
})
```

我们再次改变我们的定义来为这样的行为工作:

```
reduxTdd({ list }, state => [
  <TodoList 
    listItems={getVisibleItems(state)} 
    completedItems={getCompletedItems(state)} />,
...
```

如果我们运行测试，他们会告诉我们一些需要实现的东西，比如我们的 completeTodo 操作创建器:

```
function completeTodo(todoText) {
  return { type: 'COMPLETE_TODO', payload: todoText }
}
```

我们相应地更新了我们的减速器:

```
function list(state = {}, action) {
  switch (action.type) {
    case 'ADD_TODO':
      return { ...state, [action.payload]: {} }
    case 'COMPLETE_TODO':
      return { ...state, [action.payload]: { status: 'complete' } }
    default:
      return state
  }
}
```

再次运行我们的测试将告诉我们 getCompletedItems 丢失了:

```
function getCompletedItems(state) {
  return Object.keys(state.list)
    .map(item => state.list[item])
    .filter(item => item.status === 'complete')
}
```

我们完事了。

我们可以更进一步，使用其他操作符，如 **contains** 和 **view** 来测试 UI 中更细粒度的部分。但是我们应用程序的主要部分是以 TDD 的方式一步一步实现的。

更重要的是，我们不必事先就我们的状态或道具做出复杂的工程决策。我们在前进的过程中，在看到需要的时候，实施了这些措施。

我希望这些例子能帮助您理解 Redux TDD 背后的思想，以及如何在您自己的项目中使用它。要查看更复杂的示例，请查看 repo 中的 [tests/](https://github.com/lmatteis/redux-tdd/tree/master/test) 文件夹。你会发现另一个重要的操作符叫做 **epic** ，它可以用来测试异步行为！

谢谢，TDD 快乐🤗