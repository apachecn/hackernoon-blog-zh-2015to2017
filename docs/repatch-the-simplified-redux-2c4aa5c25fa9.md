# 重新修补—简化的 Redux

> 原文：<https://medium.com/hackernoon/repatch-the-simplified-redux-2c4aa5c25fa9>

我已经参与 [react](https://www.npmjs.com/package/react) - [redux](https://www.npmjs.com/package/redux) 项目好几年了。在我第一次见到 flux 之后，我对它的表达能力印象深刻，它描述了与其他数据流概念相反的复杂用例，当项目的复杂性增加时，它会引起许多麻烦。

**动作控制数据流概念简单明了。**数据更改可以被描述为具有最小负载的动作。这些操作为应用程序的生命周期创建了一个确定的、与时间无关的历史。应用程序在给定点的状态可以通过在链中选择一个动作来还原。

Redux 的概念有许多理论原理和优点，但我不打算谈论它们。不可变性只有一个主要缺点:它的成本。但是**我们必须为不可变数据处理付出的代价是通过避免 React 应用程序中的重渲染和重排版得到的多重回报。我们总是可以跟踪两个连续状态之间的差异，这就是为什么我不能把不变性列为 Redux 的缺点。**

# 动机

Redux 还有一个缺点:令人痛苦的冗长。

假设我们想要创建一个异步动作，该动作获取用户并将其保存在 Redux store 实例中。我们需要 3 个动作定义:

```
const START_FETCHING_USERS = "START_FETCHING_USERS"; 
const RESOLVE_FETCHING_USERS = "RESOLVE_FETCHING_USERS"; 
const REJECT_FETCHING_USERS = "REJECT_FETCHING_USERS";
```

第一个动作类型`START_FETCHING_USERS`启动流程，`RESOLVE_FETCHING_USERS`提供新的一组用户，如果获取过程中出现错误，则发出`REJECT_FETCHING_USERS`。

让我们看看动作创作者:

```
**const** startFetchingUsers = () => ({ type: START_FETCHING_USERS });  
**const** resolveFetchingUsers = users => ({ type: RESOLVE_FETCHING_USERS, users });  
**const** rejectFetchingUsers = error => ({ type: RESOLVE_FETCHING_USERS, error });
```

减速器:

```
**const** initState = {  
 isFetching: false,
 users: [],
 error: null
}**const** reducer = (state = initState, action) => {  
 **switch** (action.type) {
   **case** START_FETCHING_USERS: **return** {
     ...state,
     isFetching: true
   };
   **case** RESOLVE_FETCHING_USERS: **return** {
     ...state,
     isFetching: false,
     users: action.users
   };
   **case** REJECT_FETCHING_USERS: **return** {
     ...state,
     isFetching: false,
     error: action.error
   };
   **default**: **return** state;
 }
}
```

剩下的工作就是实现 async [thunk](https://www.npmjs.com/package/redux-thunk) 动作创建器:

```
**const** fetchUsers = () => **async** (dispatch, getState, { api }) => {  
 dispatch(startFetchingUsers());
 **try** {
   **const** users = **await** api.get('/users');
   dispatch(resolveFetchingUsers(users));
 } **catch** (error) {
   dispatch(rejectFetchingUsers(error.message));
 }
}
```

好了，我们完成了冗余部分&我们快完成了。现在我们只需要将动作创建者和状态连接到 React 组件，我们就可以开始了！

对于这个简单的特性，我们需要输入许多行

*   动作类型，
*   动作创作者，
*   减速器中的动作处理器、

我们还没有编写任何视图组件。

当我们参与开发一个具有数千个动作类型、动作创建者和子缩减者的大型应用程序时，这尤其不方便。这也导致了进一步的困难，因为这些资源被分离在不同的位置的许多文件中。因此，如果我们想跟踪一个动作的效果，我们必须跟踪许多文件的数据流，这很容易丢失。

通过查看 [npm](https://www.npmjs.com/) ，我们最有可能找到一堆库/助手/中间件，它们帮助我们避免打字，但是使用它们会引入一些其他类型的打字开销，因为我们需要在每个文件中导入它们。

也许我们应该想一个更简单的办法，考虑一下 Redux 到底需要哪些功能。

1) **我们必须保持数据不变吗？可变性是通往地狱的高速公路。所以这不是办法。尤其是在反应应用中。**

2) **我们一定要知道一个动作的名字吗？**在大多数情况下，动作只在单个地方使用。我们不需要保持它们的可复制性。如果您有方法调度匿名操作呢？这太好了。

3) **我们必须能够序列化动作吗？**在一些用例中，您绝对需要可序列化，但是在大多数应用程序中，您并不需要。所以让我们继续假设这不是现在的要求。

我们应该坚持第一个限制，同时我们可以放心地忘记其他限制。

我们应该转换 Redux 概念，使我们能够简单地创建动作。我们想把一个动作描述成一个单一的功能，要么就地。

# 重新修补

[Repatch](https://www.npmjs.com/package/repatch) 从定义集中删除动作类型和动作创建者，并回答问题:“如果 reducers 是动作的有效负载会怎样？”。这个图书馆的信条是:

**调度减速器**

```
store.dispatch(state => ({ ...state, counter: state.counter + 1 }));
```

**在这个术语中，动作是一个返回缩减器的函数:**

```
**const** increment = amount => state => ({  
  ...state,
  counter: state.counter + amount
});store.dispatch(increment(42));
```

Repatch 还有一个`Store`类，我们可以用初始状态实例化它:

```
**import** Store **from** 'repatch';**const** store = **new** Store(initialState);
```

Repatch 的接口与 [redux](https://www.npmjs.com/package/redux) 的接口非常相似，因此我们可以将它与 [react-redux](https://www.npmjs.com/package/react-redux) 库一起使用。`dispatch`和`subscribe`方法与 Redux 的`Store`方法具有相同的签名。

# 中间件和异步操作

Repatch 还有一个链接中间件的接口。这对于使用您喜欢的异步操作中间件来说很方便。这个包提供了一个`thunk`中间件——类似于[redux-thunk](https://www.npmjs.com/package/redux-thunk)——对于创建异步动作很有用。如果你的 reducer 返回一个函数，中间件会自动认为它是一个异步动作。商店实例将把`dispatch`和`getState`函数作为参数传递给它。您可以设置中间件为。例如，您可以使用它来注入您的客户端 API 库。

让我们看看下面与我们的用例相关的例子:

```
**const** fetchUsers = () => _ => **async** (dispatch, getState, { api }) => {  
 dispatch(state => ({ ...state, isFetching: true }));
 **try** {
   **const** users = **await** api.get('/users');
   dispatch(state => ({ ...state, users }));
 } **catch** (error) {
   dispatch(state => ({ ...state, error: error.message }));
 } **finally** {
   dispatch(state => ({ ...state, isFetching: false }))
 }
}
```

使用这个`thunk`中间件展示了 repatch 的真正威力，因为我们只用几行代码就可以描述异步操作。如您所见，我们不需要在 reducer 中定义详细的动作类型、动作创建者和动作处理者，因为我们可以简单地分派一个适当定义的箭头函数，从而创建一个**匿名动作**。多酷啊。这使得动作也可以从组件中创建。

剩下的就是初始状态的`Store`实例化:

```
**const** store = **new** Store({  
 isFetching: false,
 users: [],
 error: null
});
```

在某个地方分派行动:

```
store.dispatch(fetchUsers())
```

让我们看另一个例子:

```
**const** updateUser = delta => state => **async** (dispatch, getState, { api }) => {  
 **try** {
   **const** editedUserId = getState().editedUser;
   dispatch(toggleSpinner(true));
   **await** api.put(`/users/${editedUserId}`, { body: delta });
   **await** dispatch(fetchUsers());
   dispatch(toggleSpinner(false));
 } **catch** (error) {
   dispatch(state => ({ ...state, isFetching: false, error: error.message }));
 }
};
```

您可以从函数签名中看到，在这个例子中，额外的参数是我们的客户端 API 对象，正如我前面提到的。另外，请注意，reducer 的`state`参数对于读取状态并不总是令人满意的，因为它是从动作被触发时开始的一个瞬时表示。因此我们需要使用`getState`函数来代替`state`。

在这个例子中，`toggleSpinner`是一个常规的同步动作，我们可以`dispatch`。`api.put`方法是一个简单的调用 API 的异步方法，对它来说没有任何障碍。线`await dispatch(fetchUsers())`更有趣一点。使用 [redux-thunk](https://www.npmjs.com/package/redux-thunk) 我们习惯于在彼此内部嵌入异步动作并等待它们。

# Redux 中的子减速器

Redux 的 reducers 可以组成一个层次结构。这样，我们不需要定义一个巨大的减速器，相反，我们可以将它们分成更小的嵌套减速器。组合 reducer 并不神奇，我们只是创建了一个 reducer，使用它们的子状态将部分一个接一个地还原为一个对象。

```
**const** rootReducer = (state, action) => ({  
 foo: fooReducer(state.foo, action),
 bar: barReducer(state.bar, action)
});
```

相当于

```
**const** rootReducer = redux.combineReducers({  
  foo: fooReducer,
  bar: barReducer
});
```

# 重新修补中的子减速器

Repatch 还提供了一种组合子减速器的方法。我们只是定义了一个函数，它将一个嵌套的 reducer 作为参数，并返回一个缩减整个状态的 reducer:

```
**const** reduceFoo = fooReducer => state => ({  
 ...state,
 foo: fooReducer(state.foo)
});
```

现在减少`foo`属性很容易。假设我们想在`foo`对象中设置一个`x`属性:

```
**const** setX = x => reduceFoo(state => ({ ...state, x }));
```

如果 sub-reducer 描述了一个深度嵌套的属性，这将非常有用:

```
**const** reduceFoo = reducer => state => ({  
  ...state,
  bar: {
    ...state.bar,
    foo: reducer(state.bar.foo)
  }
});
```

# 测试

测试怎么样？为一个缩减器编写单元测试很简单:

```
**import** * **as** assert **from** 'assert';  
**import** { changeName } **from** './actions';// ...it('changeName', () => {  
 **const** state = { name: 'john' };
 **const** nextState = changeName('jack')(state);
 assert.strictEqual(nextState.name, 'jack');
});
```

异步操作稍微复杂一点，因为它们的生效依赖于外部资源，比如 store 实例和其他 API。但是外部资源在所有环境下总是需要被嘲讽的。

```
**import** Store, { thunk } **from** 'repatch';  
**import** * **as** assert **from** 'assert';**const** mockUsers = [{ username: 'john' }];  
**const** mockApi = {  
 getUsers: () => **Promise**.resolve(mockUsers)
}// ...it('fetchUsers', **async** () => {  
 **const** state = { users: [] };
 **const** store = **new** Store(state)
   .addMiddleware(thunk.withExtraArgument({ api: mockApi }));
 **await** store.dispatch(fetchUsers());
 **const** nextState = store.getState();
 assert.deepEqual(nextState.users, mockUsers);
});
```

# 待办事项应用程序

每个 javascript 库都有一个 todo 示例，所以 repatch 也有[一个](https://community.risingstack.com/repatch-the-simplified-redux/(https://github.com/jaystack/repatch-example-electron-app))。如果您正在寻找 TypeScript 示例，您可以在这里找到它[。](https://github.com/jaystack/repatch-example-electron-app-ts)

*这篇由* [彼得·豪斯克内希特](https://twitter.com/hasyee04) *撰写的客座博文，原载于 2017 年 8 月 16 日*[*community.risingstack.com*](https://community.risingstack.com/repatch-the-simplified-redux/)*。*