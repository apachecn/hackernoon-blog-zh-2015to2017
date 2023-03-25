# React + Typescript + RxJS

> 原文：<https://medium.com/hackernoon/react-typescript-rxjs-f50791aa596c>

最近我一直在尝试 [React](https://hackernoon.com/tagged/react) 、 [Typescript](https://hackernoon.com/tagged/typescript) 和 RxJS，到目前为止我真的很喜欢这个结果。基本的前提是有一个单一的可观察的库，并连接它以对更新的组件作出反应。然后将所有的状态和业务逻辑推到可观察的地方。这是因为花了大量时间使用 Redux(添加了中间件)、重新组合和重新选择，然后对简单地将组件连接到可观察对象并计算派生状态所需的大量库和样板文件感到沮丧。几年前，我有幸在 Angular 1 项目中与一个非常聪明的开发人员一起工作，这个项目使用了一个简单的自主开发的 observable 库进行状态管理，在不使用大量库的情况下，一切都运行得非常好。这个例子是为了用更现代的工具重新创建那个概念。它是围绕一个待办事项列表构建的，该列表显示从服务器获取的待办事项列表，并允许用户向该列表添加新的待办事项。

**免责声明:**这种方法打破了将所有状态保存在单个存储中的冗余原则。如果你是这种方法的强烈拥护者，那么你不会喜欢这样，这很酷。我个人已经取得了很大的成功，并且发现用多个相互交互的状态存储来构建一个项目更容易。

为了将 Typescript、React 和 RxJS 集成为一个简单的 todos 列表容器，我创建了 4 个文件。

**TodosListComponent.ts**

这个文件导出了“哑”组件，它只是将道具渲染为 JSX。

```
**import** * **as** React **from** 'react';
**import** {TodosListComponentProps} **from** './TodosListComponentProps';
**import** {FormEventHandler, KeyboardEventHandler} **from** 'react';
**import** {TodoEntity} **from** 'service-entities/todos';
**import** {Key} **from** 'ts-keycode-enum';

**interface** TodosListState {
 todoText: **string**;
}

**export class** TodosListComponent **extends** React.PureComponent<TodosListComponentProps, TodosListState> { state = {
  todoText: ''
 };

 render() {
  **const** {todos} = **this**.props;

  **return** (
   <div>
    <button onClick={**this**.addTodo}>add</button>
    <input type='text' 
           value={**this**.state.todoText} 
           onChange={**this**.updateTodoText} 
           onKeyPress={**this**.handleEnter}/>
    <ol>
     {
      todos.map((todo: TodoEntity) => 
        <li key={todo.id}>{todo.text}</li>
      )
     }
    </ol>
   </div>
  );
 }

 componentDidMount() {
  **this**.props.refresh();
 }

 updateTodoText: FormEventHandler<HTMLInputElement> = 
  e => **this**.setState({todoText: e.currentTarget.value});

 addTodo = () => {
  **this**.props.addTodo({text: **this**.state.todoText});
  **this**.setState({todoText: ''});
 };

 handleEnter: KeyboardEventHandler<HTMLInputElement> = e => {
  **if** (e.which === Key.*Enter*) {
   **this**.addTodo();
  }
 }
}
```

**todos listcomponentprops . ts**

这个文件导出了定义 TodosListComponent 属性的接口，RxJS 需要完成这个接口来创建 TodosList 容器。

```
**import** {TodoEntity, CreateTodoEntity} **from** 'service-entities/todos';

**export interface** TodosListComponentProps {
 todos: TodoEntity[];
 refresh: () => **void**;
 addTodo: (todo: CreateTodoEntity) => **void**;
}
```

**TodosListStore.ts**

这是我们的存储，它将实现由 TodosListComponentProps 定义的所有属性和方法。为了存储来自服务器的 todos 列表，我们使用了来自 RxJS 的 [BehaviorSubject](http://reactivex.io/rxjs/manual/overview.html#behaviorsubject) 。当存储应用程序状态时，BehaviorSubject 给了我们两个很好的特性。我们可以使用“value”属性获得可观察流的当前值，对流的任何更改都将多播给所有订阅者(就像订阅 redux 一样)。注意，我们导出类不仅仅是为了类型信息，也是为了通过基本的依赖注入使单元测试变得容易。此外，如果我们需要多个 TodosListStore 对象，我们可以通过注入不同的依赖项来轻松创建它们。

```
**import** {todosService} **from** 'todos/TodosService';
**import** {BehaviorSubject} **from** 'rxjs/BehaviorSubject';
**import** {CreateTodoEntity, TodoEntity} **from** 'service-entities/todos';

**export class** TodosListStore {

 **constructor**(
  **readonly** todos = **new** BehaviorSubject<TodoEntity[]>([]),
  **private** service = todosService
 ) {} // fetch todos from the server and update the value of todos
 **readonly** refresh = () => 
  **this**.service.find().then((todos: TodoEntity[]) =>
    **this**.todos.next(todos)
  ); // add the todo to the server and prepend it to our todos list
  **readonly** addTodo = (todo: CreateTodoEntity) =>
   **this**.service.create(todo).then(
     (addedTodo: TodoEntity) => 
       **this**.todos.next([addedTodo, ...**this**.todos])
   );
}

**export const** todosListStore = **new** TodosListStore();
```

**todo list . ts**

该文件导出最终的容器组件`<TodosList/>`。要将 TodosListStore 连接到 TodosListComponent，我们需要做两件事。

首先，为组件需要的所有道具创建一个可观察对象。我们使用一个名为 [combineLatestObj](https://github.com/slively/fullstack-typescript/blob/master/ui/src/lib/combineLatestObj.ts) 的函数来完成这项工作，该函数基于 RxJS 函数 [combineLatest](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html#instance-method-combineLatest) 。combineLatest 操作符只需获取一个观察值列表，当其中任何一个观察值发生变化时，它会将所有观察值中的最新值作为参数发送给订阅者。

```
// combineLatest Example
const observer1 = Observable.*of('a');* const observer2 = Observable.*of(1);*combineLatest(
 observer1, 
 observer2, 
 (value1, value2) => value1 + value2
)
 .subscribe(combinedValue => console.log(combinedValue));observer1.next('b');
observer2.next(2);// a1
// b1
// b2
```

我们的函数 combineLatestObj 是同样的想法，但是我们想给它一个键映射到可观察值，并让它将键映射解析到每个可观察值的最新值。它还有一个传递静态值的特性，所以我们不需要为不变的值创建可观察值(比如在组件外部定义的函数或常数)。

注意:这也可以通过组合来创建嵌套对象

```
// combineLatestObj Example
const observer1 = Observable.*of('a');* const observer2 = Observable.*of(1);
const constantValue = 'no changes';*combineLatest({
 value1: observer1, 
 value2: observer2,
 *constantValue*
})
 .subscribe(latestObject => console.log(latestObject));observer1.next('b');
observer2.next(2);// { value1: a, value2: 1, *constantValue: 'no changes'* }
// { value1: b, value2: 1, *constantValue: 'no changes'* }
// { value1: b, value2: 2, *constantValue: 'no changes'* }
```

使用 combineLatestObj 很容易创建一个满足 TodosListComponentProps 和 todosListStore 的单个可观察对象，并且是强类型的:

```
combineLatestObj<TodosListComponentProps>(todosListStore);
```

其次，我们需要将新的 observable 连接到 TodosListComponent 来创建一个容器，就像您使用 react-redux [connect](https://github.com/reactjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options) [高阶组件](https://facebook.github.io/react/docs/higher-order-components.html)一样。为了做到这一点，我们有一个类似的称为 [withObservable](https://github.com/slively/fullstack-typescript/blob/master/ui/src/lib/withObservable.tsx) 的高阶组件，它将订阅给定的可观察对象，并且每当有更新时，可观察对象的值(而不是其他值)将被传递给给给定的组件。该函数像任何其他高阶组件一样使用，并提供强类型，以确保可观察对象和组件都满足给定的接口:

```
withObservable<TodosListComponentProps>(TodosListObservable)(TodosListComponent);
```

现在导出我们的容器就像导出这两个步骤的结果一样简单:

```
**import** {TodosListComponent} **from** 'todos/TodosList/TodosListComponent';
**import** {TodosListComponentProps} **from** 'todos/TodosList/TodosListComponentProps';
**import** {todosListStore} **from** 'todos/TodosList/TodosListStore';
**import** {withObservable} **from** 'lib/withObservable';
**import** combineLatestObj **from** 'lib/combineLatestObj';

**const** TodosListObservable = combineLatestObj<TodosListComponentProps>(todosListStore);

**export const** TodosList = withObservable<TodosListComponentProps>(TodosListObservable)(TodosListComponent);
```

我喜欢这种方法，不仅因为我过去使用过类似的模式并取得了很好的结果，而且通过使用普通的类、可观察的流和单个高阶组件，我们已经取代了 redux、redux-thunk、reselect 和 recompose。这篇文章基于一个更复杂的例子,这个例子通过添加过滤待办事项列表的功能演示了两个商店的交互。