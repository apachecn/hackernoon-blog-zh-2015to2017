# Reactjs 和 Ajax

> 原文：<https://medium.com/hackernoon/reactjs-and-ajax-7cc63f1c8d10>

![](img/89f0c4c9db41d48be2bdfb3d6d5d1fb2.png)

React 是一个视图部件库。它适用于多姆。我使用 React/Redux，因为 Redux 为处理状态提供了很好的帮助。当开发人员使用视图部件时，他们需要从服务器获取数据。Ajax 是用于 Javascript 和服务器数据交换的技术。React 还没有针对 Ajax 的工具。

所以…我们可以使用普通的 Ajax，或者我们可以下载 Ajax 模块。React 有两个更好的 Ajax 模块:Axios 和 Fetch。我和 Fetch 一起工作。它非常简单，为老版本的浏览器提供了聚合填充。

我写了 ir 以前的[文章](/@audruis/first-app-with-react-redux-9e71498979af)关于 React 和 Redux。当我们使用 React/Redux 和 Fetch(或 Axios)时，我们必须改变动作。

在这个例子中，我在动作文件中调用 dispatch。Fetch 向服务器发送数据并获得响应。Promise 调用 dispatch 并将数据发送给 receivePermission 函数。如果我们使用 Fetch，我们必须知道——Ajax 是异步技术，需要不同的工作方式。