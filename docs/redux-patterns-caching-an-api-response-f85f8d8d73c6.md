# Redux 模式:缓存 API 响应

> 原文：<https://medium.com/hackernoon/redux-patterns-caching-an-api-response-f85f8d8d73c6>

## 避免重复调用稳定的数据

我目前正在尝试将 [Contentful](https://www.contentful.com/) 作为一项服务来提供可以编辑和更新的数据，而不会引发一个全新的测试/部署周期。因为我将 Redux 存储联合到本地存储，所以我试图找出一种模式，在这种模式下，我不会每隔[x]多次重新请求该数据，其中[x]可以是从毫秒到天的任何时间限制。

出于这个例子的目的，我使用 [Redux-Thunk](https://github.com/gaearon/redux-thunk) 来允许我做异步动作。

# 行动

第一部分是我的 reducer 中允许的动作。

*   **中止**:如果缓存是“新”的，我们将运行的动作
*   **错误**:存储错误以备不时之需(这个动作可以是连接外部日志的钩子)
*   **Start** :将`isFetching`设置为`true`，用于异步进程的任何类型的用户通知。
*   **更新**:将返回的数据设置为值。

# 同步动作创建者

动作创建者是将在异步动作的生命周期中被分派的函数。

# 异步操作创建者

这个函数将被导出到一个组件，并从一个生命周期挂钩中调用，无论何时需要启动获取。内部动作将在异步调用的生命周期中按顺序分派。

# 减速器和初始状态

reducer 处理异步函数生命周期中调度的所有同步动作，以及设置`isFetching`和`lastFetched`值。您可以很容易地将这一点逻辑转移到动作创建者，并避免在您的 reducer(纯函数)中产生任何副作用。

# 结论

这就是了。假设您正在将 Redux 存储联合到本地存储，如果您有更好的模式(或者知道一个更好地解决这个问题的库),您可以避免一堆不必要的 API 请求。

我知道事实上在要点之间有一两个错别字，但它们不会模糊逻辑。

## 相关职位

 [## 冗余模式:反思“byId”和“byHash”结构

### 迭代 Object.keys()并删除 byId

medium.com](/@justintulk/redux-patterns-rethinking-byid-and-byhash-structures-854e8a0fa32d)  [## Redux 模式:添加/编辑/删除数组中的对象

### 前几天我写了一篇关于如何在单个 React 组件中实现 Redux 风格的存储和缩减器的帖子…

medium.com](/@justintulk/redux-patterns-add-edit-remove-objects-in-an-array-6ee70cab2456)  [## 在单个 React 组件中将 Redux 实现为普通 JS

### 我目前正在做一个项目，将一些 React 组件一点一点地修补到现有的 ASP.NET 版本中…

medium.com](/@justintulk/implement-redux-as-vanilla-js-in-a-single-react-component-1492e9f06472)