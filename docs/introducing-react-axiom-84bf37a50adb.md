# 使用带 React 的模型

> 原文：<https://medium.com/hackernoon/introducing-react-axiom-84bf37a50adb>

![](img/a45dac7e28632ea39b17c0e200c3ce66.png)

## 引入 React 公理

*2016 年 11 月 16 日编辑反映了自本文最初发表以来 API 的变化。*

在 React 应用程序中的某个时刻，在单个组件级别描述应用程序数据的状态变化变得很困难，需要在业务逻辑和表示组件之间进行清晰的抽象。Redux 在这一点上当然是一个选项，但是假设 Redux 提供的[权衡](/@dan_abramov/you-might-not-need-redux-be46360cf367)是不利的呢？

[React Axiom](https://github.com/wgoto/react-axiom) 是一种轻量级的(~12kb)方式，通过 React 组件树来使用模型。一个基本的 React Axiom 模型如下所示:

```
class ListItemModel extends ReactAxiom.Model { static defaultState() {
    return {
      id: null,
      description: '',
      completed: false
    };
  }}
```

`Model`将 argument 对象存储在`this.state`中，并自动创建 getter 和 setter 函数:`id`属性的`getId`、`setId`、`hasId`、`description`属性的`getDescription`、`setDescription`、`hasDescription`、`completed`属性的`isCompleted`、`setCompleted`、`hasCompleted`(注意:由于 completed 属性是布尔值，所以这是不同的)。在类上定义同名的方法会覆盖 getter 或 setter:

```
class ListItemModel extends ReactAxiom.Model { static defaultState() {
    return {
      id: null,
      description: '',
      completed: false
    };
  } getDescription() {
    return this.state.description.toLowerCase();
  }}
```

当一个 React Axiom 模型被传递到一个组件中时，该组件监听模型中的状态变化并更新自己。下面是一个 React 组件的例子，它使用了下面作为`listItem`传递的模型:

```
class ListItemComponent extends React.Component { render() {
    const { listItem } = this.props;
    return (
      <li>
        {listItem.getDescription()}
        {listItem.isCompleted() ? null : this.renderButton()}
      </li>
    );
  } renderButton() {
    const { listItem } = this.props;
    return (
      <button onClick={() => listItem.setCompleted(true)}>
        complete
      </button>
    );
  }}
```

注意组件如何调用`listItem`模型上的`setCompleted`来更新状态。将所有东西放在一起:

```
const listItem = new ListItemModel({
  id: '1',
  description: 'Teach mom how to use Slack'
});const ListItemSubscriber = ReactAxiom.subscribe(ListItemComponent);ReactDOM.render(
  <ListItemSubscriber listItem={listItem} />,
  document.getElementById('app')
);
```

高阶`subscribe`函数包装`ListItemComponent`并返回一个新的`ListItemSubscriber`组件。然后，`ListItemSubscriber`组件将订阅`listItem`模型，并在状态改变时自我更新。在上面的具体例子中，点击完成按钮将导致按钮消失。

# 引用变得复杂

React Axiom 模型中的状态变化是通过突变发生的。因此，这种设计允许对其他对象、数组和模型的引用在状态中很好地操作。以下示例将一个`dependencies`字段添加到`ListItemModel`中，并添加一些额外的逻辑来完成一个列表项。

```
class ListItemComponent extends ReactAxiom.Model { static defaultState() {
    return {
      id: null,
      description: '',
      completed: false,
      dependencies: [],
    };
  } complete() {
    this.getDependencies().forEach(dependency => {
      dependency.complete();
    }); this.setCompleted(true);
  }}
```

为了递归地呈现列表项，`ListItemComponent`组件可以为每个依赖项呈现`ListItemSubscriber`组件:

```
class ListItemComponent extends React.Component { render() {
    const { listItem } = this.props;
    return (
      <li>
        {listItem.getDescription()}
        {listItem.isCompleted() ? null : this.renderButton()}
        <ul>
          {this.renderDependencies()}
        </ul>
      </li>
    );
  } renderButton() {
    const { listItem } = this.props;
    return (
      <button onClick={() => listItem.complete()}>
        complete
      </button>
    );
  } renderDependencies() {
    const { listItem } = this.props;
    return listItem.getDependencies().map(dependency => (
      <ListItemSubscriber listItem={dependency} />
    ));
  }}const ListItemSubscriber = ReactAxiom.subscribe(ListItemComponent);
```

现在，点击一个完整的按钮将会改变该模型的`completed`状态值及其所有依赖的`completed`状态值。此外，订阅任何变异列表项的任何组件都将更新并呈现正确的状态。

# 与服务器渲染一起使用

React Axiom 的一个特别的缺点是，数据存储需要将模型名称作为可传输数据的一部分进行传递。这对于从服务器到客户机的数据传输来说很好，但是如果模型名称以任何方式改变，那么在快照和将状态保存到本地存储方面就不太灵活了。下面是一个用 React Axiom `Store`序列化和解析模型和模型数据的例子:

```
const listItem1 = new ListItemModel({
  id: '1',
  description: 'Teach mom how to use Slack'
});const listItem2 = new ListItemModel({
  id: '2',
  description: 'Meditate',
  dependencies: [listItem1]
});ReactAxiom.Store.setModelRefs([ListItemModel]);const serverStore = new ReactAxiom.Store({
  title: 'Things to do',
  createdAt: Date.now(),
  listItems: [listItem1, listItem2]
});const json = serverStore.stringify();// Transfer the data to the client
// to hydrate the client store, and
// reinitialize the application.ReactAxiom.Store.setModelRefs([ListItemModel]);const clientStore = new ReactAxiom.Store();clientStore.parse(json);
```

注意`listItem2`是如何在服务器存储中包含对`listItem1`的引用的。通过传入类来初始化客户端存储，它能够从提供的 JSON 字符串重新构建这个引用。换句话说，`listItem2.getDependences()[0] === listItem1`上`clientStore`。

# 何时使用模型

像任何设计一样，也有权衡。例如，Redux 需要简单的对象来描述系统中的变化，需要纯粹的函数来描述处理这些变化的逻辑(许多开发人员称之为“样板”)，但却获得了更灵活的数据存储。React Axiom 需要模型来描述系统中的变化*和*来描述处理这些变化的逻辑，但是具有以下优点:

*   组织和清除业务逻辑变得更加容易。
*   视图、模型和数据之间的接口变得更加灵活。
*   编写语义透明的代码变得更加容易。

对于 React 生态系统的新手来说，React Axiom 可能比 Redux 更有意义，但是像任何固执己见的框架或设计一样，只有在解决特定问题或适合特定情况时才使用它。

# 下一步是什么？

React Axiom 仍处于起步阶段。我欢迎任何帮助，让这个设计范例成为 JavaScript 社区大规模应用程序的健壮解决方案。请随时通过查看 GitHub 库或通过 Twitter 联系我[参与进来。🙏🏽](https://twitter.com/wrgoto)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)