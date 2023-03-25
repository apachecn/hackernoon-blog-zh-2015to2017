# 冗余模式:反思“byId”和“byHash”结构

> 原文：<https://medium.com/hackernoon/redux-patterns-rethinking-byid-and-byhash-structures-854e8a0fa32d>

## 迭代 Object.keys()并删除 byId

在之前的一篇文章中(这是我阅读量最大的一篇文章)，我描述了一种在 Redux 存储中管理数据的模式，在 Redux 存储中，使用对象的 id 作为键对对象列表进行散列，然后在散列的旁边维护这些 id 的数组。

 [## Redux 模式:添加/编辑/删除数组中的对象

### 前几天我写了一篇关于如何在单个 React 组件中实现 Redux 风格的存储和缩减器的帖子…

hackernoon.com](https://hackernoon.com/redux-patterns-add-edit-remove-objects-in-an-array-6ee70cab2456) 

结构看起来像这样:

```
const reduxStore = {
  data: {
    byId: ['a', 'b'],
    byHash: {
      a: {someKey: "someValue", id: "a"},
      b: {someKey: "someOtherValue", id: "b"}
    }
  }
}
```

任何改变这个数据结构的动作都将被处理两次:一次是添加/删除`byId`数组中的任何键，另一次是添加/删除/更新存储在`byHash`散列中的相关数据。然而，现在我已经使用这个结构好几个月了，我发现大多数时候我都不用`byId`数组。所以我的结构是:

```
const reduxStore = {
  data: {
    a: {someKey: "someValue", id: "a"},
    b: {someKey: "someOtherValue", id: "b"}
  }
}
```

为什么我会掉`byId`？

## 优点:

*   处理动作更简单，因为我只需更新一个数据结构来响应最常见的动作。
*   Redux 存储对象具有较少嵌套
*   使用`Object.keys(data).forEach`很容易实现迭代，或者在 React 应用程序中更常见的是:`Object.keys(data).map`。
*   长度随时可用`Object.keys(data).length`。

## 预期缺点:

*   没有吗？(我错过了什么？)

## 边缘案例缺点:

*   如果我的散列变得出乎意料的大，必须不断地计算长度，而不是直接从数组中取出长度，这可能是很昂贵的。实际上，我的散列通常只有不到几十个对象按键存储，所以维护`byId`属性的负担花费了更多的时间(并且让我编写更多的测试)。

# 结果，我到处都在使用这种模式

最典型的用例是我经常需要迭代这个数据结构(forEach)或者做一些类似函数的操作(map/filter/reduce)。这很容易实现:

1.  呈现 React 组件列表

```
{Object.keys(this.props.data)
  .map(key => {
    // operate on the full value since `key` is just the key
    **const renderData = this.props.data[key];**     return <div>{renderData.someValue}</div>
  })
}
```

2.基于每个对象中的一些值进行过滤

```
{Object.keys(this.props.data)
  .filter(key => {
    // again, operate on the full value, not the key
    return **this.props.data[key].value** === condition;
  })
  .map( ....)
}
```

您可以一遍又一遍地重复使用这个基本模式。想按对象中的值排序吗？`Object.keys(data).sort((a,b) => {})`如果列表长度为零，想要避免显示某些数据吗？所有的问题都由相同的基本代码解决。

当然这有点多余，但这并不比:

```
{data.byId.map(id => {
  const renderData = data.byHash[id];
  return <div>{renderData.someValue}</div>
})}
```

# 性能考虑因素

感谢[马克·埃里克森](https://medium.com/u/846abfc7bfa9?source=post_page-----854e8a0fa32d--------------------------------)对[的精彩评论](/@mark.erikson/i-do-have-a-few-thoughts-here-afa330de153a)将过滤逻辑转移到渲染周期中的一些性能影响。

> 虽然在`mapState`函数和组件的`render`方法中应该做什么没有严格的规则，但是我的一般建议是`mapState`应该负责形成组件实际需要的数据。这与这样一个事实相联系，即连接更多的组件通常会导致更好的性能，并且最小化给定的已连接组件从存储中需要的数据量将意味着它将更少地重新呈现。所以，我的方法通常是在`mapState`级别应用过滤和排序类型的行为，这样组件只得到它实际需要渲染的数据。
> 
> 此外，如果`mapState`从一个调用到另一个调用返回完全相同的值，那么`connect`将跳过重新渲染普通组件，这通常是一种性能改进。

[我将深入研究记忆](/@justintulk/reselect-style-memoization-in-3-functions-aff30f8cba11)并在将来的某个时候更新，但现在我将确保在我的 Redux 选择器中尽可能多地处理这些。一个简单的例子:

```
render() {
  return (
    <div>{Object.keys(this.props.data).map(key => {
      const val = this.props.data[key]
      return (<span>{val.text}</span>)
    })</div>
  )
}const mapStateToProps = state => ({ data: state.myData })// could easily become this to clean up logic:render() {
  return (
    <div>{this.props.data.map(val => (
      <span>{val.text}</span>
    ))}</div>
  )
}const mapStateToProps = state => ({ 
  data: Object.keys(state.myData).map(key => state.myData[key])
})
```

您甚至可以在选择器中重新创建`byId`和`byHash`模式，以获得其好处，而不必在您的商店中维护一个并行数组。

```
const mapStateToProps = state => {
  const myKeys = Object.keys(state.myData) return {
    byId: myKeys,
    byHash: myKeys.map(key => state.myData[key])
  }
}
```

像往常一样，如果您不同意，或者您看到了我忽略的东西，请留言。

[](/@justintulk/reselect-style-memoization-in-3-functions-aff30f8cba11) [## 3 个函数中的重选式记忆

### 我一直在通读 Reselect 的源代码(只有 107 行没有被修改)，我认为它可能值得拆包…

medium.com](/@justintulk/reselect-style-memoization-in-3-functions-aff30f8cba11)