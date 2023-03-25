# Redux 模式:添加/编辑/删除数组中的对象

> 原文：<https://medium.com/hackernoon/redux-patterns-add-edit-remove-objects-in-an-array-6ee70cab2456>

*更新*:在实践中，我越来越简化这个结构。不是因为它不好，而是因为它经常比我需要的解决方案更多。阅读更多信息:

 [## 冗余模式:反思“byId”和“byHash”结构

### 迭代 Object.keys()并删除 byId

medium.com](/@justintulk/redux-patterns-rethinking-byid-and-byhash-structures-854e8a0fa32d) 

[前几天我写了一篇文章](/@justintulk/implement-redux-as-vanilla-js-in-a-single-react-component-1492e9f06472#.n1ozvpm9d)关于如何在一个 React 组件中实现 Redux 风格的 store 和 reducer，而不实际使用 Redux。我没有在文章中写出逻辑，但是当我在 JSBin 中修改时，我使用了一种模式来更新数组中的对象，如下所示:

这种方法的问题是，随着阵列大小的增长，每次更新时映射整个阵列会导致性能下降。此外，我们必须做一些额外的工作，以确保我们不会意外地在数组中为每个对象创建多个副本。

## 哈希表

如果我们使用哈希表而不是数组来存储数据会怎么样？哈希表可用于使用键-值对将数组中的项目映射到对象，从而轻松避免重复并提高查找性能。但是，某些数组属性(如长度)将不会那么容易访问。为了绕过这个限制，我们将在`state`中保留两个属性，一个是哈希数组，另一个是每个单独对象的哈希表。

```
const initialState = {
  byId: [],
  byHash: {}
}
```

每当我们通过 reducer 添加、编辑或更新一个对象时，我们将执行两个动作:一个将更新数组(如果必要的话),另一个将更新哈希表。

很快:在大规模运行时，一个更有效的减速器。如果我们在 React 视图中使用它来构建子组件列表，我们可以利用`state`对象的两个部分来映射子组件，然后填充它们。

```
// assuming Redux passes in the state object as this.props.data{this.props.data.byId.map((item, index) => (
  <div key={index}>
    {this.props.data.byHash[item].content.title}
  </div>
)}
```

在一个实际的 Redux 示例中，可以通过组合两个独立的 reducers 来构建状态对象，以独立地作用于`state`对象的`byHash`和`byId`属性，从而简化逻辑。

完全披露:我不确定这是解决这个问题的最佳方式。如果你有更好的解决方案，或者可以链接到任何相关内容，请分享。

*注意:reducer 逻辑应该确保* `*state*` *总是作为一个新对象返回，以确保 React/Redux 中的视图更新。在我写这篇博文的时候，我没有在视图中测试这段代码，所以可能有一两个语法错误，或者可能只是一些不必要的逻辑。请随时提出改进意见。*

# 有关系的

 [## Redux 模式:缓存 API 响应

### 避免重复调用稳定的数据

medium.com](/@justintulk/redux-patterns-caching-an-api-response-f85f8d8d73c6)  [## 在单个 React 组件中将 Redux 实现为普通 JS

### 我目前正在做一个项目，将一些 React 组件一点一点地修补到现有的 ASP.NET 版本中…

medium.com](/@justintulk/implement-redux-as-vanilla-js-in-a-single-react-component-1492e9f06472)