# 合金开发日记—第二部分

> 原文：<https://medium.com/hackernoon/alloyci-dev-diary-part-2-6a61b02faca8>

# 构建一个新的凤凰项目

![](img/bda701d4c082c11567ccdbfe80710dd4.png)

Photo by [William Bout](https://unsplash.com/photos/RkJF2BMrLJc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

这篇博客文章是详述 [AlloyCI](https://hackernoon.com/tagged/alloyci) 的[开发](https://hackernoon.com/tagged/development)过程的系列文章的一部分。之前的条目有:

*   [第 1 部分:开始新项目](https://hackernoon.com/alloyci-dev-diary-part-1-8ae564fce9f3)

如果您来自 Rails 背景，您可能会发现 Phoenix 项目的文件夹结构有点奇怪，尤其是在 1.3 中的更改之后。但是不要因此而退缩，这个结构实际上很有意义，特别是新的 1.3 结构。

不像 Rails，没有`app/`文件夹来存放你的大部分代码。Phoenix 1.3 项目遵循几乎所有常规 Elixir 项目的结构，这意味着您的大部分代码将位于`lib/my_app`文件夹中。

正如你现在可能知道的，Phoenix 1.3 改变了项目的结构。在这个改变之前，你的大部分代码都在`web/`之下，几乎没有进一步的组织。你可以有一个`models`目录，在那里你可以放置所有的业务逻辑，而不需要考虑它们是如何相互作用的。这个文件夹会变得很大，很快。另外，`model`这个名字似乎意味着一个对象，但是在 Elixir 中没有对象，所以将数据访问层存储在名为`models`的文件夹下没有什么上下文意义。

## 数据和上下文

默认情况下，Phoenix 1.3 会引导您以更好的方式组织代码。控制器、视图、模板放在`lib/my_app_web`下，数据库迁移和相关文件放在`priv/repo`下，而你的灵药代码的其余部分将放在`lib/my_app`下。这是基本的设置，您可以对其进行调整，并根据自己的喜好进行更改。你有完全的自由去组织你的代码。

自从我在凤凰 1.3 完全发布之前开始写 AlloyCI *以来，一些文件夹结构与最新的 1.3 生成器创建的不同。我更喜欢 AlloyCI 现在的结构，因为我真的不喜欢他们把`web/`文件夹重命名为`alloy_ci_web/`的方式，以及里面的所有东西从`AlloyCi.Web.XXX`重命名为`AlloyCiWeb.XXX`。我真的更喜欢在模块名称中的分隔，以及在 web 文件夹名称中不重复应用程序名称的事实。不过，由于 Phoenix 提供的灵活性，我不需要遵循惯例。*

无论如何，关于结构变化的最重要的部分是，Phoenix 现在指导您使用**上下文**来构建您的数据访问层。

以 AlloyCI 为例，我们有`Accounts`上下文(在`lib/alloy_ci/accounts`文件夹下)，其中有`User`、`Authentication`和`Installation`模式。这 3 种模式密切相关，属于同一个业务逻辑，即账户的处理。

如果您仔细查看`accounts`文件夹下的[文件](https://github.com/AlloyCI/alloy_ci/tree/master/lib/alloy_ci/accounts)，您会发现除了 changeset 函数之外，模式文件中没有其他函数。这意味着我需要直接通过`Ecto`来操作数据库数据(不推荐),或者我需要一个 API 边界来允许我对帐户相关的模式执行操作。

这就是`AlloyCi.Accounts`模块发挥作用的地方。如果 AlloyCI 需要对与帐户相关的任何模式执行操作，这个模块就是 alloy ci 与之交互的边界。该模块的所有公共函数提供了一种操作数据的简单方法，同时通过间接层提供了安全性。

这就是**语境的目的。它们在你的数据层和你的业务逻辑之间提供了一个边界，并且允许你有一个明确的契约来告诉你如何操作你的数据。它还允许你保持独立于`Ecto`。**

比方说，在未来，你想从`Ecto`切换到“最新、最酷的 DB 驱动”。如果你不使用抽象层，比如上下文，你将不得不重构代码库中使用`Ecto`与数据层通信的每一个函数。*但是*因为我们使用了上下文，我们只需要在上下文中重构代码。

## 数据显示

将你的数据真实呈现给用户的代码可以存在于`lib/my_app/web`或`lib/my_app_web`文件夹下，这取决于你想如何构建它(自动生成器将默认为`lib/my_app_web`，但我更喜欢前者)。

在这里你可以找到你的控制器、视图、模板和通道所在的文件夹。让我们从表示层开始。

## **视图&模板**

如果您来自 Rails 背景，您可能想知道为什么有两个组件来呈现数据，而在 Rails 中您所需要的只是`views`文件夹。在 Phoenix 中,“视图”不是由模板化的 HTML 文件组成的，而是常规的 Elixir 模块。这些模块可以帮助您与模板共享代码，并实现与 Rails“视图助手”相似的目的，但默认情况下，这些模块特定于单个控制器(不加载其他视图，不像 Rails 加载所有视图助手，不管调用哪个控制器)。这种分离使得在呈现数据所需的相似助手函数上使用相同的签名变得更加容易(而不会真正使它们过载)，这取决于调用的是哪个控制器，从而简化了您的代码。

模板就是你的 HTML 代码所在的地方。模板文件保存为`*.html.eex`文件(意思是嵌入的药剂)，与`erb`文件非常相似。语法是完全相同的，但是您编写的不是 Ruby 代码，而是 Elixir 代码😄

Phoenix 和 Rails 之间一个非常重要的区别是如何在控制器和模板之间共享信息。在 Rails 中，用`@something`声明一个实例变量就足够了，它将对模板/视图可用。

鉴于 Elixir 的功能性质，在 Phoenix 中，您需要显式地将您希望提供的信息传递给`render`函数中的视图。这些被称为分配。作为示例，下面是`PipelineController`的`show`动作:

在`"show.html"`之后的都是赋值变量，所以与`show`动作相关的模板可用的变量是`builds`、`pipeline`和`current_user`。我们可以在 pipeline info 头的代码片段中看到如何使用它们的示例:

一旦变量被赋值，它就可以通过`@var_name`进入模板，就像 Rails 一样。定义了与控制器同名的视图文件的函数(在本例中为`pipeline_view.ex`)可立即用于模板。在上面的例子中，`sha_link/1`创建了一个指向 GitHub 上特定提交的 HTML 链接。

## **控制器**

在结构上，Phoenix 控制器与 Rails 控制器非常相似，主要区别如上所述。当由助手工具生成时，它们将像 Rails 中的对应对象一样执行相同的索引、显示、编辑、更新和删除操作。就像 Rails 控制器一样，您可以通过定义一个函数并连接一个路由来定义您想要的任何动作。

## **频道**

Phoenix 通道用于通过 web 套接字与 Web 客户端通信。它们类似于 Rails 中的`ActionCable`，但在我看来，要强大得多，而且 [performant](https://dockyard.com/blog/2016/08/09/phoenix-channels-vs-rails-action-cable) 。在 AlloyCI 中，它们用于实时推送构建日志的输出，并接收一段预先格式化的 HTML 代码来显示用户的存储库(关于 AlloyCI 如何使用通道的更多信息将在另一篇文章中讨论)。

现在你知道了。这是凤凰计划的基本架构。这里还有我们没有涉及的其他组件，比如*插头*，或者*后台工作*。我们将在以后的博客文章中讨论这些高级主题。