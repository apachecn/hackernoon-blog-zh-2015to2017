# 你不需要知道“依赖注入”

> 原文：<https://medium.com/hackernoon/you-dont-need-to-know-dependency-injection-2e9d2ba1978a>

![](img/bd8f691bfd3626087313b5a6cb340304.png)

我最近参加了一个前端职位的面试，一位同事问应聘者——“你对依赖注入了解多少？”。

我有一种感觉，这可能是一个“抓到你了”的问题，但当时我不太清楚为什么。从那以后，我们花了一些时间来讨论和解释为什么这不是一个问前端开发人员的好问题。我也将为那些在面试中遇到这个问题的人提供一些建议。

那么，什么是依赖注入呢？

Martin Fowler 最初在他 2004 年的文章“[Inversion of Control Containers and Dependency Injection pattern](https://martinfowler.com/articles/injection.html)”中创造了这个短语，用来解释 Java 社区中围绕控制反转(IoC)概念开发的模式，以及它如何适用于其他面向对象的环境。归结起来就是——尽可能地允许任何与模块功能无关的东西成为可插拔的，而不是将特定的实现内部化(从而紧密耦合)。

这个概念是通过 Angular 引入前端的，在库开源的时候，Angular 的创建者 miko he very 正在 Google(很大程度上是一个 Java 商店)工作。

那时的前端场景看起来就像挥舞着 jQuery 和 MooTools 的野蛮人。Angular 像 Alexander 一样横扫亚洲部落，带来了围绕“控制反转”和“依赖注入”的讨论浪潮。

但是在前端开发的现代，所有的框架都集中在“组件化”上，作为分离和连接复杂部分的单一解决方案，“依赖注入”现在是标准的操作过程。

每一个通过 props 传入的函数都是“一个依赖项的注入”，每一个声明一个处理事件或用户交互的 prop 的组件都在观察“控制反转”。这些借用的术语现在已经因为无处不在而变得过时了，也许是为了给经历过 Angular 时代的 JavaScripters 人员用作术语。

也就是说，如果你发现自己在一个面试房间里，面试官问你说一些依赖注入的事情，这里有一些关于它如何与现代前端框架相关的事情，至少会引起注意

> “依赖注入有三种主要类型[……]如果你在当前关于控制反转的讨论中读到这种东西，你会听到它们被称为类型 1 IoC(接口注入)、类型 2 IoC(设置器注入)和类型 3 IoC(构造器注入)。我发现数字名称很难记住，这就是我在这里使用这些名称的原因。”— [马丁·福勒](https://martinfowler.com/articles/injection.html)

数字名称确实很难记住，但在被问到依赖注入的面试中，适度地使用一些行话可能不是一个坏主意😉

让我们看看 React 中这三种类型的类似物是什么样子的。

## 类型 3 IoC —构造函数注入

顾名思义，这个组件将通过构造函数接收它的依赖项。下面是这样一个组件的样子:

```
class Knight extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      weapon: props.weapon
    };
  }
  render() {
    return `🐴 ${this.state.weapon}`;
  }
}
```

你会这样使用它:

```
<Knight weapon={sword} />
```

当然，你通常应该直接使用道具，而不是复制它。

## 2 型 IoC-Setter 注射

组件不需要在构造函数中设置依赖关系，而是在初始化后提供可以调用的方法，依赖关系作为参数传入。

```
class Knight extends React.Component {
  state = {};
  setWeapon = (weapon) => this.setState({ weapon });
  render() {
    return `🐴 ${this.state.weapon}`;
  }
}
```

用法:

```
componentDidMount () {
  this.knight.setWeapon(sword);
}
render () {
  return <Knight ref={c => this.knight = c} />;
}
```

同样，您几乎肯定不希望这样做，而是直接通过 props 传递依赖关系。

## 1 型 IoC——界面注射

组件的道具是它与客户端的接口，因此“必需的道具”与 React 的接口注入最为相似:

```
class Knight extends React.Component {
  static propTypes = {
    weapon: PropTypes.any.isRequired
  };
  render() {
    return `🐴 ${this.props.weapon}`;
  }
}
```

你可以简单地把它当作

```
<Knight weapon={sword} />
```

我希望这些例子能够说明依赖注入已经变得多么普遍，并在这个话题出现时为您提供一些话题。

Component + Props 模式在构造中注入依赖关系，在初始化之后继续更新(设置)依赖关系，并且可以强制依赖关系通过 Props 接口传递。这是三种模式的结合，至少在 React 中是这样。*(构造函数注入在现代 Angular 中确实仍然流行/不可避免)*

也许围绕渲染道具/功能的讨论像小孩子一样(例如，迈克尔杰克逊的《[使用渲染道具！](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce)、Donavon West 的“[函数作为子组件是一种反模式](http://americanexpress.io/faccs-are-an-antipattern/)”，而我自己的“[解决高阶组件的问题……](https://hackernoon.com/solving-the-problems-of-higher-order-components-without-throwing-the-baby-out-with-the-bathwater-40ddc72df5aa)”)可以被视为这种对话的延伸，其中甚至组件的渲染函数也是一种可以被注入的依赖。像[降档](https://www.npmjs.com/package/downshift)这样的库集中体现了这种哲学的逻辑结论。但是我怀疑那些文章和库的作者是否需要关心那个特定的术语。

# 感谢阅读！

非常感谢来自[杰弗里·伯特](https://twitter.com/jephuff)和[亚历克斯·威尔默](https://medium.com/u/81bc57426c80?source=post_page-----2e9d2ba1978a--------------------------------)的反馈和编辑笔记

你可以在推特上找到我 [@CheapSteak](https://twitter.com/cheapsteak) 。

📬订阅我的时事通讯以便在文章发表前收到草稿。

本文由我的爱好项目资助-[](https://npmcharts.com/)****📈****

**这是 [react-apollo 和 react-relay](https://npmcharts.com/compare/react-apollo,react-relay) 的交互下载直方图**