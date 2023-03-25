# 关于榆树发展的几点思考

> 原文：<https://medium.com/hackernoon/some-thoughts-on-elm-development-39a0f8a9002a>

## 背景

我玩 Elm 已经有几个月了，现在我已经开始觉得我已经了解了这门语言以及如何把事情做好。这是一种非常好的感觉。然而，有一件事一直困扰着我，那就是一个组件和整个应用程序应该如何调整和设置。虽然这可能看起来是一个微不足道的细节，并且很容易被忽略，但是我发现这个小细节可以对一个或几个人长期维护的项目或者由一个更大的团队在短期内构建和维护的项目的结果产生深远的影响。

在查看 Elm 代码示例时，似乎没有明显的示例，所以我查看了个人示例，并与其他人的观点进行了比较。

## 第一步:看看你已经做了什么…

从现在感觉像是无穷无尽的角度应用开始，我将从这里开始。根据 Angular 社区中无处不在的 John Papa 的指导，最佳实践似乎已经达到将控制器、服务和工厂划分为尽可能小的块。在 UI 方面，指令是一条可行的道路，但是我观察到它们的大小没有工厂和控制器那么严格。

> …老实说，现在是 2016 年。我们有大量的用于后端的高效工具、快捷方式、技巧和抽象，为什么还有人在前端编写 HTML 呢？

实际上，我见过并经历过这样的项目，它们采用微小的服务、工厂和控制器，在指令之间共享它们，并且随着代码库的发展，产生非常微妙的错误和难以追踪的边缘情况。此外，随着时间的推移，指令有膨胀的趋势。然而，最让我沮丧的是:非常诚实地说，现在是 2016 年。我们有大量的用于后端的高效工具、快捷方式、技巧和抽象，为什么还有人在前端编写 HTML 呢？事实上，任何人不仅想继续使用 HTML，而且实际上认为将 Javascript 随机插入 HTML 标签中会是一个更好的主意，这对我来说就像在开放的伤口上撒盐。

> 对我来说，Typescript 和 Angular 2 感觉就像是向 web Java 的回归；这让我很难过。

这就是我对 Angular 1 的体验。现在我需要决定是升级到 2 级还是跳槽。随着 Angular 的人积极推动 Typescript、OOP，以及创建新项目的日益沉重的开销，感觉 Angular 1 中唯一有趣的东西已经被吸走了。对我来说，Typescript 和 Angular 2 感觉就像是向 web Java 的回归；这让我很难过。

## 第二步:考虑替代方案…

所以 Angular 的两个答案，据我所知是 React 和 Elm。我查看了 React，实际上对这篇关于 React 无状态函数和 Ramda 的功能组件的[帖子](/@mirkomariani/functional-components-with-react-stateless-functions-and-ramda-e83e54fcd86b#.u69g3uryt)感到非常兴奋。我通读了一遍，很喜欢我所看到的，并认为这将是模型。将所有东西分割成微小的 DOM 块，让数据流经它们各自的组件，这似乎正是我考虑应用程序的方式。我被迷住了。

不幸的是，对于所有来自 React 社区的好想法，有很多，有两件事让我担心:

1.  工具、库、最佳实践、API 甚至架构已经连续两年每个季度都在搅动。虽然太多的创新是一个典型的问题，但是 JS 疲劳是真实的，并且在跨分布式团队工作时会成倍恶化。
2.  React 和框架/设计/架构一样都是一门学科。如果团队中有人不守纪律，事情就会变得一团糟。

## 因此把我带到了榆树…

榆树很棒。它通过尽可能减少状态问题来处理它。它处理“严肃的 HTML…这是 2016 年的问题，通过代表我的函数生成它。它将我的应用程序简化为可测试的功能。它通过一个令人愉快且有用的编译器降低了重构导致的错误的风险。它消除了所有说服力的类型不变量。它甚至在每次保存后为我格式化文件。它解决了我这么多的 JS 疼痛，我很高兴能做一个后空翻。然而，尽管如此，有些事情感觉不太对劲。

## 布兰南街的精神指引…

所以我寻求专业帮助。最近在旧金山的一次榆树聚会上，我和永远令人愉快的理查德·费尔德曼聊了聊。我和他分享了编写无状态组件的梦想，这些组件可以像“拼图块”一样被用来构建越来越大的块，并创建一个网站。他友好地倾听并提供反馈，这完全改变了我对应用程序应该如何架构的思路。

> “你需要多大就做多大的记录。当它让你不舒服的时候，可能就是重构的时候了。”

这与我迄今为止所读到的关于埃尔姆的一切都截然相反。当我阅读可组合的无状态组件时，我想到了无数的三行函数慢慢构建成一个网站。事实证明 Elm 也可以做到这一点(您很快就会看到这一点)。但这并不是最难的部分。真正重要的是缩减器和状态管理部分。在 Elm 应用程序中传递状态和消息现在是一个活跃的讨论领域，但是普遍的看法是没有必要将应用程序分割成比所需更小的部分。在一个 elm 文件中包含一个完整的页面或 Elm 应用程序的主要组件是很自然的事情；模型、更新、视图等。尽管如果你愿意，你当然可以把这三个部分分开。

所以这个周末，我带着这个建议在一个可爱的免费榆树球场工作时接受了这个建议。这很有趣，也很自然，让我重新体会到了函数式编程的一个主要好处:函数式编程为你提供了为自己和他人构建漂亮 API 的工具和框架。不信你看看这个更新功能(一个 elm app 的真实动作在哪里):

```
update : Msg -> Model -> Model
update msg model =
    case msg of
        -- On new input, update the text in input box
        Input name ->
            { model | name = name } --On clicking the save button
        Save ->
            case (model.playerId) of
                -- If the user is being edited, IE already has a 
                -- playerId
                Just playerId ->
                    model
                        |> updatePlayerNames playerId
                        |> updateNameInPlays playerId
                        |> clearInputBox
                        |> clearPlayerIndex
                        |> deactivateEditMode -- If the user is new, IE doesn't yet have 
                -- a playerId
                Nothing ->
                    model
                        |> addNewPlayer
                        |> clearInputBox
                        |> clearPlayerIndex
                        |> deactivateEditMode -- On clicking the cancel button
        Cancel ->
            model
                |> clearInputBox
                |> clearPlayerIndex
                |> deactivateEditMode -- When the edit mode is activated by "pencil button" click
        Edit player ->
            model
                |> putNameInInputBox player.name
                |> setPlayerIndex player.playerId
                |> activateEditMode player.playerId -- When the two or three point buttons are clicked, 
        -- update scores
        Score playerId points ->
            model
                |> updatePlayerScore playerId points
                |> appendPlays playerId points DeletePlay play ->
            model
                |> removePlay play
                |> updatePlayerScore
                    play.playerId
                    (negate play.points)
```

如果那不是禅，我不知道那是什么。那是适合人类消费的代码。您可以向非技术经理展示的代码…或者你妈妈，他们会完全理解的。*“哦，对了，当你编辑一个球员时，把他们的名字放进输入框，设置球员索引，然后激活编辑模式……”。*完美。

## 我是如何学会停止担忧，重新开始创作的…

所以我从中得到的教训是……放松。当您的应用程序从概念验证阶段进入生产阶段时，Elm 会处理那些让您和您的团队感到沮丧的事情，其间还会有许多新特性。**将你的应用程序分割成微小的部分是治标不治本。**您可以将每一个 DOM 呈现部分缩减成更小、更可重用的组件，比如:

```
playerSection : Model -> Html Msg
playerSection model =
    div
        []
        [ playerListHeader
        , playerListModel model
        , pointTotal model
        ]pointTotal : Model -> Html Msg
pointTotal model =
    let
        total =
            List.map .points model.players
                |> List.sum
    in
        footer []
            [ div [] [ text "Total: " ]
            , div [] [ text <| toString total ]
            ]playerListHeader : Html Msg
playerListHeader =
    header
        []
        [ div [] [ text "Name" ]
        , div [] [ text "Points" ]
        ]playerListModel : Model -> Html Msg
playerListModel model =
    model.players
        |> List.sortBy .name
        |> List.map (domPlayerMaker model)
        |> ul []domPlayerMaker : Model -> Player -> Html Msg
domPlayerMaker model player =
    let
        editChecker =
            if
                model.editMode
                    == True
                    && model.editPlayerId
                    == player.playerId
            then
                class "edit"
            else
                class ""
    in
        li []
            [ i
                [ class "edit"
                , onClick (Edit player)
                ]
                []
            , div [ editChecker ]
                [ text player.name ]
            , pointButtonMaker 2 player
            , pointButtonMaker 3 player
            , div []
                [ text <| toString player.points ]
            ]pointButtonMaker : Int -> Player -> Html Msg
pointButtonMaker i player =
    button
        [ type' "button"
        , onClick <| Score player.playerId i
        ]
        [ text <| toString (i) ++ "pt" ]
```

…这非常酷，也是 React 文章一开始就如此简洁的原因。然而，微观的 DOM 函数实际上并不是一个需要答案的问题。

让我血压下降的是状态被最小化，并且在一个地方，每个组件开始完成，并且只负责代码的更新功能。构建你的声明式 API 的所有助手函数都能够留在文件中或者移动到一个助手函数文件中；这是你的电话。然而，函数式编程为您提供了快速构建 API 所需的所有工具，并且希望其他人会发现该 API 非常有用，并且能够在他们的项目中加以利用。最重要的是，因为所有这些函数都是纯函数，所以当你们都在使用它的时候，你不再需要担心有人会改变对象、原型或者代码中的任何东西。给定一个 x，这个函数将总是产生一个 y，而不管这个函数是在何时、何地、在什么上下文中被调用的。

我意识到这是 React 在无状态功能组件中的一个主要推动，但是我需要强调，这是一个建议，而不是一个要求。即使是最训练有素的团队也会偷工减料，POC 也有一个非常糟糕的习惯，那就是超过了它们的有用性，并且可怕地超出了它们的预期用途。我在软件开发方面的经验不断提醒我两个谚语:

1.  任何可以被削减的角落，在某个时候都会被削减。
2.  没有比暂时更长久的了。

Elm 消除了我所见过的 JS 框架的所有主要障碍，几乎没有什么需要放弃的。但由于一切都还很新鲜，最佳实践似乎更多的是口头传统，而不是任何致力于写作的东西。因此，我想与他人分享我的想法、经验和对话，以便这种语言和 Elm 社区能够繁荣发展。理查德的反馈是我前进所需的指导，让我对自己正在做的事情充满信心，并让我期待构建更大、更有趣的东西。我真诚地希望我已经或将要在这里写的东西对其他人同样有用，我将继续写我在 Elm 的经历和学习。

感谢詹姆斯·马龙将免费的 Elm 教程放在网上，感谢理查德·费尔德曼倾听我的意见，给我信心，让我继续做一些很酷的东西。

代码可供您查阅:[此处](https://github.com/NewMountain/Scorekeeper)。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！