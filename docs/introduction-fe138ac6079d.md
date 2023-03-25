# 用 Phoenix 和 Elixir 编写博客引擎:第 1 部分

> 原文：<https://medium.com/hackernoon/introduction-fe138ac6079d>

**最后更新于**:2016 年 7 月 20 日

## 当前版本:

在撰写本文时，我们应用程序的当前版本是:

*   **仙丹** : v1.3.1
*   **凤凰:** v1.2.0
*   **Ecto:** v2.0.2
*   **康美宁:** v2.5.2

如果你正在读这篇文章，而这些不是最新的，请告诉我，我会相应地更新这篇教程。

## 安装 Phoenix

安装 Phoenix 的最佳说明可以在 Phoenix 网站上找到[。](http://www.phoenixframework.org/docs/installation)

## 第一步:让我们添加帖子

我们需要从使用 Phoenix mix 任务创建一个新项目开始，我们将其命名为“pxblog”。我们使用 mix phoenix . new[project][command]来实现这一点。对所有问题回答 Y，因为我们确实想使用 phoenix.js 和其他前端需求。

```
mix phoenix.new pxblog
```

输出:

```
* creating pxblog/config/config.exs
...Fetch and install dependencies? [Yn] y
* running mix deps.get
* running npm install && node node_modules/brunch/bin/brunch buildWe are all set! Run your Phoenix application:$ cd pxblog
$ mix phoenix.serverYou can also run your app inside IEx (Interactive Elixir) as:$ iex -S mix phoenix.serverBefore moving on, configure your database in config/dev.exs and run:$ mix ecto.create
```

我们应该看到一串输出，表明我们的项目已经完成，初始工作已经完成。

如果我们没有正确设置 postgres 数据库或者配置我们的应用程序使用正确的凭证，那么 **mix ecto.create** 步骤可能会失败。如果您打开 **config/dev.exs** ，您应该在文件底部看到一些配置细节:

```
# Configure your database
config :pxblog, Pxblog.Repo,
  adapter: Ecto.Adapters.Postgres,
  username: "postgres",
  password: "postgres",
  database: "pxblog_dev",
  hostname: "localhost",
  pool_size: 10
```

只需将用户名和密码更改为拥有正确的数据库创建权限的角色。

当我们有了工作，我们将启动服务器只是为了确保一切正常。

```
$ iex -S mix phoenix.server
```

我们现在应该能够访问 [http://localhost:4000/](http://localhost:4000/) 并看到“欢迎来到凤凰城！”页面。现在我们有了一个好的基线，让我们添加创建帖子的基本框架，因为这毕竟是一个博客引擎。

我们要做的第一件事是利用 Phoenix 的一个生成器，不仅为我们构建了 Ecto 模型和迁移，还构建了 UI 脚手架来处理 Post 对象的 CRUD ( *Create、Read、Update、Delete* )操作。因为现在这是一个非常非常简单的博客引擎，所以让我们只关注标题和正文；标题是字符串，正文是文本。这些生成器的语法非常简单:

> 混合 phoenix.gen.html[模型名][表名][列名:列类型]…

```
$ mix phoenix.gen.html Post posts title:string body:text
```

输出:

```
* creating web/controllers/post_controller.ex
...Add the resource to your browser scope in web/router.ex: resources "/posts", PostControllerRemember to update your repository by running migrations: $ mix ecto.migrate
```

接下来，为了使这个脚手架可访问(并阻止 Elixir 抱怨)，我们将打开 web/router.ex，并将以下内容添加到我们的根作用域(使用 **:browser** 管道的“/”作用域):

```
resources "/posts", PostController
```

最后，我们将通过调用 **mix ecto.migrate** 来确保我们的数据库加载了这个新的迁移。

输出:

```
Compiling 9 files (.ex)
Generated pxblog app15:52:20.004 [info]  == Running Pxblog.Repo.Migrations.CreatePost.change/0 forward15:52:20.004 [info]  create table posts15:52:20.019 [info]  == Migrated in 0.0s
```

最后，让我们重新启动我们的服务器，然后访问[http://localhost:4000/posts](http://localhost:4000/posts)，我们应该看到“Listing posts”标题和一个包含我们对象中的列的表格。

稍微摆弄一下，你应该能够创建新帖子、编辑帖子和删除帖子。对于很少的工作来说很酷！

## 步骤 1B:职位的写作测试

从一开始就使用脚手架的美妙之处在于，它将从一开始就为我们创建基线测试。我们甚至还不需要做太多的修改，因为我们还没有真正改变任何框架，但是让我们分析一下为我们创建了什么，以便我们可以更好地准备以后编写我们自己的测试。

首先，我们将打开**test/models/post _ test . exs**看一看:

```
defmodule Pxblog.PostTest do
  use Pxblog.ModelCase alias Pxblog.Post @valid_attrs %{body: "some content", title: "some content"}
  @invalid_attrs %{} test "changeset with valid attributes" do
    changeset = Post.changeset(%Post{}, @valid_attrs)
    assert changeset.valid?
  end test "changeset with invalid attributes" do
    changeset = Post.changeset(%Post{}, @invalid_attrs)
    refute changeset.valid?
  end
end
```

我们把这个拆开，了解一下是怎么回事。

> defmodule Pxblog。测试后 do

显然，我们需要使用应用程序的名称空间来定义测试模块。

> 用 Pxblog。模型案例

接下来，我们告诉这个模块，它将使用 ModelCase 宏集引入的函数和 DSL。

> 别名 Pxblog。邮政

现在确保测试能够看到模型本身。

> @valid_attrs %{body:“某内容”，title:“某内容”}

设置一些能够创建成功变更集的基本有效属性。这只是提供了一个模块级的变量，每当我们希望能够创建一个有效的模型时，我们都可以从中提取变量。

> @invalid_attrs %{}

如上所述，但是不出所料，创建了一个无效的属性集。

```
test "changeset with valid attributes" do
  changeset = Post.changeset(%Post{}, @valid_attrs)
  assert changeset.valid?
end
```

现在，我们通过使用“test”函数给它一个基于字符串的名称来创建我们的测试。在我们的函数体内部，我们首先从 Post 模型创建一个变更集(给它一个空结构和有效参数列表)。然后我们断言变更集是有效的，因为这就是我们对@valid_attrs 变量的期望。

```
test "changeset with invalid attributes" do
  changeset = Post.changeset(%Post{}, @invalid_attrs)
  refute changeset.valid?
end
```

最后，我们检查是否创建了带有无效参数列表的变更集，并且不是断言变更集是有效的，而是执行相反的操作。反驳本质上是断言不真实。

这是一个如何编写模型测试文件的很好的例子。现在让我们来看看控制器测试。

让我们看看顶部，因为所有的看起来大致相同:

```
defmodule Pxblog.PostControllerTest do
  use Pxblog.ConnCase alias Pxblog.Post
  @valid_attrs %{body: "some content", title: "some content"}
  @invalid_attrs %{}
```

第一个变更集可以看到的是 Pxblog。ConnCase 我们依赖用于控制器级测试的 DSL。除此之外，其余的台词应该很熟悉了。

让我们来看看第一个测试:

```
test "lists all entries on index", %{conn: conn} do
  conn = get conn, post_path(conn, :index)
  assert html_response(conn, 200) =~ "Listing posts"
end
```

在这里，我们获取将通过 ConnCase 中的设置块发送的“conn”变量。这个我以后再解释。下一步是我们调用适当的动词来访问预期的路线，在我们的例子中，这是针对我们的索引操作的 get 请求。

然后，我们断言这个动作的响应返回状态为 200(“ok”)的 HTML，并包含短语“Listing posts”

```
test "renders form for new resources", %{conn: conn} do
  conn = get conn, post_path(conn, :new)
  assert html_response(conn, 200) =~ "New post"
end
```

下一个测试基本上是相同的，但是我们只是验证“新”动作。简单的东西。

```
test "creates resource and redirects when data is valid", %{conn: conn} do
  conn = post conn, post_path(conn, :create), post: @valid_attrs
  assert redirected_to(conn) == post_path(conn, :index)
  assert Repo.get_by(Post, @valid_attrs)
end
```

现在，我们正在做一些新的事情。首先，这次我们向 **post_path** 助手发送有效参数列表。我们期望被重定向到 post 资源的索引路径。 **redirected_to** 接受一个连接对象作为参数，因为我们需要看到这个连接对象被重定向到哪里。

最后，通过查询我们的 Ecto Repo，寻找与我们的@valid_attrs 参数匹配的 Post 模型，我们断言由这些有效参数表示的对象被成功地插入到数据库中。

现在，我们要测试创建新帖子的负面路径。

```
test "does not create resource and renders errors when data is invalid", %{conn: conn} do
  conn = post conn, post_path(conn, :create), post: @invalid_attrs
  assert html_response(conn, 200) =~ "New post"
end
```

因此，我们使用 invalid_attrs 参数列表向相同的创建路径进行 post，并断言它再次呈现新的 Post 表单。

```
test "shows chosen resource", %{conn: conn} do
  post = Repo.insert! %Post{}
  conn = get conn, post_path(conn, :show, post)
  assert html_response(conn, 200) =~ "Show post"
end
```

为了测试我们的显示动作，我们确保创建一个 Post 模型来使用。然后，我们调用 post_path 助手的 get 函数，并确保它返回适当的资源。但是，如果我们试图获取一个不存在的资源的显示路径，我们会执行以下操作:

```
test "renders page not found when id is nonexistent", %{conn: conn} do
  assert_error_sent 404, fn ->
    get conn, post_path(conn, :show, -1)
  end
end
```

我们在这里看到了一个新的模式，但它实际上很容易理解。我们希望，如果我们获取一个不存在的资源，我们应该收到一个 404。然后，我们向它传递一个匿名函数，该函数包含我们要执行的代码，该代码应该会返回错误。简单！

除了我们的删除操作之外，其余的测试只是对每个路径重复上面的测试。让我们来看看:

```
test "deletes chosen resource", %{conn: conn} do
  post = Repo.insert! %Post{}
  conn = delete conn, post_path(conn, :delete, post)
  assert redirected_to(conn) == post_path(conn, :index)
  refute Repo.get(Post, post.id)
end
```

在这里，我们看到的大部分是相同的，除了使用我们的删除动词。我们断言，我们从删除页面重定向回索引，但是我们在这里做了一些新的事情:我们驳斥了 Post 对象不再存在的说法。断言和反驳都是真的，所以一个对象的存在将与断言一起工作，并导致反驳失败。

我们没有向视图添加任何代码，所以我们没有对 PostView 模块做任何事情。

## 步骤 2:添加用户

除了几个不同的列之外，我们将遵循与创建 Post 对象几乎完全相同的步骤来创建用户对象。首先，我们将运行:

```
$ mix phoenix.gen.html User users username:string email:string password_digest:string
```

输出:

```
* creating web/controllers/user_controller.ex
...Add the resource to your browser scope in web/router.ex: resources "/users", UserControllerRemember to update your repository by running migrations: $ mix ecto.migrate
```

接下来，我们将打开 web/router.ex，并再次将以下内容添加到我们的浏览器范围中:

```
resources "/users", UserController
```

这里的语法定义了一个标准的资源路由，其中第一个参数是 URL，第二个是控制器的类名。然后，我们将运行 mix ecto.migrate

输出:

```
Compiling 11 files (.ex)
Generated pxblog app16:02:03.987 [info]  == Running Pxblog.Repo.Migrations.CreateUser.change/0 forward16:02:03.987 [info]  create table users16:02:03.996 [info]  == Migrated in 0.0s
```

最后，重启服务器并查看 [http://localhost:4000/users。](http://localhost:4000/users.)我们现在可以独立创建帖子和用户了！不幸的是，这还不是一个非常有用的博客。毕竟，我们可以创建用户(事实上，任何人都可以)，但我们甚至不能登录。另外，密码摘要不是来自任何加密算法；用户只是创建它们，我们以纯文本的形式存储它们！不好了。

我们将使它看起来更像一个真实的用户注册屏幕。

我们对用户内容的测试看起来与为我们的帖子自动生成的测试完全一样，所以我们将把它们放在一边，直到我们开始修改逻辑(也就是现在！)

## 步骤 3:保存密码哈希而不是密码

当我们访问 **/users/new** 时，我们看到三个字段:**用户名**、**邮箱**和**密码摘要**。但是当你在其他网站注册时，你需要输入密码并确认密码！我们如何纠正这种情况？

在**web/templates/user/form . html . eex**中，删除以下几行:

```
<div class=”form-group”>
  <%= label f, :password_digest, class: “control-label” %>
  <%= text_input f, :password_digest, class: “form-control” %>
  <%= error_tag f, :password_digest %>
</div>
```

并在它的位置添加:

```
<div class="form-group">
  <%= label f, :password, "Password", class: "control-label" %>
  <%= password_input f, :password, class: "form-control" %>
  <%= error_tag f, :password %>
</div>

<div class="form-group">
  <%= label f, :password_confirmation, "Password Confirmation", class: "control-label" %>
  <%= password_input f, :password_confirmation, class: "form-control" %>
  <%= error_tag f, :password_confirmation %>
</div>
```

刷新页面(应该会自动发生)，输入用户详细信息，点击提交。

错误:

```
Oops, something went wrong! Please check the errors below.
```

这是因为我们正在创建一个密码和密码确认，但是没有做任何事情来创建实际的 password_digest。让我们编写一些代码来实现这一点。首先，我们将修改实际的模式来做一些新的事情:

在 **web/models/user.ex** 中:

```
schema "users" do
  field :username, :string
  field :email, :string
  field :password_digest, :string timestamps # Virtual Fields
  field :password, :string, virtual: true
  field :password_confirmation, :string, virtual: true  
end
```

注意添加了两个字段:密码和密码确认。我们将这些字段声明为**虚拟**字段，因为它们实际上并不存在于我们的数据库中，而是需要作为属性存在于我们的用户结构中。这也允许我们在变更集函数中应用转换。

然后，我们修改必填字段和强制转换字段的列表，以包括:**密码**和:**密码 _ 确认**。

```
def changeset(struct, params \\ %{}) do
  struct
  |> cast(params, [:username, :email, :password, :password_confirmation])
  |> validate_required([:username, :email, :password, :password_confirmation])
end
```

如果您此时运行**test/models/user _ test . exs**，您会注意到我们的“具有有效属性的变更集”测试现在失败了。这是因为我们要求输入 password 和 password_confirmation，但是没有更新我们的@valid_attrs 映射来包含这两者。让我们把这一行改成:

```
@valid_attrs %{email: "[test@test.com](mailto:test@test.com)", password: "test1234", password_confirmation: "test1234", username: "testuser"}
```

我们的模型测试应该马上就能通过了！我们还需要通过控制器测试。在**test/controllers/user _ controller _ test . exs**中，我们会做一些修改。首先，我们将区分有效的创建属性和有效的搜索属性:

```
@valid_create_attrs %{email: "[test@test.com](mailto:test@test.com)", password: "test1234", password_confirmation: "test1234", username: "testuser"}
@valid_attrs %{email: "[test@test.com](mailto:test@test.com)", username: "testuser"}
```

然后我们将修改我们的创建测试:

```
test "creates resource and redirects when data is valid", %{conn: conn} do
  conn = post conn, user_path(conn, :create), user: @valid_create_attrs
  assert redirected_to(conn) == user_path(conn, :index)
  assert Repo.get_by(User, @valid_attrs)
end
```

我们的更新测试:

```
test "updates chosen resource and redirects when data is valid", %{conn: conn} do
  user = Repo.insert! %User{}
  conn = put conn, user_path(conn, :update, user), user: @valid_create_attrs
  assert redirected_to(conn) == user_path(conn, :show, user)
  assert Repo.get_by(User, @valid_attrs)
end
```

随着我们的测试变回绿色，我们需要修改 changeset 函数，将我们的密码动态地更改为密码摘要:

```
def changeset(struct, params \\ %{}) do
    struct
    |> cast(params, [:username, :email, :password, :password_confirmation])
    |> validate_required([:username, :email, :password, :password_confirmation])
    |> hash_password
  enddefp hash_password(changeset) do
    changeset
    |> put_change(:password_digest, "ABCDE")
  end
```

现在我们只是在研究散列函数的行为。第一步是确保我们可以在进行过程中修改我们的变更集。先验证一下这个行为。在我们的浏览器中返回到[http://localhost:4000/users](http://localhost:4000/users)，点击“New user ”,创建一个包含任何详细信息的新用户。当我们再次点击索引页面时，我们应该会看到使用“ABCDE”的 password_digest 值创建的用户。

并再次对该文件进行测试。我们的测试通过了，但是我们还没有为这个新的 **hash_password** 工作添加任何测试。让我们在模型测试套件中添加一个测试，它将添加一个关于密码摘要的测试:

```
test "password_digest value gets set to a hash" do
  changeset = User.changeset(%User{}, @valid_attrs)
  assert get_change(changeset, :password_digest) == "ABCDE"
end
```

这是一个很大的进步，但是对于安全性来说并不是很好！让我们用 Bcrypt 修改我们的散列，使之成为真正的密码散列，这是由**come on**库提供的。

首先，打开 **mix.exs** ，将**:come on**添加到我们的应用列表中:

```
def application do
    [mod: {Pxblog, []},
     applications: [:phoenix, :phoenix_pubsub, :phoenix_html, :cowboy, :logger, :gettext,
                    :phoenix_ecto, :postgrex, :comeonin]]
  end
```

我们还需要修改我们的 deps 定义:

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
   {:comeonin, "~> 2.3"}]
end
```

这里也一样，注意增加了 **{:comeonin，" ~ > 2.3"}** 。现在，让我们关闭一直在运行的服务器，运行 **mix deps.get** 。如果一切顺利(应该的！)，那么现在你应该能够重新运行 **iex -S mix phoenix.server** 来重启你的服务器。

我们旧的 **hash_password** 方法很简洁，但是我们需要用它来散列我们的密码。因为我们已经添加了**come on**库，它为我们提供了一个带有 hashpwsalt 方法的 Bcrypt 模块，所以让我们将它导入到我们的用户模型中。

在 **web/models/user.ex** 中，在我们的 **use Pxblog 下添加下面一行。网，:型号**线:

```
import Comeonin.Bcrypt, only: [hashpwsalt: 1]
```

我们在这里做的是将 Bcrypt 模块拉入 Comeonin 名称空间下，并导入 arity 为 1 的 hashpwsalt 方法。现在我们要修改我们的 **hash_password** 方法来工作。

```
defp hash_password(changeset) do
  if password = get_change(changeset, :password) do
    changeset
    |> put_change(:password_digest, hashpwsalt(password))
  else
    changeset
  end
end
```

让我们再次尝试创建一个用户！这一次，在输入我们的用户名、电子邮件、密码和密码确认数据后，我们应该看到一个加密的摘要显示在 **password_digest** 字段中！

现在，我们将想要处理我们添加的 **hash_password** 函数。我们要做的第一件事是更改我们测试环境的配置，以确保我们的测试在使用密码加密时不会明显变慢。打开 **config/test.exs** 并在底部添加以下内容:

```
config :comeonin, bcrypt_log_rounds: 4
```

这将配置 ComeOnIn，当它在我们的测试环境中时，不要太努力地加密我们的密码。由于这只是为了测试，我们不需要任何超级安全的东西，而是更喜欢理智和速度！在 **config/prod.exs** 中，我们希望改为:

```
config :comeonin, bcrypt_log_rounds: 14
```

让我们为 comeonin 调用编写一个测试。我们会说得不那么具体。我们只想验证加密。在**test/models/user _ test . exs**中:

```
test "password_digest value gets set to a hash" do
  changeset = User.changeset(%User{}, @valid_attrs)
  assert Comeonin.Bcrypt.checkpw(@valid_attrs.password, Ecto.Changeset.get_change(changeset, :password_digest))
end
```

对于更多的测试覆盖，让我们添加一个案例来处理没有命中 password = get_change()行的情况:

```
test "password_digest value does not get set if password is nil" do
  changeset = User.changeset(%User{}, %{email: "test@test.com", password: nil, password_confirmation: nil, username: "test"})
  refute Ecto.Changeset.get_change(changeset, :password_digest)
end
```

由于 assert/reject 使用了 truthiness，我们可以看看这段代码是否将 **password_digest** 留空，它确实留空了！我们在用规范覆盖我们的工作方面做得很好！

## 第四步:登录吧！

让我们添加一个新的控制器 SessionController 和一个伴随的视图 SessionView。我们将从简单开始，随着时间的推移，逐步实现更好的实现。

创建**网络/控制器/会话 _ 控制器. ex:**

```
defmodule Pxblog.SessionController do
  use Pxblog.Web, :controller def new(conn, _params) do
    render conn, "new.html"
  end
end
```

创建**web/views/session _ view . ex**:

```
defmodule Pxblog.SessionView do
  use Pxblog.Web, :view
end
```

创建 web/templates/session/new . html . eex:

```
<h2>Login</h2>
```

最后，让我们更新路由器以包括这个新的控制器。将下面一行添加到我们的“/”范围中:

```
resources "/sessions", SessionController, only: [:new]
```

目前我们想公开的唯一路线是**新**，所以我们将仅限于此。同样，我们希望事情简单，并建立在稳定的基础上。

现在让我们访问[http://localhost:4000/sessions/new](http://localhost:4000/sessions/new)，我们应该会看到 Phoenix 框架头和“Login”头。

让我们给它一个真实的形式。创建**web/templates/session/form . html . eex**

```
<%= form_for @changeset, @action, fn f -> %>
  <%= if f.errors != [] do %>
    <div class="alert alert-danger">
      <p>Oops, something went wrong! Please check the errors below:</p>
      <ul>
        <%= for {attr, message} <- f.errors do %>
          <li><%= humanize(attr) %> <%= message %></li>
        <% end %>
      </ul>
    </div>
  <% end %> <div class="form-group">
    <label>Username</label>
    <%= text_input f, :username, class: "form-control" %>
  </div> <div class="form-group">
    <label>Password</label>
    <%= password_input f, :password, class: "form-control" %>
  </div> <div class="form-group">
    <%= submit "Submit", class: "btn btn-primary" %>
  </div>
<% end %>
```

并修改**web/templates/session/new . html . eex**通过添加一行来调用我们的新表单:

```
<%= render "form.html", changeset: @changeset, action: session_path(@conn, :create) %>
```

自动重新加载将立即显示一个错误页面，因为我们实际上还没有定义 **@changeset** ，正如您所猜测的，它需要是一个变更集。因为我们使用的是成员对象，上面已经有了用户名和密码字段，让我们使用它吧！

在**web/controllers/session _ controller . ex**中，我们需要为用户模型添加别名，以便能够进一步使用它。在我们班的最上面，在我们的**下面用 Pxblog。Web，:控制器**行，添加以下内容:

```
alias Pxblog.User
```

并在**新增的**函数中，修改调用呈现如下:

```
render conn, "new.html", changeset: User.changeset(%User{})
```

我们需要向它传递连接、我们正在呈现的模板(不包括 eex)以及应该向我们的模板公开的其他变量的列表。在这种情况下，我们希望公开@changeset，所以我们在这里指定 changeset:并为具有空白用户结构的用户赋予它 Ecto changeset。(%User{}是未设置值的用户结构)

现在刷新，我们会得到类似如下的不同错误消息:

```
No helper clause for Pxblog.Router.Helpers.session_path/2 defined for action :create.
The following session_path actions are defined under your router:    
*:new
```

在我们的表单中，我们引用了一条实际上还不存在的路线！我们使用 session_path 帮助器，向它传递@conn 对象，然后指定:create 路径，我们还没有创建它。

我们已经走了一段路。现在，让我们创建它，这样我们就可以发布我们的登录详细信息并设置会话。

让我们更新我们的路线，以允许发布创建。

在 **web/router.ex** 中，将我们对 SessionController 的引用也改为包含:create。

```
resources "/sessions", SessionController, only: [:new, :create]
```

在**web/controllers/session _ controller . ex**中，我们需要从 Comeonin 的 Bcrypt 模块中导入一个新的函数 checkpw。我们通过以下代码行实现这一点:

```
import Comeonin.Bcrypt, only: [checkpw: 2]
```

这一行写着“从美国进口”。Bcrypt 模块，但只有 arity 为 2”的 checkpw 函数。然后让我们添加一个 scrub_params 插件来处理用户数据。在我们的函数之前，我们将添加:

```
plug :scrub_params, "user" when action in [:create]
```

“scrub_params”是清理任何用户输入的特殊函数；例如，在某些内容作为空字符串传入的情况下，scrub_params 会将其转换为一个 nil 值，以避免在数据库中创建包含空字符串的条目。

让我们添加我们的函数来处理创建帖子。我们将把它添加到 SessionController 模块的底部。这里会有很多代码，所以我们会一点一点来。

在**web/controllers/session _ controller . ex**中:

```
def create(conn, %{"user" => user_params}) do
    Repo.get_by(User, username: user_params["username"])
    |> sign_in(user_params["password"], conn)
  end
```

这段代码的第一位， **Repo.get_by(User，username:User _ params[" username "])**从我们的 Ecto Repo 中提取具有匹配用户名的第一个适用用户，否则将返回 nil。

以下是验证这种行为的一些输出:

```
iex(3)> Repo.get_by(User, username: "flibbity")
[debug] SELECT u0."id", u0."username", u0."email", u0."password_digest", u0."inserted_at", u0."updated_at" FROM "users" AS u0 WHERE (u0."username" = $1) ["flibbity"] OK query=0.7ms
niliex(4)> Repo.get_by(User, username: "test")
[debug] SELECT u0."id", u0."username", u0."email", u0."password_digest", u0."inserted_at", u0."updated_at" FROM "users" AS u0 WHERE (u0."username" = $1) ["test"] OK query=0.8ms
%Pxblog.User{__meta__: %Ecto.Schema.Metadata{source: "users", state: :loaded},
 email: "test", id: 15,
 inserted_at: %Ecto.DateTime{day: 24, hour: 19, min: 6, month: 6, sec: 14,
  usec: 0, year: 2015}, password: nil, password_confirmation: nil,
 password_digest: "$2b$12$RRkTZiUoPVuIHMCJd7yZUOnAptSFyM9Hw3Aa88ik4erEsXTZQmwu2",
 updated_at: %Ecto.DateTime{day: 24, hour: 19, min: 6, month: 6, sec: 14,
  usec: 0, year: 2015}, username: "test"}
```

然后我们获取用户，并将该用户链接到一个 sign_in 方法中。我们还没有写，所以让我们这样做吧！

```
defp sign_in(user, password, conn) when is_nil(user) do
  conn
  |> put_flash(:error, "Invalid username/password combination!")
  |> redirect(to: page_path(conn, :index))
enddefp sign_in(user, password, conn) do
  if checkpw(password, user.password_digest) do
    conn
    |> put_session(:current_user, %{id: user.id, username: user.username})
    |> put_flash(:info, "Sign in successful!")
    |> redirect(to: page_path(conn, :index))
  else
    conn
    |> put_session(:current_user, nil)
    |> put_flash(:error, "Invalid username/password combination!")
    |> redirect(to: page_path(conn, :index))
  end
end
```

首先要注意的是这些方法在。这些方法中的第一个有一个附加的 guard 子句，所以该方法只有在 guard 子句为真时才会被执行，所以如果用户是 nil，我们用适当的 flash 消息重定向回页面(根)路径的索引。

如果 guard 子句为 false，将调用第二个方法，该方法将处理所有其他场景。我们检查 checkpw 函数的结果，如果结果为真，我们将用户设置为 current_user 会话变量，并使用成功消息进行重定向。否则，我们清除当前的用户会话，设置一个错误消息，并重定向回根目录。

如果我们返回到我们的登录页面 http://localhost:4000/sessions/new，我们应该能够用一组有效的凭据和无效的凭据测试登录，并看到相应的错误消息！

我们还需要为这个控制器写一些规范。我们将创建**test/controllers/session _ controller _ test . exs**，并用以下内容填充它:

```
defmodule Pxblog.SessionControllerTest do
  use Pxblog.ConnCase
  alias Pxblog.User setup do
    User.changeset(%User{}, %{username: "test", password: "test", password_confirmation: "test", email: "[test@test.com](mailto:test@test.com)"})
    |> Repo.insert
    {:ok, conn: build_conn()}
  end test "shows the login form", %{conn: conn} do
    conn = get conn, session_path(conn, :new)
    assert html_response(conn, 200) =~ "Login"
  end test "creates a new user session for a valid user", %{conn: conn} do
    conn = post conn, session_path(conn, :create), user: %{username: "test", password: "test"}
    assert get_session(conn, :current_user)
    assert get_flash(conn, :info) == "Sign in successful!"
    assert redirected_to(conn) == page_path(conn, :index)
  end test "does not create a session with a bad login", %{conn: conn} do
    conn = post conn, session_path(conn, :create), user: %{username: "test", password: "wrong"}
    refute get_session(conn, :current_user)
    assert get_flash(conn, :error) == "Invalid username/password combination!"
    assert redirected_to(conn) == page_path(conn, :index)
  end test "does not create a session if user does not exist", %{conn: conn} do    
    conn = post conn, session_path(conn, :create), user: %{username: "foo", password: "wrong"}
    assert get_flash(conn, :error) == "Invalid username/password combination!"
    assert redirected_to(conn) == page_path(conn, :index)
  end
end
```

我们从标准设置块开始，为 get 请求编写一个非常标准的断言。这开始变得更有趣了:

```
test "creates a new user session for a valid user", %{conn: conn} do
  conn = post conn, session_path(conn, :create), user: %{username: "test", password: "test"}
  assert get_session(conn, :current_user)
  assert get_flash(conn, :info) == "Sign in successful!"
  assert redirected_to(conn) == page_path(conn, :index)
end
```

这里的第一部分是提交到我们的会话创建路径。然后，我们验证我们设置了 current_user 会话变量、动作的 flash 消息，最后，我们断言我们被重定向到哪里。

其他两个调用只是使用相同类型的断言(在一个例子中，是反驳)来确保我们测试了 sign_in 函数可以到达的所有路径。同样，非常简单的东西！

## 步骤 5:公开我们的当前用户

让我们修改我们的布局，根据成员是否登录来显示消息或链接。

在 **web/views/layout_view.ex** 中，让我们编写一个帮助器，使我们能够方便地访问用户信息，因此我们将添加:

```
def current_user(conn) do
  Plug.Conn.get_session(conn, :current_user)
end
```

让我们编写一个测试来确保这是可行的。

在**web/templates/layout/app . html . eex**中，让我们执行以下操作，而不是“开始”链接:

```
<li>
        <%= if user = current_user(@conn) do %>
          Logged in as
          <strong><%= user.username %></strong>
          <br>
          <%= link "Log out", to: session_path(@conn, :delete, user.id), method: :delete %>
        <% else %>
          <%= link "Log in", to: session_path(@conn, :new) %>
        <% end %>
      </li>
```

同样，让我们一步一步来。我们需要做的第一件事是找出当前用户是谁，假设他们已经登录。我们将采用先简单，后重构的方法，所以现在我们将在模板中设置一个来自会话的用户对象。get_session 是插件的一部分。连接对象。如果用户存在(这利用了 Elixir 的类似 Ruby 的真值，因为 nil 在这里将返回 false。)

如果用户已经登录，我们还想提供一个**注销**链接。尽管这还不存在，但它最终需要存在，所以现在我们要把它发送出去。我们将会话视为资源，因此要注销，我们将“删除”该会话，因此我们将在这里提供一个到它的链接。

我们还想输出当前用户的用户名。我们将用户结构存储在:current_user 会话变量中，因此我们可以访问用户名 user.username。

如果我们找不到用户，那么我们将只提供登录链接。同样，我们在这里将会话视为资源，因此“new”将提供创建新会话的适当路径。

您可能已经注意到，当所有内容都刷新时，我们会收到另一条关于缺少匹配函数子句的错误消息。让我们添加我们的删除路线，以及让凤凰高兴！

在 **web/router.ex** 中，我们将修改我们的“会话”路由，以便还允许:删除:

```
resources "/sessions", SessionController, only: [:new, :create, :delete]
```

让我们也修改一下控制器。在**web/controllers/session _ controller . ex**中，添加以下内容:

```
def delete(conn, _params) do
  conn
  |> delete_session(:current_user)
  |> put_flash(:info, "Signed out successfully!")
  |> redirect(to: page_path(conn, :index))
end
```

因为我们只是删除了:current_user 键，所以我们实际上并不关心参数是什么，所以我们用下划线将它们标记为未使用。我们设置了一个 flash 消息，使用户界面更加清晰，并重定向回我们的根路由。

我们现在可以登录、注销，还可以看到登录失败！事情正在向好的方向发展！但是首先，我们需要编写一些测试。我们将从 LayoutView 的测试开始。

```
defmodule Pxblog.LayoutViewTest do
  use Pxblog.ConnCase, async: true alias Pxblog.LayoutView
  alias Pxblog.User setup do
    User.changeset(%User{}, %{username: "test", password: "test", password_confirmation: "test", email: "[test@test.com](mailto:test@test.com)"})
    |> Repo.insert
    {:ok, conn: build_conn()}
  end test "current user returns the user in the session", %{conn: conn} do
    conn = post conn, session_path(conn, :create), user: %{username: "test", password: "test"}
    assert LayoutView.current_user(conn)
  end test "current user returns nothing if there is no user in the session", %{conn: conn} do
    user = Repo.get_by(User, %{username: "test"})
    conn = delete conn, session_path(conn, :delete, user)
    refute LayoutView.current_user(conn)
  end
end
```

我们来分析一下。我们要做的第一件事是别名 LayoutView 和 User 模块，这样我们可以缩短一些代码。接下来，在我们的 setup 块中，我们创建一个用户并将其放入数据库。然后我们返回标准的 **{:ok，conn: build_conn()}** 元组。

然后，我们编写第一个测试，登录到我们的会话 create 动作，并断言，登录后，LayoutView.current_user 函数返回一些数据。

然后我们为我们的负面情况写作；我们显式地删除了会话，并反驳了用户从我们的 current_user 调用中返回的说法。我们还通过添加一个删除操作来更新我们的 SessionController，所以我们需要设置好我们的测试。

```
test "deletes the user session", %{conn: conn} do
  user = Repo.get_by(User, %{username: "test"})
  conn = delete conn, session_path(conn, :delete, user)
  refute get_session(conn, :current_user)
  assert get_flash(conn, :info) == "Signed out successfully!"
  assert redirected_to(conn) == page_path(conn, :index)
end
```

我们确保会话中的 current_user 为空，然后我们检查 flash 消息并确保我们被重定向出去！

## 编译资产时可能出现的错误

需要注意的一点是，当您试图用 brunch 编译资产时，可能会遇到错误。我得到的错误消息是:

```
16 Dec 23:30:20 — error: Compiling of ‘web/static/js/app.js’ failed. Couldn’t find preset “es2015” relative to directory “web/static/js” ; Compiling of ‘web/static/js/socket.js’ failed. Couldn’t find preset “es2015” relative to directory “web/static/js” ; Compiling of ‘deps/phoenix/web/static/js/phoenix.js’ failed. Couldn’t find preset “es2015” relative to directory “deps/phoenix/web/static/js” ; Compiling of ‘deps/phoenix_html/web/static/js/phoenix_html.js’ failed. Couldn’t find preset “es2015” relative to directory “deps/phoenix_html/web/static/js”
```

你可以用 NPM 安装 **babel-preset-es2015** 来解决这个问题。我运行了以下命令:

```
npm install -g babel-preset-es2015
```

现在，如果您启动服务器，您应该看到所有的资产都被正确编译了！

## 本系列的下一篇文章

 [## 用 Phoenix 和 Elixir 编写博客引擎:第 2 部分，授权

### 最后更新时间:2016 年 7 月 21 日

medium.com](/@diamondgfx/writing-a-blog-engine-in-phoenix-part-2-authorization-814c06fa7c0) 

# 看看我的新书！

嘿大家好！如果你喜欢你在这里读到的东西，并且想和我一起学习更多，可以看看我的新书《长生不老药和凤凰网开发》:

[](https://www.packtpub.com/web-development/phoenix-web-development) [## 凤凰网开发| PACKT 图书

### 学习使用 Elixir 和……从头开始构建投票 web 应用程序的高性能功能原型

www.packtpub.com](https://www.packtpub.com/web-development/phoenix-web-development) 

我真的很兴奋终于可以把这个项目推向世界了！它的写作风格与我的其他教程一样，我们将从头到尾构建一个完整项目的框架，甚至涵盖一些更棘手的主题，如文件上传、Twitter/Google OAuth 登录和 API！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！