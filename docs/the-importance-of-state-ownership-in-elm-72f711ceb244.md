# 榆树市国有制的重要性

> 原文：<https://medium.com/hackernoon/the-importance-of-state-ownership-in-elm-72f711ceb244>

披露——我没有我在这里提出的所有问题的答案——一如既往，有不止一种方法！

Elm 使用单一状态原子模式来管理状态，这种模式对于使用 redux 进行反应的用户来说是很熟悉的。当动作发生时，状态被初始化，然后在更新功能中被“修改”。状态通常被传递到视图函数中，以便在构建 UI 时使用。

到目前为止还不错，但是榆树建筑在本质上也是分形的。这意味着通常在一个树中组合这种模式的多个实例。这通常意味着我们的国家也变得等级森严。

例如，在一个典型的 SPA 中，我们可能有这样的结构:

```
RootComponent
    PageOne
    PageTwo
    PageThree
```

这代表整个应用程序，然后是每个页面的组件(与应用程序中的每个路线相关)。在这种情况下，通常会创建如下所示的顶级模型:

```
type alias Root =
    { pageOne: PageOne.Model
    , pageTwo: PageTwo.Model 
    , pageThree: PageThree.Model
    }
```

这个结构可以这样初始化:

```
initialiase: Model 
initialise =
    { pageOne = PageOne.initialise
    , pageTwo = PageTwo.initialise
    , pageThree = PageThree.initialise
    }
```

这是一个非常有用的模式，因为它使我们能够在页面组件中隐藏页面级模型的结构和初始化逻辑。

但是谁拥有这些数据呢？那是什么意思？我将所有权定义如下:如果数据仅在组件更新功能中更新，则组件拥有数据。所以 RootComponent 的更新函数可能如下所示:

```
update: Msg -> Model -> (Model, Cmd Msg)
update msg model =
    case msg of
        PageOneMsg sub ->
            let
                (subModel, subCmd) =
                    PageOne.update sub model.pageOne
            in
                ( { model | pageOne = subModel }
                , Cmd.map PageOneMsg subCmd )
    ...
```

我们认为这是纯粹的样板文件，即我们只是委托给 PageOne 组件的更新功能，我们知道它负责修改其模型以响应操作。

但是如果事情没有那么简单(从来都不是这样)呢？假设我们有一个用户的概念，这通常是一个根级别的问题。这可能会将我们的 RootComponent 的模型更改为如下所示。

```
type alias Root =
    { **user: User**
    , pageOne: PageOne.Model
    , pageTwo: PageTwo.Model 
    , pageThree: PageThree.Model
    }
```

对于我们来说，想要访问应用程序所有级别的用户信息是很常见的。那么我们如何实现这一点呢？PageOne 的视图函数是通过 PageOne 传递的。模型，因此它不能访问 RootComponent。模型的用户属性。我们自然倾向于将用户属性放在第一页。模特也是。

```
initialiase: **User** -> Model 
initialise **user** =
    { pageOne = PageOne.initialise **user**
    , pageTwo = PageTwo.initialise **user**
    , pageThree = PageThree.initialise **user**
    }
```

只要用户从不改变，这是没问题的。如果在应用程序的生命周期中有可能改变数据的事件，那么你已经为自己制造了一个你可能还没有注意到的问题。

问题在于所有权。你现在有两个版本的真相。RootComponent 的模型和 PageOne 的模型。让我们假设我们的用户退出。谁处理这种类型的行动？很可能是根组件:

```
update: Msg -> Model -> (Model, Cmd Msg)
update msg model =
    case msg of
        SignOut ->
            ( { model | user = signOut model.user }
            , Cmd.none )
    ...
```

所以在这种情况下，我们会说 RootComponent *拥有*用户状态。上面代码的问题是，它没有更新我们也添加到 PageOne 模型中的用户副本。我们有两个真相来源，但它们突然不同步了。希望我们至少注意到了。

假设我们注意到了，我们该怎么办？

# 同步数据？

我们似乎在说，当在 RootComponent 中发生签出动作时，我们也需要更新某些子组件的模型。一种选择是简单地在 RootComponent 的更新函数中这样做。

```
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        SignOut ->
            let
                pageOne =
                    model.pageOne signedOut =
                    signOut model.user
            in
                ( { model
                    | user = signedOut
                    , pageOne = { pageOne | user = signedOut }
                  }
                , Cmd.none
                )
```

这有什么不好？一些事情。首先，我们打破了我们的封闭性。RootComponent 现在需要知道 PageOne 模型的结构。这使得 PageOne 模型更难改变。其次，忘记做这件事太容易了。我们的问题不会马上显现出来。

我们也许可以通过将*如何*更新 PageOne 的模型委托给 PageOne 实用函数来解决第一个问题。

```
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        SignOut ->
            let
                signedOut =
                    signOut model.user
            in
                ( { model
                    | user = signedOut
                    , **pageOne = PageOne.updateUser signedOut**
                  }
                , Cmd.none
                )
```

这样更好。我们重新获得了封装。但是更严重的问题依然存在。没有什么能阻止我们忘记做这件事。

这个函数`PageOne.updateUser`看起来很像普通更新函数的特殊形式，不是吗？如果我们只是调用普通的更新函数，会不会更合适呢？

```
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        SignOut ->
            let
                signedOut =
                    signOut model.user ( pageOne, pageOneCmd ) =
                    PageOne.update 
                        (PageOneMsg PageOne.signOut) model.pageOne
            in
                ( { model
                    | user = signedOut
                    , pageOne = pageOne
                  }
                , Cmd.map PageOneMsg pageOneCmd
                )
```

你可能更喜欢这样，但实际上在一天结束时都是一样的。我仍然想不出一种方法来使这种级联更新自动化(或者至少由编译器强制执行)。

# 朝着正确方向迈出的一步？

也许我们可以将用户添加到子组件的模型中，并约定在每次调用 update / view 时必须用父组件的副本替换它。所以 RootComponent 的更新函数可能看起来像这样:

```
update: Msg -> Model -> (Model, Cmd Msg)
update msg model =
    case msg of
        PageOneMsg sub ->
            let
                (subModel, subCmd) =
                    PageOne.update sub model.pageOne
            in
            ( { model | pageOne = **updateUser model.user subModel**
                , Cmd.map PageOneMsg subCmd )
    ...-- a general util that can be used from anywhere
**updateUser : User -> { a | user: User} -> { a | user: User}
updateUser user model =
    { model | user = user }**
```

现在我们不会失去同步**，但前提是每个人都记得这是使用**的模式。用这种方法，当我们忘记的时候，我们会不断地被发现。

# 不允许易失性数据的两个副本

我们必须得出结论，我们不能允许任何不稳定状态有两个副本。但这又把我们带回了问题，PageOne 组件如何访问用户。答案是确保任何这样的状态都是从父组件连同子组件的模型一起传入的，而不是作为 if 的一部分。因此，PageOne 的更新签名可能看起来像这样:

```
update: Msg -> User -> Model -> (Model, Cmd Msg)
```

如果我们在视图中需要这种状态，我们会希望对视图签名进行类似的调整。请注意，这与 React 中采用的方法非常相似，在 React 中，我们拥有道具(从上面传递的数据，但我不拥有这些道具)并声明哪些数据是我自己可以控制的。

这种方法的最大优点是，我们每次都不能不从上面传入它，并且我们不再有数据的两个副本。没有什么是不同步的，这是非常有价值的。不利的一面是，如果你要达到这个规模，你必须小心谨慎。当我发现另一个状态实际上属于一个祖先组件时会发生什么？我还得把这个加到签名上。一路向下。

这会让人觉得已经有太多的样板文件了。但在我看来，这是唯一真正的解决办法。如果你认为还有其他选择，请告诉我！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！