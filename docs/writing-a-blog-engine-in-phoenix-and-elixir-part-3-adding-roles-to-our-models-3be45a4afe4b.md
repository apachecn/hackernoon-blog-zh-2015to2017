# 用 Phoenix 和 Elixir 编写博客引擎:第 3 部分，向模型添加角色

> 原文：<https://medium.com/hackernoon/writing-a-blog-engine-in-phoenix-and-elixir-part-3-adding-roles-to-our-models-3be45a4afe4b>

**最新更新:**2016 年 7 月 21 日

## 本系列的前一篇文章

 [## 用 Phoenix 和 Elixir 编写博客引擎:第 2 部分，授权

### 最后更新时间:2016 年 7 月 21 日

medium.com](/@diamondgfx/writing-a-blog-engine-in-phoenix-part-2-authorization-814c06fa7c0) 

## 当前版本

在撰写本文时，我们应用程序的当前版本是:

*   **仙丹** : v1.3.1
*   **凤凰:** v1.2.0
*   **Ecto:** v2.0.2
*   **康美宁:** v2.5.2

如果你正在读这篇文章，而这些不是最新的，请告诉我，我会相应地更新这篇教程。

## 我们离开的地方

当我们最后一次停止时，我们完成了我们的帖子与用户的关联，并开始适当地限制对帖子的访问，除非我们有有效的用户(并且用户首先创建了帖子)，但是如果我们想要有多个用户呢？我们希望用理智的用户规则来控制事情，这样我们就不会出现某个流氓用户突然删除所有人的账户或帖子的情况。我们将用一个相当标准的解决方案来解决这个问题:创建角色。

## 创建角色

我们将从在终端中运行以下命令开始:

```
$ mix phoenix.gen.model Role roles name:string admin:boolean
```

我们应该会看到类似于下面的输出:

```
* creating web/models/role.ex
* creating test/models/role_test.exs
* creating priv/repo/migrations/20160721151158_create_role.exsRemember to update your repository by running migrations:$ mix ecto.migrate
```

我们将按照脚本的建议，立即运行 **mix ecto.migrate** 。假设我们的数据库已经正确设置，我们应该看到类似如下的输出:

```
Compiling 21 files (.ex)
Generated pxblog app11:12:04.736 [info]  == Running Pxblog.Repo.Migrations.CreateRole.change/0 forward11:12:04.736 [info]  create table roles11:12:04.742 [info]  == Migrated in 0.0s
```

我们还将运行混合测试来检查我们的新模型添加没有干扰任何其他测试。如果一切正常，那么我们就可以继续修改我们的用户模型，关联一个相关的角色。

## 添加角色关联

对于这个特定的特性实现，我遵循的一般设计是每个用户有一个角色，每个角色有多个用户，因此我们将修改 **web/models/user.ex** 文件来反映这一点:

在模式“users”do 部分，我们将添加下面一行:

```
belongs_to :role, Pxblog.Role
```

在本例中，我们将把 role_id 外键放在 Users 表上，所以我们想说一个用户“属于”一个角色。我们还将打开 **web/models/role.ex** 并通过添加以下行来更改模式“roles”do 部分:

```
has_many :users, Pxblog.User
```

然后，我们将再次运行混合测试，但我们应该会遇到许多失败。我们告诉 Ecto，我们的 users 表与 roles 表有关系，但是我们从未在数据库中定义过，所以我们必须修改 users 表来保存对 role_id 的引用。

```
$ mix ecto.gen.migration add_role_id_to_usersCompiling 5 files (.ex)
* creating priv/repo/migrations
* creating priv/repo/migrations/20160721184919_add_role_id_to_users.exs
```

让我们打开它创建的迁移文件。默认情况下，它将包含:

```
defmodule Pxblog.Repo.Migrations.AddRoleIdToUsers do
  use Ecto.Migration def change do
  end
end
```

我们需要补充一些东西。第一件事是我们需要改变 users 表来添加对角色的引用，所以我们将用下面的代码来做这件事:

```
alter table(:users) do
  add :role_id, references(:roles)
end
```

我们应该为 role_id 添加一个索引，因此我们将添加下面一行:

```
create index(:users, [:role_id])
```

最后，我们将再次运行 **mix ecto.migrate** ，应该会看到一切都成功迁移了！如果我们现在运行我们的测试，一切应该又变绿了！不幸的是，我们的测试并不完美。首先，我们从来没有修改过 Post/User 模型的测试，以确保一个 Post 必须有一个用户定义。同样，当用户没有角色时，我们也不希望能够创建用户。在 **web/models/user.ex** 中，我们将 changeset 函数改为如下所示(注意**的两个附加项:role_id** ):

```
def changeset(struct, params \\ %{}) do
    struct
    |> cast(params, [:username, :email, :password, :password_confirmation, :role_id])
    |> validate_required([:username, :email, :password, :password_confirmation, :role_id])
    |> hash_password
  end
```

## 创建测试助手

现在运行我们的测试会导致很多失败，但是没关系！我们将做大量的工作来清理我们的测试，我们需要的东西之一是某种测试助手，以避免我们不得不一遍又一遍地编写相同的代码。我们将创建一个新文件来帮助我们构建这些模型。创建**test/support/test _ helper . ex**并用以下代码填充它:

```
defmodule Pxblog.TestHelper do
  alias Pxblog.Repo
  alias Pxblog.User
  alias Pxblog.Role
  alias Pxblog.Post import Ecto, only: [build_assoc: 2] def create_role(%{name: name, admin: admin}) do
    Role.changeset(%Role{}, %{name: name, admin: admin})
    |> Repo.insert
  end def create_user(role, %{email: email, username: username, password: password, password_confirmation: password_confirmation}) do
    role
    |> build_assoc(:users)
    |> User.changeset(%{email: email, username: username, password: password, password_confirmation: password_confirmation})
    |> Repo.insert
  end def create_post(user, %{title: title, body: body}) do
    user
    |> build_assoc(:posts)
    |> Post.changeset(%{title: title, body: body})
    |> Repo.insert
  end
end
```

在我们继续修正我们的测试之前，让我们讨论一下这个文件在做什么。首先要注意的是我们放置文件的位置: **test/support** ，在这里我们可以放置任何我们希望对我们的测试套件可用的模块。我们仍然需要在每个文件中对这些文件进行别名引用，但是没关系！

我们首先别名化我们的 Repo、User、Role 和 Post 模块，这样我们可以用更短的语法访问它们，我们还**导入 Ecto** 以便我们可以访问 **build_assoc** 方法来构建关联。

在 create_role 中，我们期望一个映射包含一个角色名称以及它是否是管理员。我们在这里使用 Repo.insert，这意味着我们将对成功的插入返回标准的 **{:ok，model}** 响应。否则，它只是一个角色变更集的简单插入。

在 **create_user** 中，我们将想要使用的角色作为第一个参数，将用于创建用户的参数映射作为第二个参数。我们从我们的角色开始，然后通过管道将其传递到我们的构建函数中，创建一个用户模型(因为我们指定了:users 作为关联)，然后通过管道将其传递到 User.changeset 中，并带有前面提到的参数。最终结果会被传送到 Repo.insert()中，我们就完成了！

虽然解释起来有点复杂，但我们最终得到了可读性和可理解性都非常好的代码。扮演一个角色，构建一个关联用户，准备将其插入到数据库中，然后插入！

对于 **create_post** 我们做了同样的事情，除了用帖子和用户代替用户和角色！

## 修正我们的测试

我们将从修复**test/models/user _ test . exs**开始。我们需要做的第一件事是添加**别名 Pxblog。TestHelper** 添加到我们的模块定义的顶部，这样我们就可以使用我们之前创建的那些方便的助手。接下来，我们将在测试之前创建一个设置块来重用一个角色。

```
setup do
  {:ok, role}  = TestHelper.create_role(%{name: "user", admin: false})
  {:ok, role: role}
end
```

然后，在我们的第一个测试中，我们将根据我们的设置块中的角色键对其进行模式匹配。每当我们想要包含这个角色时，我们都要节省一点打字的时间，所以我们要编写一个助手函数并修改测试:

```
defp valid_attrs(role) do
  Map.put(@valid_attrs, :role_id, role.id)
endtest "changeset with valid attributes", %{role: role} do
  changeset = User.changeset(%User{}, valid_attrs(role))
  assert changeset.valid?
end
```

概括地说，我们对来自设置块的角色键进行模式匹配，然后我们修改了 **valid_attrs** 键，以便在我们的帮助器方法中包含一个有效的角色 id！当我们修改并运行这个规范时，我们现在应该回到绿色的**test/models/user*_*test . exs**规范！

接下来，打开**test/controllers/user _ controller*_*test . exs**，我们将使用相同的课程让这个文件再次通过。我们将添加一个**别名 Pxblog。Role** 语句，以及一个别名 Pxblog。TestHelper 语句，并添加设置代码来创建一个角色，并用 conn 返回。

```
setup do
  {:ok, user_role}  = TestHelper.create_role(%{name: "user", admin: false})
  {:ok, admin_role} = TestHelper.create_role(%{name: "admin", admin: true})
  {:ok, conn: build_conn(), user_role: user_role, admin_role: admin_role}
end
```

我们将添加一个名为 **valid_create_attrs** 的助手函数，它接受一个角色作为参数，并返回一个设置了 role_id 的新映射。

```
defp valid_create_attrs(role) do
  Map.put(@valid_create_attrs, :role_id, role.id)
end
```

最后，我们将修改我们的创建和更新操作，以使用这个新的助手和来自映射的 user_role 值的模式匹配。

```
test "creates resource and redirects when data is valid", %{conn: conn, user_role: user_role} do
  conn = post conn, user_path(conn, :create), user: valid_create_attrs(user_role)
  assert redirected_to(conn) == user_path(conn, :index)
  assert Repo.get_by(User, @valid_attrs)
endtest "updates chosen resource and redirects when data is valid", %{conn: conn, user_role: user_role} do
  user = Repo.insert! %User{}
  conn = put conn, user_path(conn, :update, user), user: valid_create_attrs(user_role)
  assert redirected_to(conn) == user_path(conn, :show, user)
  assert Repo.get_by(User, @valid_attrs)
end
```

我们的用户控制器测试现在应该都是绿色的了！可悲的是，运行混合测试仍然会给我们带来失败。

## 修复开机自检控制器测试

在我们的帖子控制器中，我们最终创建了许多帮助器函数，以便于我们与用户一起构建帖子，因此我们需要修改这些帮助器并添加角色的概念，以便我们可以创建有效的用户。我们将从添加对 **Pxblog 的引用开始。角色**在我们岗位控制员的顶端。在**test/controllers/post _ controller _ test . exs**中:

```
alias Pxblog.Role
alias Pxblog.TestHelper
```

然后，我们将创建我们的设置块，与我们在前面的设置块中所做的略有不同。

```
setup do
  {:ok, role} = TestHelper.create_role(%{name: "User Role", admin: false})
  {:ok, user} = TestHelper.create_user(role, %{email: "test@test.com", username: "testuser", password: "test", password_confirmation: "test"})
  {:ok, post} = TestHelper.create_post(user, %{title: "Test Post", body: "Test Body"})
  conn = build_conn() |> login_user(user)
  {:ok, conn: conn, user: user, role: role, post: post}
end
```

我们需要做的第一件事是创建一个角色，在这里一个标准的非管理员角色就可以了。在下一行中，我们还将创建一个使用该角色的用户。接下来，我们将为用户创建一个帖子。我们已经讨论了登录部分，所以我们将跳过它。最后，我们返回我们创建的所有新模型，以允许每个测试根据需要进行模式匹配。

我们有一个测试，我们也将修改，以获得一切绿色。我们的“为不同用户编辑帖子时的重定向”测试失败了，因为它试图在没有角色概念的情况下动态创建第二个用户。我们会稍微改变一下:

```
test "redirects when trying to edit a post for a different user", %{conn: conn, user: user, role: role, post: post} do
  {:ok, other_user} = TestHelper.create_user(role, %{email: "test2@test.com", username: "test2", password: "test", password_confirmation: "test"})
  conn = get conn, user_post_path(conn, :edit, other_user, post)
  assert get_flash(conn, :error) == "You are not authorized to modify that post!"
  assert redirected_to(conn) == page_path(conn, :index)
  assert conn.halted
end
```

因此，在这里我们将 role: role 位添加到我们的测试定义中，以在角色键上进行模式匹配，并将 other_user 创建位改为使用 TestHelper 并引用我们模式匹配的角色。

我们有一个快速重构的机会，因为我们包含了一个来自 TestHelper 的 post 对象，作为我们可以进行模式匹配的值之一。在我们之前调用 **build_post** 的任何地方，我们都可以删除，取而代之的是在我们的 post 对象上进行模式匹配。经过我们所有的修改后，完整的文件应该是:

```
defmodule Pxblog.PostControllerTest do
  use Pxblog.ConnCase alias Pxblog.Post
  alias Pxblog.TestHelper @valid_attrs %{body: "some content", title: "some content"}
  @invalid_attrs %{} setup do
    {:ok, role} = TestHelper.create_role(%{name: "User Role", admin: false})
    {:ok, user} = TestHelper.create_user(role, %{email: "test@test.com", username: "testuser", password: "test", password_confirmation: "test"})
    {:ok, post} = TestHelper.create_post(user, %{title: "Test Post", body: "Test Body"})
    conn = build_conn() |> login_user(user)
    {:ok, conn: conn, user: user, role: role, post: post}
  end defp login_user(conn, user) do
    post conn, session_path(conn, :create), user: %{username: user.username, password: user.password}
  end test "lists all entries on index", %{conn: conn, user: user} do
    conn = get conn, user_post_path(conn, :index, user)
    assert html_response(conn, 200) =~ "Listing posts"
  end test "renders form for new resources", %{conn: conn, user: user} do
    conn = get conn, user_post_path(conn, :new, user)
    assert html_response(conn, 200) =~ "New post"
  end test "creates resource and redirects when data is valid", %{conn: conn, user: user} do
    conn = post conn, user_post_path(conn, :create, user), post: @valid_attrs
    assert redirected_to(conn) == user_post_path(conn, :index, user)
    assert Repo.get_by(assoc(user, :posts), @valid_attrs)
  end test "does not create resource and renders errors when data is invalid", %{conn: conn, user: user} do
    conn = post conn, user_post_path(conn, :create, user), post: @invalid_attrs
    assert html_response(conn, 200) =~ "New post"
  end test "shows chosen resource", %{conn: conn, user: user, post: post} do
    conn = get conn, user_post_path(conn, :show, user, post)
    assert html_response(conn, 200) =~ "Show post"
  end test "renders page not found when id is nonexistent", %{conn: conn, user: user} do
    assert_error_sent 404, fn ->
      get conn, user_post_path(conn, :show, user, -1)
    end
  end test "renders form for editing chosen resource", %{conn: conn, user: user, post: post} do
    conn = get conn, user_post_path(conn, :edit, user, post)
    assert html_response(conn, 200) =~ "Edit post"
  end test "updates chosen resource and redirects when data is valid", %{conn: conn, user: user, post: post} do
    conn = put conn, user_post_path(conn, :update, user, post), post: @valid_attrs
    assert redirected_to(conn) == user_post_path(conn, :show, user, post)
    assert Repo.get_by(Post, @valid_attrs)
  end test "does not update chosen resource and renders errors when data is invalid", %{conn: conn, user: user, post: post} do
    conn = put conn, user_post_path(conn, :update, user, post), post: %{"body" => nil}
    assert html_response(conn, 200) =~ "Edit post"
  end test "deletes chosen resource", %{conn: conn, user: user, post: post} do
    conn = delete conn, user_post_path(conn, :delete, user, post)
    assert redirected_to(conn) == user_post_path(conn, :index, user)
    refute Repo.get(Post, post.id)
  end test "redirects when the specified user does not exist", %{conn: conn} do
    conn = get conn, user_post_path(conn, :index, -1)
    assert get_flash(conn, :error) == "Invalid user!"
    assert redirected_to(conn) == page_path(conn, :index)
    assert conn.halted
  end test "redirects when trying to edit a post for a different user", %{conn: conn, role: role, post: post} do
    {:ok, other_user} = TestHelper.create_user(role, %{email: "test2@test.com", username: "test2", password: "test", password_confirmation: "test"})
    conn = get conn, user_post_path(conn, :edit, other_user, post)
    assert get_flash(conn, :error) == "You are not authorized to modify that post!"
    assert redirected_to(conn) == page_path(conn, :index)
    assert conn.halted
  end
end
```

## 修复会话控制器测试

**test/controllers/session _ controller _ test . exs**也有一些失败的测试，因为我们没有更新它来使用我们的 TestHelper。我们将在顶部添加一个别名，并修改设置块，就像我们在其他地方做的那样:

```
defmodule Pxblog.SessionControllerTest do
  use Pxblog.ConnCase alias Pxblog.User
  alias Pxblog.TestHelper setup do
    {:ok, role} = TestHelper.create_role(%{name: "User", admin: false})
    {:ok, _user} = TestHelper.create_user(role, %{username: "test", password: "test", password_confirmation: "test", email: "test@test.com"})
    {:ok, conn: build_conn()}
  end
```

这应该足以让这些测试通过！万岁！

## 修正我们剩下的测试

我们还有两项测试失败。让我们得到那些绿色！

```
1) test current user returns the user in the session (Pxblog.LayoutViewTest)
 test/views/layout_view_test.exs:13
 Expected truthy, got nil
 code: LayoutView.current_user(conn)
 stacktrace:
 test/views/layout_view_test.exs:152) test current user returns nothing if there is no user in the session (Pxblog.LayoutViewTest)
 test/views/layout_view_test.exs:18
 ** (ArgumentError) cannot convert nil to param
 stacktrace:
 (phoenix) lib/phoenix/param.ex:67: Phoenix.Param.Atom.to_param/1
 (pxblog) web/router.ex:1: Pxblog.Router.Helpers.session_path/4
 test/views/layout_view_test.exs:20
```

打开**test/views/layout _ view _ test . exs**，在顶部我们看到一个没有角色的用户被创建！在我们的 setup 块中，我们也没有传递我们创建的用户，所以我们必须一遍又一遍地查找它！恶心！我们将重构整个文件:

```
defmodule Pxblog.LayoutViewTest do
  use Pxblog.ConnCase, async: true alias Pxblog.LayoutView
  alias Pxblog.TestHelper setup do
    {:ok, role} = TestHelper.create_role(%{name: "User Role", admin: false})
    {:ok, user} = TestHelper.create_user(role, %{email: "[test@test.com](mailto:test@test.com)", username: "testuser", password: "test", password_confirmation: "test"})
    {:ok, conn: build_conn(), user: user}
  end test "current user returns the user in the session", %{conn: conn, user: user} do
    conn = post conn, session_path(conn, :create), user: %{username: user.username, password: user.password}
    assert LayoutView.current_user(conn)
  end test "current user returns nothing if there is no user in the session", %{conn: conn, user: user} do
    conn = delete conn, session_path(conn, :delete, user)
    refute LayoutView.current_user(conn)
  end
end
```

我们为我们的角色模型添加一个别名，创建一个有效的角色，用该角色创建一个有效的用户，然后返回带有 conn 的用户。现在，运行混合测试和…

一切都是绿色的！但是当运行我们的测试时，我们得到了一些警告(因为我们把一切都做得如此可爱和干净)。

```
test/controllers/post_controller_test.exs:20: warning: function create_user/0 is unused
test/views/layout_view_test.exs:6: warning: unused alias Role
test/views/layout_view_test.exs:5: warning: unused alias User
test/controllers/user_controller_test.exs:5: warning: unused alias Role
test/controllers/post_controller_test.exs:102: warning: variable user is unused
test/controllers/post_controller_test.exs:6: warning: unused alias Role
```

只需进入每个文件并删除不合适的别名和函数，因为我们不再需要它们了！

```
$ mix test
.........................................Finished in 0.4 seconds
41 tests, 0 failuresRandomized with seed 588307
```

## 创建管理种子

最终，我们将限制新用户的创建仅限于管理员。然而，对我们来说，这样做意味着我们将处于一个奇怪的第 22 条军规的状态，我们没有成员或管理员，因此意味着我们不能创建成员或管理员，等等。我们将通过为默认管理员用户提供种子来解决这个问题。打开 **priv/repo/seeds.exs** 并插入以下代码:

```
alias Pxblog.Repo
alias Pxblog.Role
alias Pxblog.Userrole = %Role{}
  |> Role.changeset(%{name: "Admin Role", admin: true})
  |> Repo.insert!admin = %User{}
  |> User.changeset(%{username: "admin", email: "admin@test.com", password: "test", password_confirmation: "test", role_id: role.id})
  |> Repo.insert!
```

然后，我们将通过调用以下命令来运行种子文件:

```
$ mix run priv/repo/seeds.exs
```

## 接下来

现在，我们已经设置好了模型，并准备好处理我们的角色，我们的测试也变回绿色，我们需要开始修改控制器中的功能，以限制某些操作，除非您是适当的用户或管理员。在下一篇文章中，我们将探索如何最好地实现这个功能，如何添加一个助手模块，当然，我们将保持我们的测试绿色！

## 本系列的下一篇文章

[](/@diamondgfx/writing-a-blog-engine-in-phoenix-and-elixir-part-4-adding-roles-to-our-controllers-9f4678b48468) [## 用 Phoenix 和 Elixir 编写博客引擎:第 4 部分，向控制器添加角色

### 最新更新:2016 年 1 月 26 日

medium.com](/@diamondgfx/writing-a-blog-engine-in-phoenix-and-elixir-part-4-adding-roles-to-our-controllers-9f4678b48468) 

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