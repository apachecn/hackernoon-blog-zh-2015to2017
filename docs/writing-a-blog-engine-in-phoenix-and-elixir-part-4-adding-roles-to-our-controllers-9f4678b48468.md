# 用 Phoenix 和 Elixir 编写博客引擎:第 4 部分，向控制器添加角色

> 原文：<https://medium.com/hackernoon/writing-a-blog-engine-in-phoenix-and-elixir-part-4-adding-roles-to-our-controllers-9f4678b48468>

**最新更新:**2016 年 8 月 17 日

## 本系列的前一篇文章

 [## 用 Phoenix 和 Elixir 编写博客引擎:第 3 部分，向模型添加角色

### 最新更新:2016 年 7 月 21 日

medium.com](/@diamondgfx/writing-a-blog-engine-in-phoenix-and-elixir-part-3-adding-roles-to-our-models-3be45a4afe4b) 

## 当前版本

在撰写本文时，我们应用程序的当前版本是:

*   **仙丹** : v1.3.1
*   **凤凰:** v1.2.0
*   **Ecto:** v2.0.2
*   **康美宁:** v2.5.2

如果你正在读这篇文章，而这些不是最新的，请告诉我，我会相应地更新这篇教程。

## 我们离开的地方

当我们停止时，我们刚刚完成了在模型中实现角色的概念，并创建了一些测试助手来简化我们的工作，但是现在我们需要进入在控制器中实现基于角色的限制的棘手部分。我们将首先创建一个助手，为我们提供一个在每个控制器中使用的函数。

## 创建角色检查器助手

我们需要做的下一件事是创建一个简单的方法来验证创建其他用户的用户是否确实是管理员。创建**web/models/role _ checker . ex**，我们将用以下模块填充它:

```
defmodule Pxblog.RoleChecker do
  alias Pxblog.Repo
  alias Pxblog.Role

  def is_admin?(user) do
    (role = Repo.get(Role, user.role_id)) && role.admin
  end
end
```

我们还将编写一些测试来涵盖这一功能。打开**测试/模型/角色 _ 检查器 _ 测试. exs** :

```
defmodule Pxblog.RoleCheckerTest do
  use Pxblog.ModelCase
  alias Pxblog.TestHelper
  alias Pxblog.RoleChecker

  test "is_admin? is true when user has an admin role" do
    {:ok, role} = TestHelper.create_role(%{name: "Admin", admin: true})
    {:ok, user} = TestHelper.create_user(role, %{email: "test@test.com", username: "user", password: "test", password_confirmation: "test"})
    assert RoleChecker.is_admin?(user)
  end

  test "is_admin? is false when user does not have an admin role" do
    {:ok, role} = TestHelper.create_role(%{name: "User", admin: false})
    {:ok, user} = TestHelper.create_user(role, %{email: "test@test.com", username: "user", password: "test", password_confirmation: "test"})
    refute RoleChecker.is_admin?(user)
  end
end
```

在这两个测试中，我们都创建了一个角色和一个用户；在一个示例中，我们创建了一个管理员角色，而在下一个示例中，我们没有创建。最后，我们断言 is_admin？函数为管理员用户返回 true，为非管理员用户返回 false。因为角色检查器的**是 _admin？**功能要求你供给用户，我们可以编写非常简单的测试来保证我们的功能。这是我们可以放心的代码！运行这些测试，并验证您的测试套件仍然是绿色的。

## 将用户创建限制为管理员

在我们的用户控制器中，我们从未编写任何 authorize_user 插件来限制这一点，所以我们现在要添加它们。我们将很快设计出来，以确保我们正在执行的行动是有意义的。我们将允许用户编辑、更新和删除他们自己的帐户，但我们只允许管理员查看新用户表单或创建新帐户。

在**web/controllers/user _ controller . ex**中的 **scrub_params** 行下面，添加以下内容:

```
plug :authorize_admin when action in [:new, :create]
plug :authorize_user when action in [:edit, :update, :delete]
```

在底部，我们将添加一些私有函数来处理授权用户和授权管理员。

```
defp authorize_user(conn, _) do
  user = get_session(conn, :current_user)
  if user && (Integer.to_string(user.id) == conn.params["id"] || Pxblog.RoleChecker.is_admin?(user)) do
    conn
  else
    conn
    |> put_flash(:error, "You are not authorized to modify that user!")
    |> redirect(to: page_path(conn, :index))
    |> halt()
  end
end

defp authorize_admin(conn, _) do
  user = get_session(conn, :current_user)
  if user && Pxblog.RoleChecker.is_admin?(user) do
    conn
  else
    conn
    |> put_flash(:error, "You are not authorized to create new users!")
    |> redirect(to: page_path(conn, :index))
    |> halt()
  end
end
```

这个 **authorize_user** 调用基本上与我们的 Post 控制器相同，除了上面的 if 语句也检查我们新的 **RoleChecker.is_admin？**呼。

**authorize_admin** 更简单；我们只是检查当前用户是否是管理员。

为了验证这一切工作正常，我们将返回到我们的**test/controllers/user _ controller _ test . exs**文件，并修改我们的测试，使其能够针对这些新的假设工作。

首先，我们必须改变我们的安装程序块，以适应这些新规则。

```
setup do
  {:ok, user_role}     = TestHelper.create_role(%{name: "user", admin: false})
  {:ok, nonadmin_user} = TestHelper.create_user(user_role, %{email: "nonadmin@test.com", username: "nonadmin", password: "test", password_confirmation: "test"})

  {:ok, admin_role}    = TestHelper.create_role(%{name: "admin", admin: true})
  {:ok, admin_user}    = TestHelper.create_user(admin_role, %{email: "admin@test.com", username: "admin", password: "test", password_confirmation: "test"})

  {:ok, conn: build_conn(), admin_role: admin_role, user_role: user_role, nonadmin_user: nonadmin_user, admin_user: admin_user}
end
```

我们创建一个用户角色、一个管理员角色、一个非管理员用户和一个管理员用户，然后将所有这些返回到我们的测试中，以便在模式匹配中使用。我们还需要一个助手函数来登录一个用户，所以我们将从我们的 Post 控制器中复制 login_user 函数。

```
defp login_user(conn, user) do
  post conn, session_path(conn, :create), user: %{username: user.username, password: user.password}
end
```

我们没有对 index 附加任何限制，所以我们可以跳过这个测试。下一个测试是我们的“为新资源呈现表单”，这是我们的新动作，有一个限制(必须是管理员)。

将该测试更改为以下代码:

```
@tag admin: true
test "renders form for new resources", %{conn: conn, admin_user: admin_user} do
  conn = conn
    |> login_user(admin_user)
    |> get(user_path(conn, :new))
  assert html_response(conn, 200) =~ "New user"
end
```

我们在测试上方添加了一个“@tag admin: true”行，将其标记为“admin”测试，这样我们就可以运行所有的 admin 测试，而不是整个套件。我们将使用以下命令运行这个测试:

```
mix test --only admin
```

在我们的输出中，我们应该看到 GRE-啊哦！我们失败了:

```
1) test renders form for new resources (Pxblog.UserControllerTest)
 test/controllers/user_controller_test.exs:26
 ** (KeyError) key :role_id not found in: %{id: 348, username: “admin”}
 stacktrace:
 (pxblog) web/models/role_checker.ex:6: Pxblog.RoleChecker.is_admin?/1
 (pxblog) web/controllers/user_controller.ex:84: Pxblog.UserController.authorize_admin/2
 (pxblog) web/controllers/user_controller.ex:1: Pxblog.UserController.phoenix_controller_pipeline/2
 (pxblog) lib/phoenix/router.ex:255: Pxblog.Router.dispatch/2
 (pxblog) web/router.ex:1: Pxblog.Router.do_call/2
 (pxblog) lib/pxblog/endpoint.ex:1: Pxblog.Endpoint.phoenix_pipeline/1
 (pxblog) lib/phoenix/endpoint/render_errors.ex:34: Pxblog.Endpoint.call/2
 (phoenix) lib/phoenix/test/conn_test.ex:193: Phoenix.ConnTest.dispatch/5
 test/controllers/user_controller_test.exs:28
```

这里的问题是我们没有将一个完整的用户模型传递给 **RoleChecker.is_admin？**；相反，我们通过会话控制器的 sign_in 函数传递存储在 current_user 中的一小部分数据。我们将对其进行更新，以包含 role_id。我在下面的**web/controllers/session _ controller . ex**中添加了修改:

```
defp sign_in(user, password, conn) do
  if checkpw(password, user.password_digest) do
    conn
    |> put_session(:current_user, %{id: user.id, username: user.username, role_id: user.role_id})
    |> put_flash(:info, "Sign in successful!")
    |> redirect(to: page_path(conn, :index))
  else
    failed_login(conn)
  end
end
```

现在我们将运行我们的混合测试命令，只针对管理员标记的测试。

```
$ mix test --only admin
```

又变绿了！现在，我们需要创建一个否定测试，用于测试用户不是管理员，但试图访问用户的“新建”操作。回到**test/controllers/user _ controller _ test . exs**:

```
@tag admin: true
test "redirects from new form when not admin", %{conn: conn, nonadmin_user: nonadmin_user} do
  conn = login_user(conn, nonadmin_user)
  conn = get conn, user_path(conn, :new)
  assert get_flash(conn, :error) == "You are not authorized to create new users!"
  assert redirected_to(conn) == page_path(conn, :index)
  assert conn.halted
end
```

我们将对创建操作进行同样的操作；创建一个有效测试和一个无效测试。

```
@tag admin: true
test "creates resource and redirects when data is valid", %{conn: conn, user_role: user_role, admin_user: admin_user} do
  conn = login_user(conn, admin_user)
  conn = post conn, user_path(conn, :create), user: valid_create_attrs(user_role)
  assert redirected_to(conn) == user_path(conn, :index)
  assert Repo.get_by(User, @valid_attrs)
end

@tag admin: true
test "redirects from creating user when not admin", %{conn: conn, user_role: user_role, nonadmin_user: nonadmin_user} do
  conn = login_user(conn, nonadmin_user)
  conn = post conn, user_path(conn, :create), user: valid_create_attrs(user_role)
  assert get_flash(conn, :error) == "You are not authorized to create new users!"
  assert redirected_to(conn) == page_path(conn, :index)
  assert conn.halted
end

@tag admin: true
test "does not create resource and renders errors when data is invalid", %{conn: conn, admin_user: admin_user} do
  conn = login_user(conn, admin_user)
  conn = post conn, user_path(conn, :create), user: @invalid_attrs
  assert html_response(conn, 200) =~ "New user"
end
```

我们可以跳过节目，因为我们没有附加任何新的条件。我们将反复遵循这种模式，直到我们完成的**user _ controller*_*test . exs**文件看起来像这样:

```
defmodule Pxblog.UserControllerTest do
  use Pxblog.ConnCase
  alias Pxblog.User
  alias Pxblog.TestHelper

  @valid_create_attrs %{email: "test@test.com", username: "test", password: "test", password_confirmation: "test"}
  @valid_attrs %{email: "test@test.com", username: "test"}
  @invalid_attrs %{}

  setup do
    {:ok, user_role}     = TestHelper.create_role(%{name: "user", admin: false})
    {:ok, nonadmin_user} = TestHelper.create_user(user_role, %{email: "nonadmin@test.com", username: "nonadmin", password: "test", password_confirmation: "test"})

{:ok, admin_role}    = TestHelper.create_role(%{name: "admin", admin: true})
    {:ok, admin_user}    = TestHelper.create_user(admin_role, %{email: "admin@test.com", username: "admin", password: "test", password_confirmation: "test"})

    {:ok, conn: build_conn(), admin_role: admin_role, user_role: user_role, nonadmin_user: nonadmin_user, admin_user: admin_user}
  end

  defp valid_create_attrs(role) do
    Map.put(@valid_create_attrs, :role_id, role.id)
  end

  defp login_user(conn, user) do
    post conn, session_path(conn, :create), user: %{username: user.username, password: user.password}
  end

  test "lists all entries on index", %{conn: conn} do
    conn = get conn, user_path(conn, :index)
    assert html_response(conn, 200) =~ "Listing users"
  end

  @tag admin: true
  test "renders form for new resources", %{conn: conn, admin_user: admin_user} do
    conn = login_user(conn, admin_user)
    conn = get conn, user_path(conn, :new)
    assert html_response(conn, 200) =~ "New user"
  end

  @tag admin: true
  test "redirects from new form when not admin", %{conn: conn, nonadmin_user: nonadmin_user} do
    conn = login_user(conn, nonadmin_user)
    conn = get conn, user_path(conn, :new)
    assert get_flash(conn, :error) == "You are not authorized to create new users!"
    assert redirected_to(conn) == page_path(conn, :index)
    assert conn.halted
  end

  @tag admin: true
  test "creates resource and redirects when data is valid", %{conn: conn, user_role: user_role, admin_user: admin_user} do
    conn = login_user(conn, admin_user)
    conn = post conn, user_path(conn, :create), user: valid_create_attrs(user_role)
    assert redirected_to(conn) == user_path(conn, :index)
    assert Repo.get_by(User, @valid_attrs)
  end

  @tag admin: true
  test "redirects from creating user when not admin", %{conn: conn, user_role: user_role, nonadmin_user: nonadmin_user} do
    conn = login_user(conn, nonadmin_user)
    conn = post conn, user_path(conn, :create), user: valid_create_attrs(user_role)
    assert get_flash(conn, :error) == "You are not authorized to create new users!"
    assert redirected_to(conn) == page_path(conn, :index)
    assert conn.halted
  end

  @tag admin: true
  test "does not create resource and renders errors when data is invalid", %{conn: conn, admin_user: admin_user} do
    conn = login_user(conn, admin_user)
    conn = post conn, user_path(conn, :create), user: @invalid_attrs
    assert html_response(conn, 200) =~ "New user"
  end

  test "shows chosen resource", %{conn: conn} do
    user = Repo.insert! %User{}
    conn = get conn, user_path(conn, :show, user)
    assert html_response(conn, 200) =~ "Show user"
  end

  test "renders page not found when id is nonexistent", %{conn: conn} do
    assert_error_sent 404, fn ->
      get conn, user_path(conn, :show, -1)
    end
  end

  @tag admin: true
  test "renders form for editing chosen resource when logged in as that user", %{conn: conn, nonadmin_user: nonadmin_user} do
    conn = login_user(conn, nonadmin_user)
    conn = get conn, user_path(conn, :edit, nonadmin_user)
    assert html_response(conn, 200) =~ "Edit user"
  end

  @tag admin: true
  test "renders form for editing chosen resource when logged in as an admin", %{conn: conn, admin_user: admin_user, nonadmin_user: nonadmin_user} do
    conn = login_user(conn, admin_user)
    conn = get conn, user_path(conn, :edit, nonadmin_user)
    assert html_response(conn, 200) =~ "Edit user"
  end

  @tag admin: true
  test "redirects away from editing when logged in as a different user", %{conn: conn, nonadmin_user: nonadmin_user, admin_user: admin_user} do
    conn = login_user(conn, nonadmin_user)
    conn = get conn, user_path(conn, :edit, admin_user)
    assert get_flash(conn, :error) == "You are not authorized to modify that user!"
    assert redirected_to(conn) == page_path(conn, :index)
    assert conn.halted
  end

  @tag admin: true
  test "updates chosen resource and redirects when data is valid when logged in as that user", %{conn: conn, nonadmin_user: nonadmin_user} do
    conn = login_user(conn, nonadmin_user)
    conn = put conn, user_path(conn, :update, nonadmin_user), user: @valid_create_attrs
    assert redirected_to(conn) == user_path(conn, :show, nonadmin_user)
    assert Repo.get_by(User, @valid_attrs)
  end

  @tag admin: true
  test "updates chosen resource and redirects when data is valid when logged in as an admin", %{conn: conn, admin_user: admin_user} do
    conn = login_user(conn, admin_user)
    conn = put conn, user_path(conn, :update, admin_user), user: @valid_create_attrs
    assert redirected_to(conn) == user_path(conn, :show, admin_user)
    assert Repo.get_by(User, @valid_attrs)
  end

  @tag admin: true
  test "does not update chosen resource when logged in as different user", %{conn: conn, nonadmin_user: nonadmin_user, admin_user: admin_user} do
    conn = login_user(conn, nonadmin_user)
    conn = put conn, user_path(conn, :update, admin_user), user: @valid_create_attrs
    assert get_flash(conn, :error) == "You are not authorized to modify that user!"
    assert redirected_to(conn) == page_path(conn, :index)
    assert conn.halted
  end

  @tag admin: true
  test "does not update chosen resource and renders errors when data is invalid", %{conn: conn, nonadmin_user: nonadmin_user} do
    conn = login_user(conn, nonadmin_user)
    conn = put conn, user_path(conn, :update, nonadmin_user), user: @invalid_attrs
    assert html_response(conn, 200) =~ "Edit user"
  end

  @tag admin: true
  test "deletes chosen resource when logged in as that user", %{conn: conn, user_role: user_role} do
    {:ok, user} = TestHelper.create_user(user_role, @valid_create_attrs)
    conn =
      login_user(conn, user)
      |> delete(user_path(conn, :delete, user))
    assert redirected_to(conn) == user_path(conn, :index)
    refute Repo.get(User, user.id)
  end

  @tag admin: true
  test "deletes chosen resource when logged in as an admin", %{conn: conn, user_role: user_role, admin_user: admin_user} do
    {:ok, user} = TestHelper.create_user(user_role, @valid_create_attrs)
    conn =
      login_user(conn, admin_user)
      |> delete(user_path(conn, :delete, user))
    assert redirected_to(conn) == user_path(conn, :index)
    refute Repo.get(User, user.id)
  end

  @tag admin: true
  test "redirects away from deleting chosen resource when logged in as a different user", %{conn: conn, user_role: user_role, nonadmin_user: nonadmin_user} do
    {:ok, user} = TestHelper.create_user(user_role, @valid_create_attrs)
    conn =
      login_user(conn, nonadmin_user)
      |> delete(user_path(conn, :delete, user))
    assert get_flash(conn, :error) == "You are not authorized to modify that user!"
    assert redirected_to(conn) == page_path(conn, :index)
    assert conn.halted
  end
end
```

现在，我们运行我们的完整测试套件，我们都回到绿色！

## 允许管理员修改所有帖子

令人欣慰的是，我们已经做了几乎所有的工作，使这最后一块管理功能的工作符合预期。我们将打开**web/controllers/post _ controller . ex**并修改 **authorize_user** 函数以使用我们的 **RoleChecker.is_admin？**帮助功能，查看用户是否是管理员。如果是的话，我们将给予他们修改任何用户帖子的完全控制权。

```
defp authorize_user(conn, _) do
  user = get_session(conn, :current_user)
  if user && (Integer.to_string(user.id) == conn.params["user_id"] || Pxblog.RoleChecker.is_admin?(user)) do
    conn
  else
    conn
    |> put_flash(:error, "You are not authorized to modify that post!")
    |> redirect(to: page_path(conn, :index))
    |> halt()
  end
end
```

最后，我们将打开**test/controllers/post _ controller _ test . exs**，并在底部添加更多测试，涵盖我们的授权规则:

```
test "redirects when trying to delete a post for a different user", %{conn: conn, role: role, post: post} do
  {:ok, other_user} = TestHelper.create_user(role, %{email: "test2@test.com", username: "test2", password: "test", password_confirmation: "test"})
  conn = delete conn, user_post_path(conn, :delete, other_user, post)
  assert get_flash(conn, :error) == "You are not authorized to modify that post!"
  assert redirected_to(conn) == page_path(conn, :index)
  assert conn.halted
end

test "renders form for editing chosen resource when logged in as admin", %{conn: conn, user: user, post: post} do
  {:ok, role}  = TestHelper.create_role(%{name: "Admin", admin: true})
  {:ok, admin} = TestHelper.create_user(role, %{username: "admin", email: "admin@test.com", password: "test", password_confirmation: "test"})
  conn =
    login_user(conn, admin)
    |> get(user_post_path(conn, :edit, user, post))
  assert html_response(conn, 200) =~ "Edit post"
end

test "updates chosen resource and redirects when data is valid when logged in as admin", %{conn: conn, user: user, post: post} do
  {:ok, role}  = TestHelper.create_role(%{name: "Admin", admin: true})
  {:ok, admin} = TestHelper.create_user(role, %{username: "admin", email: "admin@test.com", password: "test", password_confirmation: "test"})
  conn =
    login_user(conn, admin)
    |> put(user_post_path(conn, :update, user, post), post: @valid_attrs)
  assert redirected_to(conn) == user_post_path(conn, :show, user, post)
  assert Repo.get_by(Post, @valid_attrs)
end

test "does not update chosen resource and renders errors when data is invalid when logged in as admin", %{conn: conn, user: user, post: post} do
  {:ok, role}  = TestHelper.create_role(%{name: "Admin", admin: true})
  {:ok, admin} = TestHelper.create_user(role, %{username: "admin", email: "admin@test.com", password: "test", password_confirmation: "test"})
  conn =
    login_user(conn, admin)
    |> put(user_post_path(conn, :update, user, post), post: %{"body" => nil})
  assert html_response(conn, 200) =~ "Edit post"
end

test "deletes chosen resource when logged in as admin", %{conn: conn, user: user, post: post} do
  {:ok, role}  = TestHelper.create_role(%{name: "Admin", admin: true})
  {:ok, admin} = TestHelper.create_user(role, %{username: "admin", email: "admin@test.com", password: "test", password_confirmation: "test"})
  conn =
    login_user(conn, admin)
    |> delete(user_post_path(conn, :delete, user, post))
  assert redirected_to(conn) == user_post_path(conn, :index, user)
  refute Repo.get(Post, post.id)
end
```

现在，我们的博客引擎正在嗡嗡作响，但还是有一些 bug，不管是由于遗漏还是我一路上错过的东西，所以让我们确定并解决一些 bug。我们还将升级依赖关系的版本，以确保这是运行在最新和最好的一切，它可以！

## 添加新用户会引发关于缺少角色的错误

这是由[https://hexdocs.pm/phoenix_html/Phoenix.HTML.Form.html#select/4](https://medium.com/u/3c497d21a78#select/4) ):

```
select(form, field, values, opts \\ [])
  Generates a select tag with the given values.
```

对于**值**参数，选择框需要列表或关键字列表，其形式为*[值，值，值]* 或*[显示:值，显示:值]* 。在我们的例子中，我们希望显示角色名，但是让它在表单 submit 中携带 id 值。我们不能盲目地将@roles 放入其中，因为它不符合任何一种格式，所以让我们在我们的视图中编写一个函数来简化它:

```
defmodule Pxblog.UserView do
  use Pxblog.Web, :view

  def roles_for_select(roles) do
    roles
    |> Enum.map(&["#{&1.name}": &1.id])
    |> List.flatten
  end
end
```

我们添加了一个 **roles_for_select** 函数，它只接受一组角色。让我们一行一行地探索这个函数的作用。我们从我们的系列开始，然后将它输送到下一个系列:

```
Enum.map(&["#{&1.name}": &1.id])
```

同样，记住&/&1 是匿名函数的简写语法，所以如果我们放弃管道操作和简写，我们会看到这个函数被重写为:

```
Enum.map(roles, fn role -> ["#{role.name}": role.id] end)
```

我们运行 map 操作来返回一个较小的关键字列表，其中角色的名称是键，角色的 id 是值。

给定以下角色的特定起始值:

```
roles = [%Role{name: "Admin Role", id: 1}, %Role{name: "User Role", id: 2}]
```

这个 map 调用将返回:

```
[["Admin Role": 1], ["User Role": 2]]
```

然后我们把它放到最后一个调用中， **List.flatten** 把它压缩成一个方便的列表，而不是一个列表列表。所以我们的最终结果是:

```
["Admin Role": 1, "User Role": 2]
```

这恰好是选择表单助手所期望的格式！我们还不能沾沾自喜；我们还需要修改**网页/模板/用户/new.html.eex:** 的模板

```
<h2>New user</h2>

<%= render "form.html", changeset: @changeset,
                        action: user_path(@conn, :create),
                        roles: @roles %>

<%= link "Back", to: user_path(@conn, :index) %>
```

以及**web/templates/user/edit . html . eex**:

```
<h2>Edit user</h2><%= render "form.html", changeset: @changeset,
                        action: user_path(@conn, :update, @user),
                        roles: @roles %><%= link "Back", to: user_path(@conn, :index) %>
```

最后，在**web/templates/user/form . html . eex**中，您将希望使用我们的助手和角色分配添加到我们的新选择框中。我们希望添加一个选择框，包含用户可以进入的每个角色。在提交按钮前添加以下内容:

```
<div class="form-group">
  <%= label f, :role_id, "Role", class: "control-label" %>
  <%= select f, :role_id, roles_for_select(@roles), class: "form-control" %>
  <%= error_tag f, :role_id %>
</div>
```

现在，如果您尝试添加新用户或编辑现有用户，您将能够为该用户分配一个角色！我们的清单上少了一个 bug！

## 多次运行我们的种子会复制数据

现在，如果我们多次运行我们的种子，我们最终会错误地复制数据，这是不好的。让我们实现几个助手 **find_or_create** 匿名函数:

```
alias Pxblog.Repo
alias Pxblog.Role
alias Pxblog.User
import Ecto.Query, only: [from: 2]

find_or_create_role = fn role_name, admin ->
  case Repo.all(from r in Role, where: r.name == ^role_name and r.admin == ^admin) do
    [] ->
      %Role{}
      |> Role.changeset(%{name: role_name, admin: admin})
      |> Repo.insert!()
    _ ->
      IO.puts "Role: #{role_name} already exists, skipping"
  end
end

find_or_create_user = fn username, email, role ->
  case Repo.all(from u in User, where: u.username == ^username and u.email == ^email) do
    [] ->
      %User{}
      |> User.changeset(%{username: username, email: email, password: "test", password_confirmation: "test", role_id: role.id})
      |> Repo.insert!()
    _ ->
      IO.puts "User: #{username} already exists, skipping"
  end
end

_user_role  = find_or_create_role.("User Role", false)
admin_role  = find_or_create_role.("Admin Role", true)
_admin_user = find_or_create_user.("admin", "admin@test.com", admin_role)
```

首先要注意的是，我们混淆了我们的 Repo、Role 和 User，并且我们还从 Ecto 的函数中导入了**。查询以使用 linq 样式的查询语法。接下来，我们将看看 **find_or_create_role** 匿名函数。该函数本身只接受一个角色名和一个管理标志作为其参数。在此基础上，我们使用 Repo.all 查询这些标准(注意 where 子句中每个变量旁边的^；我们不想在这里做任何模式匹配或任何事情)并将其放入 case 语句中。如果我们用 Repo.all 找不到任何东西，我们将得到一个空列表，因此如果我们得到一个空列表，我们将插入角色。否则，我们假设我们已经获得了一些匹配条件，我们将承认它已经存在，并继续处理种子文件的其余部分。 **find_or_create_user** 执行相同的操作，但只是寻找不同的标准。**

最后，我们调用这些函数中的每一个(注意。在函数名和参数之间；这是匿名函数调用所必需的！).我们需要重用 admin 角色来创建 admin 用户，所以我们没有在 admin_role 前面加下划线。我们以后可能会决定为以后的种子保留 user_role 或 admin 用户，所以我将保留这些代码，但在它们前面加上下划线。它使种子文件看起来又好又干净。现在已经完成了，我们准备运行我们的种子:

```
$ mix run priv/repo/seeds.exs
[debug] SELECT r0.”id”, r0.”name”, r0.”admin”, r0.”inserted_at”, r0.”updated_at” FROM “roles” AS r0 WHERE ((r0.”name” = $1) AND (r0.”admin” = $2)) [“User Role”, false] OK query=81.7ms queue=2.8ms
[debug] BEGIN [] OK query=0.2ms
[debug] INSERT INTO “roles” (“admin”, “inserted_at”, “name”, “updated_at”) VALUES ($1, $2, $3, $4) RETURNING “id” [false, {{2015, 11, 6}, {19, 35, 49, 0}}, “User Role”, {{2015, 11, 6}, {19, 35, 49, 0}}] OK query=0.8ms
[debug] COMMIT [] OK query=0.4ms
[debug] SELECT r0.”id”, r0.”name”, r0.”admin”, r0.”inserted_at”, r0.”updated_at” FROM “roles” AS r0 WHERE ((r0.”name” = $1) AND (r0.”admin” = $2)) [“Admin Role”, true] OK query=0.4ms
[debug] BEGIN [] OK query=0.2ms
[debug] INSERT INTO “roles” (“admin”, “inserted_at”, “name”, “updated_at”) VALUES ($1, $2, $3, $4) RETURNING “id” [true, {{2015, 11, 6}, {19, 35, 49, 0}}, “Admin Role”, {{2015, 11, 6}, {19, 35, 49, 0}}] OK query=0.4ms
[debug] COMMIT [] OK query=0.3ms
[debug] SELECT u0.”id”, u0.”username”, u0.”email”, u0.”password_digest”, u0.”role_id”, u0.”inserted_at”, u0.”updated_at” FROM “users” AS u0 WHERE ((u0.”username” = $1) AND (u0.”email” = $2)) [“admin”, “[admin@test.com](mailto:admin@test.com)”] OK query=0.7ms
[debug] BEGIN [] OK query=0.3ms
[debug] INSERT INTO “users” (“email”, “inserted_at”, “password_digest”, “role_id”, “updated_at”, “username”) VALUES ($1, $2, $3, $4, $5, $6) RETURNING “id” [“[admin@test.com](mailto:admin@test.com)”, {{2015, 11, 6}, {19, 35, 49, 0}}, “$2b$12$.MuPBUVe/7/9HSOsccJYUOAD5IKEB77Pgz2oTJ/UvTvWYwAGn/L.i”, 2, {{2015, 11, 6}, {19, 35, 49, 0}}, “admin”] OK query=1.2ms
[debug] COMMIT [] OK query=1.1ms
```

我们第一次运行它时，看到一堆 insert 语句！太棒了。为了确保一切正常，让我们再运行一次，验证我们没有看到任何插入:

```
$ mix run priv/repo/seeds.exs
Role: User Role already exists, skipping
[debug] SELECT r0.”id”, r0.”name”, r0.”admin”, r0.”inserted_at”, r0.”updated_at” FROM “roles” AS r0 WHERE ((r0.”name” = $1) AND (r0.”admin” = $2)) [“User Role”, false] OK query=104.8ms queue=3.6ms
Role: Admin Role already exists, skipping
[debug] SELECT r0.”id”, r0.”name”, r0.”admin”, r0.”inserted_at”, r0.”updated_at” FROM “roles” AS r0 WHERE ((r0.”name” = $1) AND (r0.”admin” = $2)) [“Admin Role”, true] OK query=0.6ms
User: admin already exists, skipping
[debug] SELECT u0.”id”, u0.”username”, u0.”email”, u0.”password_digest”, u0.”role_id”, u0.”inserted_at”, u0.”updated_at” FROM “users” AS u0 WHERE ((u0.”username” = $1) AND (u0.”email” = $2)) [“admin”, “[admin@test.com](mailto:admin@test.com)”] OK query=0.8ms
```

太好了！一切正常，安全多了！此外，我们还可以为 Ecto 编写自己的实用函数，从中获得一些乐趣！

## 关于测试中重复管理用户的错误

因为我们修改了种子来创建一个新用户，所以如果您在任何时候重置您的测试数据库，您将开始遇到问题，因为您不能创建一个已经存在的用户。有一个简单的(暂时的)方法可以解决这个问题。打开**test/support/test _ helper . ex**，修改 **create_user** 函数:

```
def create_user(role, %{email: email, username: username, password: password, password_confirmation: password_confirmation}) do
  if user = Repo.get_by(User, username: username) do
    Repo.delete(user)
  end
  role
  |> build_assoc(:users)
  |> User.changeset(%{email: email, username: username, password: password, password_confirmation: password_confirmation})
  |> Repo.insert
end
```

## 我们现在在哪里？

现在，我们有了绿色规范，我们有了用户、帖子和角色。我们实现了限制用户注册、修改用户和帖子的 sane 功能，并实现了一些助手，使我们编写代码时的生活更加轻松。在接下来的几篇文章中，我们将花些时间为我们的博客引擎添加一些很酷的新功能！

## 本系列的下一篇文章

 [## 用 Phoenix 和 Elixir 编写博客引擎:第 5 部分，添加 ExMachina

### 最新更新:2016 年 1 月 26 日

medium.com](/@diamondgfx/mixology-exmachina-92a08dc3e954) 

# 看看我的新书！

嘿大家好！如果你喜欢你在这里读到的东西，并且想和我一起学习更多，可以看看我的新书《长生不老药和凤凰网开发》:

[](https://www.packtpub.com/web-development/phoenix-web-development) [## 凤凰网开发| PACKT 图书

### 学习使用 Elixir 和……从头开始构建投票 web 应用程序的高性能功能原型

www.packtpub.com](https://www.packtpub.com/web-development/phoenix-web-development) 

我真的很兴奋终于可以把这个项目推向世界了！它的写作风格与我的其他教程一样，我们将从头到尾构建一个完整项目的框架，甚至涵盖一些更棘手的主题，如文件上传、Twitter/Google OAuth 登录和 API！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)