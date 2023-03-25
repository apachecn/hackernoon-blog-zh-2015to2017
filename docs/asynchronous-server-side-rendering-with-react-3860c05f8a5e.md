# 使用 React 的异步服务器端渲染

> 原文：<https://medium.com/hackernoon/asynchronous-server-side-rendering-with-react-3860c05f8a5e>

> 我会尽量简短:这篇文章将介绍 React 应用程序中异步服务器端呈现的实现。我将使用 React-Router、Mobx 和 Koa。应用程序代码过于简化，应该是最小的——这只是一个演示。

## 为什么这是一件事？

React 和 React-路由器自带开箱即用的服务器端渲染。不幸的是，它们不支持异步渲染。这意味着您的路径将在服务器上处理，并且您的视图将被渲染，但是如果您需要加载任何不在服务器上的数据(例如，来自数据库)，则在服务器进行渲染时这些数据将不可用。

## 什么时候需要这个？

每当您的应用程序在页面最初加载之前需要一些动态内容(例如来自 API 的数据)时。在我们的例子中，我们将有一个显示漫画人物信息的页面。这些信息来自维基百科的 API。如果该页面是在脸书上共享的，脸书爬虫会期望关于该角色的数据出现在它从服务器得到的初始响应中。如果动态数据只呈现在客户端——它将从我们的脸书邮报中消失。

![](img/fe8dca83b2b2a93f9a1b4403a7d3fab7.png)

Synchronous Rendering ;)

# 让我们开始吧

> 你可以在[https://github . com/avnersorek/react-async-server-side-rendering](https://github.com/avnersorek/react-async-server-side-rendering)中找到所有这些代码。我将包含一些代码片段，但是我建议使用到存储库的链接来获得更好的视图。

我们的示例应用程序将有两个视图——根视图('/')显示斯坦·李创作的漫画人物列表，另一个视图('/characters/:id ')显示单个人物的维基百科摘要。让我们看看当我们请求一个特定的字符时会发生什么:

## 第一站:React-路由器

我们在流程中遇到的第一个地方是[src/app . server . js # render app](https://github.com/avnersorek/react-async-server-side-rendering/blob/master/src/app.server.js#L10)。我们称 *matchRoutes —* 只是 React-Router 的 *match* 方法包装在一个承诺中(回调是 *so* 2014)。我们让 React-Router 施展它的魔法——它会根据路线找到需要渲染的组件。我们将获得一个 *props* 对象，其中包含将要呈现的内容的信息。

## 在服务器上设置应用程序

props 对象转到 [initAndRender](https://github.com/avnersorek/react-async-server-side-rendering/blob/master/src/app.server.js#L34) 方法。让我们来分解一下:

```
function* initAndRender(props) {  
  const appStore = yield initStore(props);
  const initialState = serializeStore(appStore);
  const app = shared.injectStores(<RouterContext {...props} />, appStore);
  const componentHTML = renderToString(app);
  return renderIndex(componentHTML, initialState);
}
```

## initStore(道具)

```
function* initStore(props) {
  const appStore = new AppStore(); yield props.components
    .filter(Boolean)
    .map(component => component.wrappedComponent || component)
    .filter(component => component.preServerRender)
    .map(component => component.preServerRender(appStore, props.params)); return appStore;
}
```

这被称为 *yield* ,因为——正如这个帖子的名字所暗示的——这将是一个异步操作。这将创建一个新的*应用商店*对象——商店在这个阶段是空的。然后我们将检查道具对象中的组件。这些是 React-Router 发现需要渲染的组件(*它们是组件的类而不是实例*)。我们将在每个组件上调用静态方法*preserver render*(*这是我编的——不是内置的 React 组件生命周期方法*)。这是每个组件在服务器上呈现之前说明它需要什么的地方。我们在[人物组件](https://github.com/avnersorek/react-async-server-side-rendering/blob/master/src/components/Character.jsx#L7)上看一下:

```
static preServerRender(appStore, params) {
  return appStore.loadCharacter(params.pageId);  
}
```

因此，角色组件需要我们的 *appStore* 在渲染之前加载角色。我们还得到路线参数，所以我们知道使用哪个字符 ID。这个方法需要返回一个承诺，所以我们会知道它什么时候完成加载资源。在幕后，这将对维基百科的 API 执行一个 GET 请求。

# 从现在开始同步

就是这样！这是整个异步的东西。我将很快介绍其余部分，但这是“标准”的 React 服务器端渲染:

```
const initialState = serializeStore(appStore);
```

将序列化状态。为了让我们的客户端应用程序以服务器结束时的状态开始，它需要被序列化为 JSON 并植入到服务器发送的响应中。那个[发生在这里](https://github.com/avnersorek/react-async-server-side-rendering/blob/master/src/app.server.js#L67)。接下来，我们渲染视图:

```
const app = shared.injectStores(<RouterContext {...props} />, appStore);
const componentHTML = renderToString(app);
```

将状态发送到客户端应用程序是不够的。我们还希望我们的初始视图(HTML)根据这种状态呈现。[s*hared . injectstores*](https://github.com/avnersorek/react-async-server-side-rendering/blob/master/src/app.shared.js#L5)*只是客户端和服务器端共享的一些代码，利用 Mobx 的提供者将 appStore 注入到我们的组件实例中。renderToString 只是 React-DOM 做它该做的事情。*

```
*return renderIndex(componentHTML, initialState);*
```

*现在，我们获取渲染视图(HTML)和序列化状态(JSON)，并将它们注入我们的 index.html(T25)。如果你有一个更大的 index.html 文件，我推荐使用 ejs 或 jade 来完成这一部分。*

# *别忘了客户*

*在我们的初始渲染之后，客户端应用程序接管。我们需要在客户端用初始状态初始化我们的*app store*—[这发生在 src/app.client.js](https://github.com/avnersorek/react-async-server-side-rendering/blob/master/src/app.client.js#L9) 中:*

```
*const initialState = global.window.__INITIAL_STATE__;
const appStore = new AppStore(initialState);*
```

*React 会知道从那里拿(那其实很神奇)。如果您从服务器获得的状态和 HTML 不一致，您甚至会在控制台中得到警告。*

## *从现在开始的客户*

*用户与应用程序的其余交互将在客户端完成。[角色组件将使用 componentidmount](https://github.com/avnersorek/react-async-server-side-rendering/blob/master/src/components/Character.jsx#L11)React 生命周期挂钩来获取下一个角色:*

```
*componentDidMount() {
  const { appStore, params } = this.props;
  appStore.loadCharacter(params.pageId);
}*
```

*这与渲染并行发生，所以不要忘记[支持组件中的加载状态](https://github.com/avnersorek/react-async-server-side-rendering/blob/master/src/components/Character.jsx#L29)。*

> *就是这样。我真的没有深究，我试着让这篇文章简短。因此，如果你有任何问题，请在这里发表评论。谢谢！*

> *编辑:
> *这适用于嵌套路线，但不适用于嵌套在组件内的组件。这是因为只有当调用* Component.render *时，这些才会发挥作用。这可以通过两次渲染来处理，如*[*react-async-render*](https://www.npmjs.com/package/react-async-render)*中所述。**

*[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)**[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)**[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*

*[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)*