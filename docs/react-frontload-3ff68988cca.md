# 反作用-前负荷

> 原文：<https://medium.com/hackernoon/react-frontload-3ff68988cca>

## 将来自 API 的数据请求绑定到 React 组件

> **Tl；博士**
> 
> 在 React 应用程序中，一个常见的需求是在服务器和客户端呈现从 API 动态加载到组件中的数据。
> 
> **react-frontload** 是一个库，它允许你声明性地将定制的数据加载逻辑绑定到你的组件，然后当组件在服务器和客户端上呈现时，自动触发数据加载。
> 
> 它很容易集成到您现有的 React 堆栈中，并且**不需要对您现有的数据 API** 进行任何更改。

**github**:[https://github.com/davnicwil/react-frontload](https://github.com/davnicwil/react-frontload)

**npm 包** : [反应-前负荷](https://www.npmjs.com/package/react-frontload)

请继续阅读，探索这个问题， **react-frontload** 如何解决它，并查看一些代码示例。

## 典型的使用案例

在最简单的用例中，您有一个视图组件，它显示一些 URL 的动态数据，其中包含从 API 加载数据所需的参数。

***例题***

> `www.myreactapp.co/profiles/**davnicwil**` *显示用户****davnicwil***的个人资料视图
> 
> *加载* ***davnicwil 的*** *配置文件数据，向* `api.myreactapp.co/users/**davnicwil**`发出 GET 请求

直到组件的初始呈现(包括初始呈现)的一切都是同步的。然而，数据请求是异步的。根据设计， [React](https://hackernoon.com/tagged/react) 组件渲染生命周期是同步的。一旦组件呈现开始，就无法等到数据请求解决后再继续。异步数据请求和组件呈现必须分离。

这种分离可以通过一种简单的方式实现，即通过 props 将所需的数据传递给视图组件，如果这些 props 为空，则呈现一个加载器。

***例***

```
const ProfileView = (props) => (
  props.profile
    ? <div>{props.profile.fullName}'s profile</div>
    : <div>loading..</div>
)
```

## 问题是

重要的问题是:数据请求何时被激发？

**在客户端**上，数据请求什么时候触发并不重要。它可以在组件的初始渲染之前或之后发生。重要的是，当请求被解析时，结果被输入到组件的 props 中。React 将重新渲染组件，这样就完成了。在客户端，组件可以被认为是长期存在的状态机，只要它们的属性被更新，就会无限期地等待重新渲染。

***例子***

```
class ProfileContainer extends React.Component { componentDidMount () {
    // asynchronously load the profile 
    // into this.props.boundProfile
    // via your state management solution of choice,   
    getProfileAsync(this.props.username)
  } render () {
    // this.props.boundProfile will at some point
    // contain the loaded profile. For the initial
    // render it will be undefined
    return (
      <ProfileView profile={this.props.boundProfile} />
    )
  }}
```

在服务器上，顺序很重要。这是因为对于每个单独的页面请求，我们会创建相同的视图组件，使用您提供的任何初始属性呈现一次，然后丢弃。任何通过 props 传递的数据都必须在这个渲染发生之前加载。在服务器上，组件最好被认为是一个纯函数，而不是一个长期存在的状态机。

***举例***

```
const handleServerPageRequest = async (req, res) => { 
  // data request must happen first.
  // wait until it's done before continuing
  const profile = await getProfileAync(req.body.username) // render component passing loaded data via props
  // note that this is a plain synchronous function call
  const markup = ReactDOM.renderToString(
    <ProfileContainer profile={profile} />
  ) // send the rendered markup in the response
  res.send(markup)
}
```

客户机和服务器的两种模式都很简单，但是需要不同的连接来获得请求和随后的组件(重新)呈现。

这两种情况的高级意图是相同的:指定一些数据依赖项将被加载到 props 中，以便组件进行呈现——在服务器上同步，在客户机上异步。

还有另一个重要的特性，上面的模式没有考虑到。当用数据在服务器上呈现一个组件时，我们可能不希望组件一在客户机上呈现就重新加载数据。我们需要组件知道它已经被服务器呈现了，然后只在更新用于数据请求的参数时重新加载数据。例如，如果 URL 中的用户名在应用内导航中发生变化，并且组件需要加载和呈现不同的用户配置文件，则用户名属性会更新。当然，我们可以在上面的模式之上实现这个特性，但是它涉及到很多容易出错的小细节，即使有状态管理解决方案的帮助。

在为我的组件一次又一次地实现这些模式的连接代码之后，我最终发现自己真的需要一个抽象。连接代码本身并不复杂，但是有很多细节，所以通常的规则适用——一旦您需要为多个组件重复它，它就不再是可管理的了。

## 一个解决方案

在使用各种模式编写一个库的几次尝试之后，我最终发现了我想要的特性集:

*   **声明性的、协同定位的、普通的 JS 数据加载逻辑:**一个组件的数据加载逻辑应该只是一个常规的 JS 函数，为 async 返回一个承诺。该功能应与组件位于同一位置。
*   **习惯用法 React:** 数据加载功能应该只通过组件 props 与应用程序的其余部分进行交互，这样它就可以很好地与堆栈其余部分中的任意库进行交互，而不需要 React 本身之外的任何隐式依赖或集成。
*   **组件封装&独立性**:不管组件放在应用程序组件树的哪个位置，组件的数据加载功能都应该是一样的。
*   **可配置组件数据加载行为**:当数据加载功能运行时，应该可以进行微调，以便处理特殊情况。默认设置应该为最常见的用例设置组件。

我将这些特性实现到一个库中: **react-frontload。**下面是使用 react-frontload 的结果，和之前的例子一样。

```
import { **frontloadConnect** } from 'react-frontload'// assuming here that getProfileAsync returns a Promise that
// resolves when the profile is loaded into props.boundProfile
const frontload = (props) => (
  getProfileAsync(props.username)
)// all available options, set to the default values
const options = {
  noServerRender: false,
  onMount: true,
  onUpdate: true
}// just decorate the same underlying component from earlier
const ProfileView =
  **frontloadConnect**(frontload, options)((props) => (
    props.profile
      ? <div>{props.profile.fullName}'s profile</div>
      : <div>loading..</div>
  ))
```

只需这几行代码，就可以在客户机和服务器上加载和呈现配置文件数据，我们还将获得前面讨论过的好特性，即在服务器呈现之后，请求不会在第一次客户机呈现时被不必要地重新触发。

剩下的就是两个微小的改变，让你的应用程序能够感知前端负载。

1.用 **Frontload** provider 包装您的应用程序

```
// before
const Application = () => (
  <div>
    {/* application content here */}
  </div>
)// after: react-frontload aware
const Application = () => (
  <Frontload>
    <div>
      {/* application content here */}
    </div>
  </Frontload>
)
```

2.在服务器上使用特殊的 react-front load**server render**函数

```
// before
const store = initStore(req)
const markup = ReactDOM.renderToString(
  <Application store={store} />
)
res.send(markup)// after: react-frontload aware
import { **serverRender** } from 'react-frontload'...const store = initStore(req)
// encapsulate exactly the same render code as above in a
// function and pass it **serverRender**
const markup = serverRender(() => ( 
  ReactDOM.renderToString(<Application store={store} />)
))
res.send(markup)
```

这就是我们所需要的。已经可以用了。

只是为了对示例中缺少的代码进行评论——即，将异步加载的概要文件绑定到 props.boundProfile，将 URL 中的用户名绑定到 props——我特意省略了这些细节，因为已经建立了库和模式，react-frontload 对您使用哪一个没有意见。

这是因为组件的 frontload 函数只通过 props 与组件交互。这是设计目标之一，希望它清楚地表明在任何现有的堆栈中使用 react-frontload 是多么简单。您可以像往常一样将这些绑定绑定到组件属性，然后可以在组件的 frontload 函数中访问它们。您不需要做任何额外的事情来让绑定工作。

## react-frontload API

`**frontloadConnect(frontload, [options])(Component)**`

**frontloadConnect** 是库和你要加载数据的组件之间的桥梁。

让我们来看一下 frontload 和 options 参数。

`**frontload(props)**`

用组件**调用的函数** props，并返回一个承诺，当所有要求的数据加载完成时，该承诺必须解决。

因此，Promise API 的全部功能和灵活性可用于将数据加载行为的异步性封装到单个返回的 Promise 中——这意味着单个请求很好，但同样地，如果需要多个请求，它们可以与`.then`链接(如果顺序重要)或与`Promise.all`并行(如果它们是独立的)。

`**[options]**`

**options** 对象提供了三种配置，它们指定了 **frontload** 函数应该在客户端和服务器端触发的确切时间。如果任何特定的配置未定义，则采用其默认值。同样，如果整个**选项**对象未定义，所有三个配置都采用默认值。

1 `**noServerRender** (boolean) [default false]`

当`false`时，**组件**的 **frontload** 函数将在每一个服务器渲染上运行。在这个例子中，这确保了组件服务器使用预期的概要文件呈现，而不是“加载”..占位符。

当`true`时，**组件**的**前端加载**功能将不会在服务器渲染上运行。在本例中，组件将由服务器使用“加载”呈现..占位符，因为没有加载配置文件。

重要的是，当这是`true`的时候，它也关闭了阻止组件在服务器渲染之后装载数据的特性。假设设置了在挂载时加载数据的选项(如下所述)，frontload 函数将在服务器渲染后的第一次挂载时触发，因为 react-frontload 知道数据没有在服务器上加载，并且“正在加载”占位符必须替换为载入的配置文件。

*注意*:如果您需要关闭整个应用程序的服务器渲染，而不是在每个单独的组件上设置此配置为真，那么 **noServerRender** 可以改为在应用程序的 **Frontload** provider 上设置为道具，如下所示:

```
<Frontload **noServerRender**> 
  <div>
    {/* app code */}
  </div>
</Frontload> 
```

2 `**onMount** (boolean) [default true]`

该选项切换当**组件**安装到客户端时 **frontload** 功能是否触发。同样，这只在**组件**被服务器渲染之前跳过——更多细节见上文。

缺省值是`true`，99%的情况下都应该是这样。毕竟，在几乎所有情况下，您都希望在第一次显示**组件**时加载并呈现数据。但是，它是作为一个选项提供的，以适应边缘情况。

3 `**onUpdate** (boolean) [default true]`

与`onMount`非常相似，这个选项切换当**组件**的道具在客户端更新时 **frontload** 功能是否应该触发。

缺省值是`true`，同样，对于一个设计良好的应用组件树来说，当属性不变时，适当限制不必要的更新(通过`shouldComponentUpdate`)很可能没问题。

对于边缘情况，或者为了消除在不可行的情况下改变触发虚假更新的组件树的需要，可以将其设置为`false`。

注意，有一种常见的情况，一些但不是所有的道具应该触发**前端加载**功能。在我们的例子中，我们总是希望在`username`更新时启动它，但是如果有另一个道具`textColor`也可以更新，但是不需要重新加载概要文件，那该怎么办呢？

我考虑过通过另一个**选项**字段进行配置，例如，通过提供属性名称，当它们更新时触发 **frontload** 函数。但是我意识到这误入了 DSL 的领域，带来了所有的功耗和可维护性问题，事实上已经有了一种机制来处理 JS 的全部功能——回想一下 **frontload** 函数本身接收**组件** props。因此，我们可以在 **frontload** 函数本身中做出任意复杂的决定，决定是否在**组件**更新上实际触发任何昂贵的请求。

—

`**frontloadServerRender**(renderFunction)`

可以将 **frontloadServerRender** 函数视为对现有 React 服务器渲染逻辑的修饰，您必须将它封装在一个输出服务器渲染的 React 标记的`renderFunction`中。通常，`renderFunction`将是一个简单的`ReactDOM.renderToString`调用的包装器。

frontloadServerRender 的输出与 renderFunction 产生的标记相同，可以以通常的方式在对客户端的响应中返回。它只不过是此功能的代理，其存在只是为了在最终渲染之前“注入”同步加载应用程序中所有 frontload 功能所需的管道代码。

## 它是如何工作的？(即将推出)

在未来的一篇文章中，我会解释**是如何实现的。现在，如果你好奇的话，请阅读源代码，我已经尽可能清晰地写了出来，它本身还附有一些实现注释。**

我希望你觉得这篇文章很有趣。我开发了 **react-frontload** 来整理和改进我自己的应用程序中的代码，也是在思考这个问题一段时间并多次尝试和失败后想出一个解决它的好方法的一次学习经历。

我把源代码和 npm 包放在那里，以防这个解决方案对 React 社区中的其他人有用。它有不错的测试覆盖率，我已经在我的副业项目[https://postbelt.com](https://postbelt.com)的生产中使用了一段时间，在那里它没有出现任何问题。

然而，它很大程度上是为我的特定用例而编写的，很可能存在我没有发现的 bug/edge 用例，只是因为我还不需要使用它们。如果是这样的话，我非常乐意回答任何问题，并审查 github 回购上的 PRs！

感谢你花时间阅读这篇文章。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！