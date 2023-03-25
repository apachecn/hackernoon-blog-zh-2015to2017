# 标记的联合、反应和还原

> 原文：<https://medium.com/hackernoon/tagged-unions-react-and-redux-55e262e4d0ea>

由于种种原因，Typescript 和 Flow 非常酷。我想在将它们与 [React](https://hackernoon.com/tagged/react) 一起使用时强调一个这样的例子。具体来说，我们将看到标记的联合如何通过 redux 连接的组件为我们提供更好的文档和保证。本文将假设对类型有一些基本的了解。

# 需求

让我们从构建一个简单的聊天应用程序开始。它将获取登录的用户、聊天的用户列表，并将其全部呈现在屏幕上。没什么特别令人兴奋的，但是这个应用有一些有趣的状态。例如，它可以是:脱机状态，我们看不到其他用户；加载状态，我们将看到一些加载指示器；加载状态，我们将看到自己和其他用户在一起。

# 连接我们的组件

首先，这里有一个快速而简单的解决方案，将我们的组件连接到我们的 redux 存储。我们还不想写视图或连接函数，我们将把注意力集中在作为我们的视图的道具传递的`mapStateToProps`函数的返回值上。

```
type MapStateToPropsResult
    = { loading?: boolean
      , disconnected?: boolean
      , users?: User[]
      , activeUser?: User
      }
```

很简单:一个具有所有可选属性的对象。这是完全可用的；我们可以继续我们的快乐之路，类型系统会很高兴地让我们在使用它们之前检查每个属性是否存在。

你觉得有什么问题吗？业务需求在这些属性之间有明确的关系，但是通过查看这些代码无法推断出任何关系。当屏幕加载时，我们有`users`或`activeUser`是没有意义的。此外，如果我们可以在应用程序知道它离线时与用户交谈，那就没有意义了。如果我们简单地让一切都是可选的，那么所有这些微妙之处都会消失。

其次，我们错过了一个宝贵的机会*使用类型系统作为工具*。被动地处理类型、编写代码太容易了，编译器只是一个安全网，确保你不犯任何打字错误。如果我们积极地思考类型在我们的系统中是如何工作的，我们可以让编译器引导我们找到正确的解决方案。

记住这一点，让我们修改我们的类型签名。

```
type MapStateToPropsResult
    = { uiState: 'loading' }
    | { uiState: 'disconnected', activeUser: User }
    | { uiState: 'loaded', activeUser: User, users: User[] }
```

我们将所有可选属性的单个对象拆分成三个具有所有必需属性的对象的并集。现在，当您查看这个类型时，您可以很快看到属性之间的关系以及 UI 处于什么状态。现在很清楚，当应用程序加载时，我们将无法访问`user`或`activeUser`。同样，当我们断开连接时，我们也无法访问`users`。

最后，我们可以利用这种类型让编译器指导我们编写符合我们业务逻辑的代码！

```
@connect((state, props): MapStateToPropsResult => {})
class UserList extends React.Component
// ...
```

我们已经清除了我们的类和`mapStateToProps`函数，并告诉编译器这个函数将返回我们刚刚定义的类型。编译器马上发现我们没有返回联合类型的值，它将开始引导我们纠正代码。

```
@connect((state, props): MapStateToPropsResult => {
    const activeUser = selectActiveUser(state) // User | undefined
    const users = selectOtherUsers(state) // User[] | undefined if (activeUser) {
        return { uiState: 'loaded', activeUser, users } // ERROR
    }
})
class UserList extends React.Component
// ...
```

举个例子，也许我们已经获取完了`activeUser`的数据，但是我们仍然在等待`users`从服务器返回。由于`loaded`中需要`users`和`activeUser`，这种类型将迫使我们转而呈现一种`loading`状态。

通过使所有的属性成为必需的类型，我们告诉编译器*强制*我们处理加载状态。

# 景色

从视图的角度来看，这有什么好处呢？首先，现在我们将无法访问联盟内的属性，直到我们弄清楚我们处于什么状态。

```
class UserList extends React.Component {
    render() {
        const user = this.props.activeUser // Error!
    }
}
```

比如上面的例子，类型系统不知道我们是否是`loading`等，因此不能保证`activeUser`存在于我们的`props`上。那么真实的例子会是什么样的呢？

```
class UserList extends React.Component {
    renderLoading() { /* ... */ }
    renderDisconnected(activeUser) { /* ... */ }
    renderLoaded(activeUser, users) { /* ... */ } render() {
        const { uiState } = this.props
        if (uiState === 'loading')
            return this.renderLoading() const { activeUser } = this.props
        if (uiState === 'disconnected')
            return this.renderDisconnected(activeUser) const { users } = this.props
        if (uiState === 'loaded')
            return this.renderLoaded(activeUser, users)
    }
}
```

这只是我们接近视图逻辑的一种方式。有趣的是，当我们缩小联盟能做什么和不能做什么的范围时，我们会看到更多的支持。在第一个条件之后，我们检查类型系统是否可以推断出剩余的状态都包含`activeUser`，因此让我们通过 props 来访问它。

另一个有趣的方面是，如果我们在渲染函数中遗漏了案例，它可以通知我们。也许以后我们会在我们的联合中添加另一个案例，`away,`，并且它的呈现略有不同。类型系统将会发现这个变化，并告诉我们渲染函数并不总是返回`JSX`(假设我们输入了渲染函数的返回值)。

# 最后的想法

如果到目前为止你一直坚持和我在一起，你可能会说我对此很感兴趣。动态语言的美妙之处有很多很多。然而，来到类型化语言的世界，有全新的表达能力领域正等待开发。

我知道有句谚语“工作总是会填满所有可用的时间。”对我来说，就是“如果有更懒的方法，我还是不会去做，因为这可能需要太多的努力。”任何能让别人强迫我做好工作的机会都是巨大的胜利。在这种情况下，我可以(故意)让一台冷酷无情、无所不能的计算机强迫我正确地完成我的工作。

说了这么多，做了这么多，为了子孙后代，这里有一些要点:

*   联合类型在原始数据之外很有用。将它们用于从 connect 或 props 返回到组件中的值。真的，他们真的真的很棒。
*   如果你看到多种选择开始渗入你的类型，考虑把它分解成一个没有选择的联合。
*   首先考虑你的类型。如果你预先告诉编译器函数的形状和结果，这将有助于你写出正确的代码。
*   我真的很喜欢阅读亚龙·明斯基的[这首曲子](https://blogs.janestreet.com/effective-ml-revisited/)、T2 的这首曲子和 T4 的这首曲子。它们都处理结构化类型，使不应该发生的状态不发生。它们都非常有启发性，并且都适用于不同的语言。
*   最后，类型给了我们一个在编译时嵌入和执行业务逻辑的独特机会。为了他们将来提供的安全性和文档，添加一些字符确实是值得的。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！