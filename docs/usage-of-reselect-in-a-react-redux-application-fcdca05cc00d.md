# 在 React-Redux 应用程序中使用重选

> 原文：<https://medium.com/hackernoon/usage-of-reselect-in-a-react-redux-application-fcdca05cc00d>

![](img/2e8c937315bfbe928ed3fbe220a21680.png)

# **为什么重选这么好**

Reselect 是一个流行的库，它提供了一种从 React-Redux 应用程序的存储中获取值的便捷方式。它的优点是它的记忆能力。您可以在文档中阅读所有这些内容。换句话说，当您使用 *createSelector()* 函数时，它会记忆每个输入选择器的输出，并且仅当任何输入选择器改变其输出时才重新计算结果值。这里需要注意的重要一点是，reselect 使用引用等式(===)来确定值的变化。

作为使用记忆化的动机，文档建议提高性能，因为每次调用的重新计算可能非常昂贵。但是我们将在本文中看到，在 React-Redux 应用程序中，使用内存化的选择器有时是唯一的方法，即使计算非常便宜并且不影响性能。

# **React-Redux 连通分量如何工作**

首先，我们来看看一个 React-Redux 应用程序是如何工作的。Redux 所做的本质上是为我们的应用程序状态提供一个商店，并提供与商店通信的方法。其中一种方法是 *connect()* 函数。在定制组件上调用 *connect()* 之后，您会得到一个包装器，它将状态作为道具从存储传递到您的组件。这通过每次状态改变时调用的 *mapStateToProps()* 函数来实现。

在 *mapStateToProps()* 产生重新计算的属性后，新属性比旧属性浅，如果它们不同，组件将被重新渲染。再次使用引用等式(===)来比较道具。

# 一个没有记忆的选择器会毁了你的一天

这里我们可以利用一个例子。让我们创建一个名为商品列表的应用程序。app 将基于[**react-boilerplate**](https://github.com/react-boilerplate/react-boilerplate)具有不可变状态(参见 [**Immutable.js**](https://facebook.github.io/immutable-js/) )。

我们为我们的应用程序定义了一个状态:

```
import {SET_GOODS, SET_SORTED, COUNT} from 'constants/index';
import {fromJS} from 'immutable';const initialState = fromJS({
goods: [
 {
  name: 'tomatoes',
  price: 3,
 },
 {
  name: 'potatoes',
  price: 2,
 },
 {
  name: 'cucumbers',
  price: 5,
 },
 {
  name: 'salad',
  price: 1,
 }
],
sorted: false,
});export default (*state* = initialState, *action*) => {
switch (*action*.type) {
 case SET_SORTED: {
  return *state*.set('sorted', *action*.sorted);
 }
 default: {
  return initialState;
 }
}
}
```

和几个组件:

```
class GoodsList extends React.Component {
render () {
 return (
  <**div**>
   <**ul**>
   {this.props.goods.map((*g*, *i*) =>
   <**li** key={*i*}>{`${*g*.get('name')} - ${*g*.get('price')}$`}</**li**>)}
   </**ul**>
  </**div**>
 )
}
}const mapStateToProps = (*state*) => {
return {
 goods: getGoods(*state*),
};
}const mapDispatchToProps = (*dispatch*) => bindActionCreators({
count,
}, *dispatch* ;export default connect(mapStateToProps, mapDispatchToProps)(GoodsList);
```

```
class Buttons extends React.Component {
render () {
 return (
  <**div** style={{display: 'flex'}}>
   <**button** style={buttonStyle}
   onClick={() => this.props.setSorted(true)}>
     **Show Sorted** </**button**>
    <**button** style={buttonStyle}
    onClick={() => this.props.setSorted(false)}>
      **Show Unsorted** </**button**>
  </**div**>
 )
}
}const mapStateToProps = (*state*) => {
return {}
}const mapDispatchToProps = (*dispatch*) => bindActionCreators({
setSorted,
}, *dispatch*);export default connect(mapStateToProps, mapDispatchToProps)(Buttons);
```

我们还有一个包含我们组件的页面:

```
 export default class HomePage extends React.PureComponent {
 render() {
  return (
   <**div**>
    <**GoodsList**/>
    <**Buttons**/>
   </**div**>
  );
}
}
```

该应用程序只呈现商品列表，要么按价格排序(当用户单击“显示排序”按钮时)，要么未排序(默认情况下，当用户单击“显示未排序”按钮时)。在状态中，我们有*商品*和*排序*商品，前者是我们想要显示的商品列表，后者告诉我们该列表是应该排序还是不排序。

我们现在唯一需要做的就是定义一个选择器。让我们首先尝试一个未记忆的选择器。基本上，这只是一个函数，就像这样:

```
 export const getGoods = (*state*) => {
 const list = *state*.getIn(['main', 'goods']);
 const sorted = *state*.getIn(['main', 'sorted']); return sorted ? list.sort((*a*, *b*) => {
   const aPrice = *a*.get('price');
   const bPrice = *b*.get('price');
   if (aPrice < bPrice) { return -1; }
   if (aPrice > bPrice) { return 1; }
   if (aPrice === bPrice) { return 0; }
}) : list;
}
```

根据排序后的的*的值，该选择器要么只返回该州的商品列表，要么在返回之前按价格排序。让我们仔细看看这里发生了什么。不可变的. js 文档说:“sort()总是返回一个新的实例，即使原始实例已经被排序了”。这意味着，我们排序后的商品列表将永远不会与之前排序的商品列表相同。GoodsList 组件的*商品*属性的引用等式将永远不成立，这意味着该组件将在每次状态更改时重新呈现，即使这种更改不会以任何方式影响商品列表。*

虽然这显然是一种错误的创建选择器的方式，但是到目前为止看起来并没有什么大不了的。对于我们只有四个条目的列表，选择器中的计算非常便宜，不会有任何明显的性能下降。但是如果我们需要改变组件生命周期中的状态呢？正如 [**React 文档**](https://facebook.github.io/react/docs/react-component.html#componentwillreceiveprops) 所建议的，我们将在 componentWillReceiveProps 方法中完成。比方说，出于某种原因，我们需要计算 GoodsList 收到道具的次数。因此，我们将 componentWillReceiveProps 添加到 GoodsList 组件:

```
componentWillReceiveProps = (*nextProps*) => {
this.*props*.count();
}
```

这里的 *count* 是一个发送到 store 的动作。

减速器看起来像这样:

```
import {SET_GOODS, SET_SORTED, COUNT} from 'constants/index';
import {fromJS} from 'immutable';const initialState = fromJS({
goods: [...],
sorted: false,
count: 0,
});export default (*state* = initialState, *action*) => {
switch (*action*.type) {
 case SET_SORTED: {
   return *state*.set('sorted', *action*.sorted);
 }
 case COUNT: {
   return *state*.set('count', *state*.get('count') + 1);
 }
 default: {
   return initialState;
 }
}
}
```

当用户单击“显示已排序”时，将触发以下操作序列:

1.  *值为*真*的 SET_SORTED* 动作被分派到存储。
2.  排序后的*的值*在状态下改变。
3.  *GoodsList* 连接组件调用 *mapStateToProps* 。
4.  选择器 *getGoods* 从 *mapStateToProps* 中调用。
5.  选择器返回排序后的列表，该列表不等于已经传递给 *GoodsList* 的列表。
6.  当 *mapStateToProps* 返回的属性不浅等于组件先前的属性时，React 开始 *GoodsList* 重新渲染。
7.  *GoodsList* 生命周期方法*componentWillReceiveProps*被调用。
8.  动作*计数*被分派到商店。
9.  计数*的值*在状态下改变。
10.  再次重复第 3-9 点。
11.  然后再来一次。
12.  然后…嗯，永远不会停止。我们有一个无尽的循环。

这种对收到的道具进行计数的情况可能看起来有点虚假，但这可能发生在现实生活中。例如，当你为一个[](http://redux-form.com/)**设置初始值时，一个动作被分派到 store。或者，如果您需要在状态中存储路由参数，您可以将它们设置为一个对象，这将导致状态发生变化，您可能会在 componentWillReceiveProps()中这样做，因为路由可能会在不卸载组件的情况下发生变化。这两种情况都有效地表现为好像您在 componentWillReceiveProps()中计算收到的属性。**

# ****重选可能对你有帮助也可能没有****

**我们可以很容易地通过重新选择来解决这个问题。这个选择器在我们的例子中工作得很好:**

```
import {createSelector} from 'reselect';const getList = (*state*) => *state*.getIn(['main', 'goods']);
const getSorted = (*state*) => *state*.getIn(['main', 'sorted']);export const getGoods = createSelector(
getList,
getSorted,
(*list*, *sorted*) => {
 return *sorted* ? *list*.sort((*a*, *b*) => {
    const aPrice = *a*.get('price');
    const bPrice = *b*.get('price');
    if (aPrice < bPrice) { return -1; }
    if (aPrice > bPrice) { return 1; }
    if (aPrice === bPrice) { return 0; }
  }) : *list*;
}
)
```

**这里，在 *getList* 或 *getSorted* 改变它们在 *COUNT* 动作中没有的值之前，不会调用转换函数。相反， *getGoods* 选择器只返回先前计算的值，该值显然等于已经传递给 *GoodsList* 组件的列表。React 不会尝试第二次重新呈现组件，循环会中断。**

**然而，有一个危险:很容易意外地使重选选择器失去记忆。**

**例如，出于某种原因，您可能希望在组件中使用 JavaScript 数组，而不是不可变列表。但是该选择器将再次导致在“显示已排序的”点击时开始无休止的循环:**

```
import {createSelector} from 'reselect';const getList = (*state*) => *state*.getIn(['main', 'goods']).toJS();
const getSorted = (*state*) => *state*.getIn(['main', 'sorted']);export const getGoods = createSelector(
getList,
getSorted,
(*list*, *sorted*) => {
 return *sorted* ? *list*.sort((*a*, *b*) => *a*.price - *b*.price) : *list*;
}
)
```

**虽然 *getGoods* 在这里是一个记忆化的选择器，但它每次都从 getList 获得不同的输入。一般来说，从状态中获取值的选择器不应该做任何其他事情，因为它们没有被记忆。**

**另一个可能的错误是咖喱选择器。有时您希望创建这样的选择器，以防将来需要将参数传递给选择器:**

```
export const getGoods = () => createSelector(...)
```

**这样写 *mapStateToProps* 很有诱惑力:**

```
const mapStateToProps = (*state*) => {
return {
  goods: getGoods()(*state*),
};
}
```

**但是这里我们在每次调用 *mapStateToProps* 时创建一个新的选择器实例。基本上，我们只是放弃了选择器的记忆能力，因为每个新实例都会重新计算它的值，而这个值不等于另一个实例计算的值。**

**这种类型的 bug 非常烦人，因为当你创建了一个 bug，并在以后的多次提交中偶然发现它时，你通常不会注意到。谢天谢地， *git 平分*可以帮你找到哪里出了问题。**

# ****让你的生活更美好的小贴士****

**总而言之，我将指定我在使用 Reselect 库时为自己获得的一些技巧。**

1.  **如果它以任何方式从状态转换值，总是使用 Reselect 中的 *createSelector()* 来创建选择器。**
2.  **避免咖喱选择器。一般来说，你不需要它们。您可以使用选择器的第二个参数传递您需要的所有参数。事实上，我能想到的唯一需要 curried 选择器的情况是在 [**文档**](https://github.com/reactjs/reselect#sharing-selectors-with-props-across-multiple-components) **中描述的。****
3.  **从生命周期功能向商店分派动作时要小心。一般来说，避免这样做。但如果你不得不，也许在派遣一个动作之前手动比较道具是个好主意。**

**不要使用`state => state`或`state => state.get(‘main’)`这样的选择器作为输入选择器。如果您访问状态的大部分，这些部分很可能会随着每个动作而改变。如果你真的需要，你可能不得不自己记忆转换函数。这可以通过使用来自 [**Lodash**](https://lodash.com/docs/4.17.4#memoize) 或 [**Ramda**](http://ramdajs.com/docs/#memoize) 或其他东西的 *memoize()* 来完成。**

**此外，如果你想使用选择器并模拟一些其他情况，你可以在 GitHub 上找到我在这里使用的例子。**

**[](https://hackernoon.com/missing-part-of-redux-saga-experience-1d2d169ba765) [## Redux Saga 体验中缺失的部分

### Redux saga 是应用程序和 redux store 之间的中间件，由 redux actions 处理。这意味着，它可以…

hackernoon.com](https://hackernoon.com/missing-part-of-redux-saga-experience-1d2d169ba765) [](https://hackernoon.com/using-normalizr-to-organize-data-in-stores-practical-guide-82fa061b60fb) [## 使用 Normalizr 组织商店中的数据——实用指南

### 在对 Normalizr 的工作结果应用一些简单的操作之后，我们得到了可以存储的数据

hackernoon.com](https://hackernoon.com/using-normalizr-to-organize-data-in-stores-practical-guide-82fa061b60fb) [](https://hackernoon.com/using-normalizr-to-organize-data-in-store-part-2-d9646133b7df) [## 使用 Normalizr 组织存储中的数据。第二部分

### 文章的第二部分讲述了如何使用 Normalizr 来组织商店中的数据。

hackernoon.com](https://hackernoon.com/using-normalizr-to-organize-data-in-store-part-2-d9646133b7df) [](https://hackernoon.com/how-to-stop-using-callbacks-and-start-living-1e5ed92e68e8) [## 如何停止使用回调，开始生活

### Javascript 有两种处理异步任务的主要方式——回调和承诺。一般来说，承诺是…

hackernoon.com](https://hackernoon.com/how-to-stop-using-callbacks-and-start-living-1e5ed92e68e8) 

作者: [Ilya Bohaslauchyk](https://github.com/iPhaeton)**