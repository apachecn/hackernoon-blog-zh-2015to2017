# 用 Phoenix 和 Elixir 编写博客引擎:第 5 部分，添加 ExMachina

> 原文：<https://medium.com/hackernoon/mixology-exmachina-92a08dc3e954>

**最新更新:**2016 年 7 月 21 日

## 本系列的前一篇文章

[](/@diamondgfx/writing-a-blog-engine-in-phoenix-and-elixir-part-4-adding-roles-to-our-controllers-9f4678b48468) [## 用 Phoenix 和 Elixir 编写博客引擎:第 4 部分，向控制器添加角色

### 最新更新:2016 年 1 月 26 日

medium.com](/@diamondgfx/writing-a-blog-engine-in-phoenix-and-elixir-part-4-adding-roles-to-our-controllers-9f4678b48468) 

## 介绍

由于我们一直在为我们的博客引擎编写代码，您可能已经注意到我们在代码库中使用了很少的库。这是双重的:首先，我们希望我要开始的第一个是 [ExMachina](https://github.com/thoughtbot/ex_machina) ，一个与 Ruby 的工厂女孩一脉相承的工厂库。

## 当前版本

在撰写本文时，我们应用程序的当前版本是:

*   **仙丹** : v1.3.1
*   凤凰城:1.2.0 版
*   **Ecto:** v2.0.2
*   **ExMachina** : v1.0.2

如果你正在读这篇文章，而这些不是最新的，请告诉我，我会相应地更新这篇教程。

## 这是什么？

如前所述，ExMachina 被设计成类似于 Ruby 的[工厂女孩](https://github.com/thoughtbot/factory_girl)的工厂风格实现(也来自 Thoughtbot 的优秀人员)。这里的前提是，对于您的测试来说，有一种方法可以为您的测试建立各种模型及其关联，而不必一遍又一遍地为它们重写创建逻辑，这很好。这可以通过编写提供简单创建函数的助手模块来完成，但最终您不得不为每个变体、每个额外关联等创建函数和助手，这可能会有点重复。

## 入门指南

我们将首先打开 **mix.exs** 并将 **ExMachina** 引入我们的依赖项和应用程序列表。

将 ExMachina 添加到我们的依赖列表非常简单，所以我们只需在 comeonin 之后添加一个 ExMachina 条目。

```
defp deps do
  [{:phoenix, "~> 1.2.0"},
   {:phoenix_pubsub, "~> 1.0"},
   {:phoenix_ecto, "~> 3.0"},
   {:postgrex, ">= 0.0.0"},
   {:phoenix_html, "~> 2.6"},
   {:phoenix_live_reload, "~> 1.0", only: :dev},
   {:gettext, "~> 0.11"},
   {:cowboy, "~> 1.0"},
   {:comeonin, "~> 2.5.2"},
   {:ex_machina, "~> 1.0"}]
end
```

接下来，我们将把 **:ex_machina** 添加到我们的应用程序正在使用的应用程序列表中:

```
def application do
  [mod: {Pxblog, []},
   applications: [:phoenix, :phoenix_pubsub, :phoenix_html, :cowboy, :logger, :gettext,
                  :phoenix_ecto, :postgrex, :comeonin, :ex_machina]]
end
```

运行以下命令，确保一切准备就绪并正确设置:

```
$ mix do deps.get, compile
```

如果一切顺利，您应该会看到一些关于获取 ExMachina 的输出，最后一切都编译成功了！你也应该运行**混合测试**并且在你开始修改之前验证一切都是绿色的，只是为了在这里保持额外的安全。

## 添加我们的第一个工厂:角色

我们需要做的是创建一个工厂模块，并使它可以被我们所有的测试访问。我最喜欢的不使每个测试膨胀太多的方法是将包含工厂的模块文件放入**测试/支持**中，然后专门将其导入到我们需要的测试中。

所以，让我们从创建**test/support/factory . ex**开始:

```
defmodule Pxblog.Factory do
  use ExMachina.Ecto, repo: Pxblog.Repo

  alias Pxblog.Role
  alias Pxblog.User
  alias Pxblog.Post

  def role_factory do
    %Role{
      name: sequence(:name, &"Test Role #{&1}"),
      admin: false
    }
  end
end
```

我们称之为**工厂**,因为这是这个模块要做的事情。然后，我们编写工厂函数，在 atom 上匹配构建/创建工厂的类型。因为它和《工厂女孩》很接近，所以它带有一些命名约定，值得注意。第一个是**构建**:构建意味着我们正在构建模型(不是变更集)，但是没有将它持久化到数据库中。 **insert** ，但是，确实将模型持久化到数据库中。

我们需要**使用 ExMachina。Ecto** bit 告诉 ExMachina 我们使用 Ecto 作为我们的 Repo 层，它应该相应地执行 create/associations/etc。我们还需要为所有的模型添加别名，因为我们将为所有的模型编写工厂。

**role_factory** 函数应该只返回一个 **Role** struct，它定义了我们希望它拥有的默认属性。此函数仅支持 arity 为 1。

这里的序列位非常有趣。我们希望为每个角色生成一个惟一的名称，因此为了能够做到这一点，我们定义了一个顺序生成的名称。我们使用 **sequence** 函数，我们向它传递两个参数:第一个是我们要为其生成序列的字段的名称，第二个是匿名函数，它返回一个字符串并向其中插入一个值。让我们来看看这个函数:

```
&”Test Role #{&1}”
```

如果您对 Elixir 感到满意，您可能会认为这是编写匿名函数的另一种方式。这大致可以解释为:

```
fn x ->
  "Test Role #{x}"
end
```

因此，如果我们考虑这个序列函数在做什么，它实际上只是在做这个:

```
sequence(:name, fn x ->
  "Test Role #{x}"
end)
```

最后，我们将 admin 标志设置为 false，因为我们将使用这些作为默认情况，并且只显式创建 admin 角色。稍后，我们还将讨论 ExMachina 的一些更复杂的选项。现在，我们将花一些时间将我们的新角色工厂集成到我们的控制器测试中。

## 改变我们的控制器测试来使用我们的角色工厂

首先打开**test/controllers/user _ controller _ test . exs**。在顶部，在我们的**设置**块中，我们将使用我们的 **TestHelper.create_role** 函数来修改这些位。

```
# ...import Pxblog.Factory

@valid_create_attrs %{email: "test@test.com", username: "test", password: "test", password_confirmation: "test"}
@valid_attrs %{email: "test@test.com", username: "test"}
@invalid_attrs %{}

setup do
  user_role = insert(:role)
  {:ok, nonadmin_user} = TestHelper.create_user(user_role, %{email: "nonadmin@test.com", username: "nonadmin", password: "test", password_confirmation: "test"})

  admin_role = insert(:role, admin: true)
  {:ok, admin_user}    = TestHelper.create_user(admin_role, %{email: "admin@test.com", username: "admin", password: "test", password_confirmation: "test"})

  {:ok, conn: build_conn(), admin_role: admin_role, user_role: user_role, nonadmin_user: nonadmin_user, admin_user: admin_user}
end# ...
```

我们必须从**导入**我们创建的工厂模块开始。第 10 行只是我们使用一个简单的角色使用**:角色**工厂。第 13 行是我们使用相同的 **:role** 工厂，但是我们特别将 admin 标志重写为 true。

保存文件并重新运行我们的测试，我们仍然是绿色的！现在，让我们实现我们的用户工厂，并引入一个也能构建关联的工厂！

## 添加我们的下一个工厂:用户

让我们来看看用户的工厂。

```
def user_factory do
  %User{
    username: sequence(:username, &"User #{&1}"),
    email: "test@test.com",
    password: "test1234",
    password_confirmation: "test1234",
    password_digest: Comeonin.Bcrypt.hashpwsalt("test1234"),
    role: build(:role)
  }
end
```

在很大程度上，这个工厂与我们为角色创建的工厂相匹配。我们首先要处理几个问题。在上面的第 7 行，您可以看到我们必须将 password_digest 值设置为散列密码值(因为我们模拟用户登录，所以您会希望包括这个值)。我们只是调用 Comeonin 下的 Bcrypt 模块，并专门调用 hashpwsalt 函数，向它传递我们在 password/password_confirmation 值中使用的相同密码。在第 8 行，我们还必须指定**角色**实际上是一个关联；我们使用**构建**函数，并通过原子传递我们想要为此构建的关联的名称。

修改完我们的用户工厂后，让我们回到**测试/控制器/用户 _ 控制器 _ 测试.实例**

```
setup do
  user_role     = insert(:role)
  nonadmin_user = insert(:user, role: user_role)

  admin_role = insert(:role, admin: true)
  admin_user = insert(:user, role: admin_role)

  {:ok, conn: build_conn(), admin_role: admin_role, user_role: user_role, nonadmin_user: nonadmin_user, admin_user: admin_user}
end
```

现在，我们已经用对工厂的调用完全替换了对 TestHelper 的所有调用。我们使用用户角色，并将其传递给我们的用户工厂，以便它知道使用哪个角色。然后我们对管理员用户做同样的事情，但是除此之外，我们根本不需要修改我们的测试！运行我们的测试，确保一切仍然是绿色的，然后我们可以继续进行！

## 添加我们的下一个工厂:帖子

既然我们已经非常擅长添加新工厂，那么为 posts 添加最终工厂应该非常简单。

```
def post_factory do
  %Post{
    title: "Some Post",
    body: "And the body of some post",
    user: build(:user)
  }
end
```

这里没有什么新东西，所以我们将直接修改**test/controllers/post _ controller _ test . exs**:

```
# ...import Pxblog.Factory

@valid_attrs %{body: "some content", title: "some content"}
@invalid_attrs %{}

setup do
  role = insert(:role)
  user = insert(:user, role: role)
  post = insert(:post, user: user)
  conn = build_conn() |> login_user(user)
  {:ok, conn: conn, user: user, role: role, post: post}
end# ...
```

我们再一次**导入** **Pxblog。工厂**所以我们的模块知道工厂调用来自哪里。然后，我们用工厂调用代替 setup 块中的所有创建步骤。**角色**由我们的 **insert** 语句填充，该语句创建我们的角色，然后用于从工厂构建我们的用户，最后该用户用于创建与该用户相关联的帖子…仅此而已！再次运行我们的测试，它又变回绿色了！

超过这一点，其他一切都只是繁重的工作；返回并用工厂调用替换对 TestHelper 的调用。这没有什么特别新的或令人兴奋的，所以我不会为每个细节而烦恼。

## 包含您的工厂的其他方式

我选择了将我的工厂显式导入到我的每个测试中的路线，但是如果您不想这样做，您可以改为执行以下操作之一:

将我们的别名语句添加到**test/support/model _ case . ex:**中的 using 块中

```
using do
  quote do
    alias Pxblog.Repo import Ecto
    import Ecto.Changeset
    import Ecto.Query
    import Pxblog.ModelCase
    import Pxblog.Factory
  end
end
```

和**test/support/conn _ case . ex:**

```
using do
  quote do
    # Import conveniences for testing with connections
    use Phoenix.ConnTest

    alias Pxblog.Repo
    import Ecto
    import Ecto.Changeset
    import Ecto.Query

    import Pxblog.Router.Helpers
    import Pxblog.Factory

    # The default endpoint for testing
    @endpoint Pxblog.Endpoint
  end
end
```

## 您可以使用 Ex 玛奇纳执行的其他功能

出于我们正在编写的小博客引擎的目的，我们并不明确需要 ExMachina 提供的一些现成的其他特性。例如，为了方便起见，build 和 create 都支持一些其他函数(我使用 build 作为例子，但是这些函数也支持 create):

```
build_pair(:factory, attrs)    <- Builds 2 models
build_list(n, :factory, attrs) <- Builds N models
```

您还可以通过对模型调用 create 来持久化使用工厂构建方法的模型:

```
build(:role) |> insert
```

## 其他资源

有关如何使用 ExMachina 的更多信息，请访问 Github 页面([https://github.com/thoughtbot/ex_machina](https://github.com/thoughtbot/ex_machina))。你也可以访问 thoughtbot 的技术博客，那里的创造者写了一篇很棒的文章，宣布了 ExMachina 和其他一些使用它的方法！问题岗位在 https://robots.thoughtbot.com/announcing-ex-machina。

## 包扎

不可否认的是，起初我有点担心，因为我已经看到了一些过去在《工厂女孩》中实现的事情，我害怕马上回到同样的场景，但 Elixir 在保护我们免受自己伤害方面做得如此好，我认为它相当好地平衡了。语法清晰明了，我不得不编写的代码量大幅下降，而且它让我们的测试更加轻松，所以总体来说我非常满意。非常感谢 Thoughtbot 的优秀员工给我们提供了另一个非常方便的库。

我也很想知道人们对这篇文章的看法。我一直在考虑发表一系列关于 Elixir 和 Phoenix 的不同库的文章，ExMachina 似乎是一个很好的开始！你通常可以在 Elixir Slack 群或 Elixir IRC 聊天室找到我，当然你也可以在 Twitter @ diamond gfx 上找到我。

## 本系列的下一篇文章

[](/@diamondgfx/writing-a-blog-engine-in-phoenix-and-elixir-part-5-markdown-support-fde72badd8e1) [## 用 Phoenix 和 Elixir 编写博客引擎:第 6 部分，降价支持

### 最新更新:2016 年 1 月 26 日

medium.com](/@diamondgfx/writing-a-blog-engine-in-phoenix-and-elixir-part-5-markdown-support-fde72badd8e1) 

# 看看我的新书！

嘿大家好！如果你喜欢你在这里读到的东西，并且想和我一起学习更多，可以看看我的新书《长生不老药和凤凰网开发》:

[](https://www.packtpub.com/web-development/phoenix-web-development) [## 凤凰网开发| PACKT 图书

### 学习使用 Elixir 和……从头开始构建投票 web 应用程序的高性能功能原型

www.packtpub.com](https://www.packtpub.com/web-development/phoenix-web-development) 

我真的很兴奋终于可以把这个项目推向世界了！它的写作风格与我的其他教程一样，我们将从头到尾构建一个完整项目的框架，甚至涵盖一些更棘手的主题，如文件上传、Twitter/Google OAuth 登录和 API！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！