# 用 Phoenix 和 Elixir 编写博客引擎:第 2 部分，授权

> 原文：<https://medium.com/hackernoon/writing-a-blog-engine-in-phoenix-part-2-authorization-814c06fa7c0>

**最后更新于:**2016 年 7 月 21 日

## 本系列的前一篇文章

 [## 用 Phoenix 和 Elixir 编写博客引擎:第 1 部分

### 最后更新时间:2016 年 7 月 20 日

medium.com](/@diamondgfx/introduction-fe138ac6079d) 

## 当前版本:

在撰写本文时，我们应用程序的当前版本是:

*   **仙丹** : v1.3.1
*   **凤凰:** v1.2.0
*   **Ecto:** v2.0.2
*   **康美宁:** v2.5.2

如果你正在读这篇文章，而这些不是最新的，请告诉我，我会相应地更新这篇教程。

## 修复一些错误

如果您一直在跟进，那么您应该有一个运行在 Elixir/Phoenix 中的(有点)功能性的博客引擎。如果你和我一样，这种东西会让你眼花缭乱，你迫不及待地想要更好的东西！

如果你想检查你的进度，我已经把我们所有的工作放在网上了。

第一个很容易重现的错误是进入[http://localhost:4000/sessions/new](http://localhost:4000/sessions/new)并点击 submit。您应该会看到如下所示的错误消息:

```
nil given for :username, comparison with nil is forbidden as it always evaluates to false. Pass a full query expression and use is_nil/1 instead.
```

如果我们看看 **SessionController** 中的 **create** 函数，这里发生了什么就很清楚了。

```
def create(conn, %{"user" => user_params}) do
  user = Repo.get_by(User, username: user_params["username"])
  user
  |> sign_in(user_params["password"], conn)
end
```

因此，如果我们发送的参数字符串包含一个空值(或没有值)的用户名，我们会遇到这个错误。让我们赶快解决这个问题；谢天谢地，这很容易用保护子句和模式匹配来解决。用以下内容替换当前的**创建**功能:

```
def create(conn, %{"user" => %{"username" => username, "password" => password}})
when not is_nil(username) and not is_nil(password) do
  user = Repo.get_by(User, username: username)
  sign_in(user, password, conn)
enddef create(conn, _) do
  failed_login(conn)
end
```

我们用下划线替换第二个 **create** 函数中的 params 参数，因为我们不需要在任何地方使用它的结果。我们还添加了对这个 **failed_login** 函数的引用，所以我们把它添加为私有函数。在**web/controllers/session _ controller . ex**中，修改顶部的 Comeonin 导入语句:

```
import Comeonin.Bcrypt, only: [checkpw: 2, dummy_checkpw: 0]
```

我们需要调用 dummy_checkpw()，这样某人就不能简单地迭代使用错误密码的用户名，并检查当用户名在我们的系统中不存在时，时间是否会改变。然后，我们添加我们的 **failed_login** 函数:

```
defp failed_login(conn) do
  dummy_checkpw()
  conn
  |> put_session(:current_user, nil)
  |> put_flash(:error, "Invalid username/password combination!")
  |> redirect(to: page_path(conn, :index))
  |> halt()
end
```

同样，请注意对顶部的 **dummy_checkpw()** 的调用！我们还清除了 current_user 会话，设置了一个 flash 消息，指示用户输入了无效的用户名/密码组合，并重定向回主索引和 halt。此处停止的调用是针对双重渲染问题的明智之举。

然后，无论在哪里，只要我们有代码在做上面的任何工作，我们就可以用对新函数的调用来替换旧代码。

```
defp sign_in(user, _password, conn) when is_nil(user) do
  failed_login(conn)
enddefp sign_in(user, password, conn) do
  if checkpw(password, user.password_digest) do
    conn
    |> put_session(:current_user, %{id: user.id, username: user.username})
    |> put_flash(:info, "Sign in successful!")
    |> redirect(to: page_path(conn, :index))
  else
    failed_login(conn)
  end
end
```

这应该可以解决任何现有的登录错误，所以我们可以继续让我们的帖子与当前登录的用户相关联。

## 添加我们的迁移

为了能够将帖子与用户相关联，我们需要做的第一件事是修改**帖子**表，以包含对**用户**表的引用。

首先，我们将使用 Ecto 的迁移生成器来创建一个新的迁移。

```
$ mix ecto.gen.migration add_user_id_to_posts
```

我们应该看到一些输出，让我们知道它成功地工作。

```
Compiling 1 file (.ex)
* creating priv/repo/migrations
* creating priv/repo/migrations/20160720211140_add_user_id_to_posts.exs
```

但是，如果我们打开文件，那里还没有任何东西，所以我们将添加细节。

我们将更改**更改**功能，以包含以下内容:

```
def change do
  alter table(:posts) do
    add :user_id, references(:users)
  end
  create index(:posts, [:user_id])
end
```

这将添加引用 users 表的 user_id 列。它还在 posts 表的 user_id 列上建立了一个索引。我们将运行 **mix ecto.migrate** 并开始修改我们的模型。

## 将帖子与用户相关联

让我们打开 **web/models/post.ex** 并添加一个对**用户**模型的引用。

在“文章”模式下，添加以下内容:

```
belongs_to :user, Pxblog.User
```

我们将为**用户**模型添加一个反向关系，指向**发布**模型。在 **web/models/user.ex** 中的“用户”模式下，添加以下内容:

```
has_many :posts, Pxblog.Post
```

我们还需要打开**帖子**控制器，并将我们的帖子与用户相关联。

## 修改我们的路线

首先，我们将更新路由器以指向用户下方的帖子。打开 **web/router.ex** ，我们将稍微改变一下路线。

我们将稍微更改“/用户”和“/帖子”路径。删除这两个并替换为这个块:

```
resources "/users", UserController do
  resources "/posts", PostController
end
```

## 修理我们的控制器

如果我们现在尝试运行 **mix phoenix.routes** ，我们会得到一个错误。不过，没关系！由于我们改变了路由结构，我们失去了 **post_path** 助手，取而代之的是嵌套资源版本的 **user_post_path** 。嵌套的助手允许我们访问代表需要另一个资源存在的资源的路由(比如用户下面的帖子)。

因此，如果我们有一个常规的 **post_path** 助手，我们这样调用它:

```
post_path(conn, :show, post)
```

**conn** 是我们的连接对象， **:show** 是我们链接到的动作，第三个参数可以是对象的模型或 id。我们还可以做:

```
post_path(conn, :show, 1)
```

然而，当我们有一个嵌套的资源时，助手必须随着我们的 routes 文件的修改而改变。鉴于帖子现在嵌套在用户下，我们的路线也发生了变化:

```
user_post_path(conn, :show, user, post)
```

请注意，第三个参数更改为嵌套资源的顶层，每个附加的资源按顺序出现。鉴于我们对这一点的新理解，很清楚这是在哪里给我们抛出了错误，所以我们要清理这一点。我们希望向被请求的用户公开我们所有的控制器操作。对我们来说最好的方法是通过一个插件，所以我们将打开**web/controllers/post _ controller . ex**:

在顶部，我们将添加一个新的插件调用。

```
plug :assign_user
```

然后在底部，我们将编写我们的 assign_user 插件:

```
defp assign_user(conn, _opts) do
  case conn.params do
    %{"user_id" => user_id} ->
      user = Repo.get(Pxblog.User, user_id)
      assign(conn, :user, user)
    _ ->
      conn
  end
end
```

然后在 **post_path** 出现的任何地方，我们都将它替换为 **user_post_path** :

```
def create(conn, %{"post" => post_params}) do
 changeset = Post.changeset(%Post{}, post_params) case Repo.insert(changeset) do
    {:ok, _post} ->
      conn
      |> put_flash(:info, "Post created successfully.")
      |> redirect(to: user_post_path(conn, :index, conn.assigns[:user]))
    {:error, changeset} ->
      render(conn, "new.html", changeset: changeset)
  end
enddef update(conn, %{"id" => id, "post" => post_params}) do
  post = Repo.get!(Post, id)
  changeset = Post.changeset(post, post_params) case Repo.update(changeset) do
    {:ok, post} ->
      conn
      |> put_flash(:info, "Post updated successfully.")
      |> redirect(to: user_post_path(conn, :show, conn.assigns[:user], post))
    {:error, changeset} ->
      render(conn, "edit.html", post: post, changeset: changeset)
  end
enddef delete(conn, %{"id" => id}) do
  post = Repo.get!(Post, id) # Here we use delete! (with a bang) because we expect
  # it to always work (and if it does not, it will raise).
  Repo.delete!(post) conn
  |> put_flash(:info, "Post deleted successfully.")
  |> redirect(to: user_post_path(conn, :index, conn.assigns[:user]))
end
```

## 修复我们的模板

我们的控制器不再抛出错误信息，所以现在我们将在模板上工作。我们走了一条捷径，实现了一个影响我们所有控制器动作的插件。通过在我们的连接对象上使用**赋值**函数，我们已经暴露了一个可以在模板中使用的变量。我们将不得不修改相当多的模板，因为无论我们在哪里使用 link helper 和我们的 **post_path** helper，我们都需要将它更新为 user_post_path，并确保动作之后的第一个参数是用户的 id。

**web/templates/post/index . html . eex**:

```
<h2>Listing posts</h2><table class="table">
  <thead>
    <tr>
      <th>Title</th>
      <th>Body</th><th></th>
    </tr>
  </thead>
  <tbody>
<%= for post <- @posts do %>
    <tr>
      <td><%= post.title %></td>
      <td><%= post.body %></td><td class="text-right">
        <%= link "Show", to: user_post_path(@conn, :show, @user, post), class: "btn btn-default btn-xs" %>
        <%= link "Edit", to: user_post_path(@conn, :edit, @user, post), class: "btn btn-default btn-xs" %>
        <%= link "Delete", to: user_post_path(@conn, :delete, @user, post), method: :delete, data: [confirm: "Are you sure?"], class: "btn btn-danger btn-xs" %>
      </td>
    </tr>
<% end %>
  </tbody>
</table><%= link "New post", to: user_post_path(@conn, :new, @user) %>
```

**web/templates/post/show . html . eex**:

```
<h2>Show post</h2><ul> <li>
    <strong>Title:</strong>
    <%= @post.title %>
  </li> <li>
    <strong>Body:</strong>
    <%= @post.body %>
  </li></ul><%= link "Edit", to: user_post_path(@conn, :edit, @user, @post) %>
<%= link "Back", to: user_post_path(@conn, :index, @user) %>
```

**web/templates/post/new . html . eex**:

```
<h2>New post</h2><%= render "form.html", changeset: @changeset,
                        action: user_post_path(@conn, :create, @user) %><%= link "Back", to: user_post_path(@conn, :index, @user) %>
```

**web/templates/post/edit . html . eex**:

```
<h2>Edit post</h2><%= render "form.html", changeset: @changeset,
                        action: user_post_path(@conn, :update, @user, @post) %><%= link "Back", to: user_post_path(@conn, :index, @user) %>
```

现在，作为健全性检查，如果我们运行 **mix phoenix.routes** ，我们应该会看到输出和成功的编译！

```
Compiling 14 files (.ex)
     page_path  GET     /                               Pxblog.PageController :index
     user_path  GET     /users                          Pxblog.UserController :index
     user_path  GET     /users/:id/edit                 Pxblog.UserController :edit
     user_path  GET     /users/new                      Pxblog.UserController :new
     user_path  GET     /users/:id                      Pxblog.UserController :show
     user_path  POST    /users                          Pxblog.UserController :create
     user_path  PATCH   /users/:id                      Pxblog.UserController :update
                PUT     /users/:id                      Pxblog.UserController :update
     user_path  DELETE  /users/:id                      Pxblog.UserController :delete
user_post_path  GET     /users/:user_id/posts           Pxblog.PostController :index
user_post_path  GET     /users/:user_id/posts/:id/edit  Pxblog.PostController :edit
user_post_path  GET     /users/:user_id/posts/new       Pxblog.PostController :new
user_post_path  GET     /users/:user_id/posts/:id       Pxblog.PostController :show
user_post_path  POST    /users/:user_id/posts           Pxblog.PostController :create
user_post_path  PATCH   /users/:user_id/posts/:id       Pxblog.PostController :update
                PUT     /users/:user_id/posts/:id       Pxblog.PostController :update
user_post_path  DELETE  /users/:user_id/posts/:id       Pxblog.PostController :delete
  session_path  GET     /sessions/new                   Pxblog.SessionController :new
  session_path  POST    /sessions                       Pxblog.SessionController :create
  session_path  DELETE  /sessions/:id                   Pxblog.SessionController :delete
```

## 在控制器里把它们连接起来

现在，我们需要做的就是完成控制器的连接，以使用新的关联。首先，我们将启动 iex -S mix ,这样我们就可以了解一点如何获取用户的帖子。不过，在我们这样做之前，设置一个标准导入/别名列表会对我们有一点帮助，每次我们在项目中加载 iex 提示符时，都会加载这个列表。在项目根目录下创建一个名为 **.iex.exs** 的新文件(注意文件名开头的句点)，并用以下内容填充它:

```
import Ecto.Query
alias Pxblog.User
alias Pxblog.Post
alias Pxblog.Repo
import Ecto
```

现在，当 iex 启动时，我们不必每次都做类似下面这样的事情:

```
iex(1)> import Ecto.Query
nil
iex(2)> alias Pxblog.User
nil
iex(3)> alias Pxblog.Post
nil
iex(4)> alias Pxblog.Repo
nil
iex(5)> import Ecto
nil
```

继续，运行 **iex -S mix:** 我们的回购中至少应该有一个用户。如果没有，请先创建一个。然后我们会跑:

```
iex(8)> user = Repo.get(User, 1)
    [debug] SELECT u0."id", u0."username", u0."email", u0."password_digest", u0."inserted_at", u0."updated_at" FROM "users" AS u0 WHERE (u0."id" = $1) [1] OK query=8.2ms
    %Pxblog.User{__meta__: #Ecto.Schema.Metadata<:loaded>, email: "test", id: 1,
     inserted_at: #Ecto.DateTime<2015-10-06T17:47:07Z>, password: nil,
     password_confirmation: nil,
     password_digest: "$2b$12$pV/XBBCRl0RQhadQd9Y4mevOy5y0j4bCC/LjGgx7VJMosRdwme22a",
     posts: #Ecto.Association.NotLoaded<association :posts is not loaded>,
     updated_at: #Ecto.DateTime<2015-10-06T17:47:07Z>, username: "test"} iex(10)> Repo.all(assoc(user, :posts))
    [debug] SELECT p0."id", p0."title", p0."body", p0."user_id", p0."inserted_at", p0."updated_at" FROM "posts" AS p0 WHERE (p0."user_id" IN ($1)) [1] OK query=3.5ms
    []
```

我们还没有创建任何与用户相关的帖子，所以我们在那里得到一个空白列表是有意义的。我们使用来自 **Ecto** 的 **assoc** 函数来给出一个将帖子链接到用户的查询。我们还可以做以下事情:

```
iex(14)> Repo.all from p in Post,
...(14)>          join: u in assoc(p, :user),
...(14)>          select: p
[debug] SELECT p0."id", p0."title", p0."body", p0."user_id", p0."inserted_at", p0."updated_at" FROM "posts" AS p0 INNER JOIN "users" AS u1 ON u1."id" = p0."user_id" [] OK query=0.9ms
```

这将为我们提供一个带有内部连接的查询，而不是用户 id 上的直接 where 子句。请特别注意这两种情况下生成的查询；在使用生成查询的代码时，理解后台生成的 SQL 非常方便。

我们还可以在获取帖子时使用 **preload** 函数来预载用户，如下所示:

```
iex(18)> Repo.all(from u in User, preload: [:posts])
[debug] SELECT u0."id", u0."username", u0."email", u0."password_digest", u0."inserted_at", u0."updated_at" FROM "users" AS u0 [] OK query=0.9ms
[debug] SELECT p0."id", p0."title", p0."body", p0."user_id", p0."inserted_at", p0."updated_at" FROM "posts" AS p0 WHERE (p0."user_id" IN ($1)) ORDER BY p0."user_id" [1] OK query=0.8msiex(20)> Repo.all(from p in Post, preload: [:user])
[debug] SELECT p0."id", p0."title", p0."body", p0."user_id", p0."inserted_at", p0."updated_at" FROM "posts" AS p0 [] OK query=0.8ms
[]
```

我们需要自己处理这些查询，所以我们将使用 **Ecto 的 build_assoc** 函数。 **build_assoc** 将我们想要添加关联的模型作为第一个参数，将我们想要挂钩的关联作为原子作为第二个参数，将参数作为第三个参数。

```
iex(1)> user = Repo.get(User, 1)
iex(2)> post = build_assoc(user, :posts, %{title: "Test Title", body: "Test Body"})
iex(3)> Repo.insert(post)
iex(4)> posts = Repo.all(from p in Post, preload: [:user])
```

在最后一个命令中，我们应该得到以下输出:

```
iex(4)> posts = Repo.all(from p in Post, preload: [:user])
[debug] SELECT p0."id", p0."title", p0."body", p0."user_id", p0."inserted_at", p0."updated_at" FROM "posts" AS p0 [] OK query=0.7ms
[debug] SELECT u0."id", u0."username", u0."email", u0."password_digest", u0."inserted_at", u0."updated_at" FROM "users" AS u0 WHERE (u0."id" IN ($1)) [1] OK query=0.7ms
[%Pxblog.Post{__meta__: #Ecto.Schema.Metadata<:loaded>, body: "Test Body",
  id: 1, inserted_at: #Ecto.DateTime<2015-10-06T18:06:20Z>, title: "Test Title",
  updated_at: #Ecto.DateTime<2015-10-06T18:06:20Z>,
  user: %Pxblog.User{__meta__: #Ecto.Schema.Metadata<:loaded>, email: "test",
   id: 1, inserted_at: #Ecto.DateTime<2015-10-06T17:47:07Z>, password: nil,
   password_confirmation: nil,
   password_digest: "$2b$12$pV/XBBCRl0RQhadQd9Y4mevOy5y0j4bCC/LjGgx7VJMosRdwme22a",
   posts: #Ecto.Association.NotLoaded<association :posts is not loaded>,
   updated_at: #Ecto.DateTime<2015-10-06T17:47:07Z>, username: "test"},
  user_id: 1}]
```

我们将快速检查第一个结果:

```
iex(5)> post = List.first posts
%Pxblog.Post{__meta__: #Ecto.Schema.Metadata<:loaded>, body: "Test Body", id: 1,
 inserted_at: #Ecto.DateTime<2015-10-06T18:06:20Z>, title: "Test Title",
 updated_at: #Ecto.DateTime<2015-10-06T18:06:20Z>,
 user: %Pxblog.User{__meta__: #Ecto.Schema.Metadata<:loaded>, email: "test",
  id: 1, inserted_at: #Ecto.DateTime<2015-10-06T17:47:07Z>, password: nil,
  password_confirmation: nil,
  password_digest: "$2b$12$pV/XBBCRl0RQhadQd9Y4mevOy5y0j4bCC/LjGgx7VJMosRdwme22a",
  posts: #Ecto.Association.NotLoaded<association :posts is not loaded>,
  updated_at: #Ecto.DateTime<2015-10-06T17:47:07Z>, username: "test"},
 user_id: 1}iex(6)> post.title
"Test Title"
iex(7)> post.user.username
"test"
```

酷！我们的实验正在做我们期望的事情，所以让我们回到我们的控制器(**web/controllers/post _ controller . ex)**并开始修复代码。对于索引 action，我们需要与一个用户相关联的所有帖子，所以让我们修复代码。我们将从索引操作开始:

```
def index(conn, _params) do
  posts = Repo.all(assoc(conn.assigns[:user], :posts))
  render(conn, "index.html", posts: posts)
end
```

然后，我们可以访问用户 1 的[帖子索引，并看到一个帖子列表！但是，如果我们试图访问一个不存在的用户的 post 索引，我们会得到一个错误消息，这不是一个很好的用户体验，所以让我们清理我们的 assign user 插件。](http://localhost:4000/users/1/posts)

```
defp assign_user(conn, _opts) do
  case conn.params do
    %{"user_id" => user_id} ->
      case Repo.get(Pxblog.User, user_id) do
        nil  -> invalid_user(conn)
        user -> assign(conn, :user, user)
      end
    _ -> invalid_user(conn)
  end
end

defp invalid_user(conn) do
  conn
  |> put_flash(:error, "Invalid user!")
  |> redirect(to: page_path(conn, :index))
  |> halt
end
```

现在，当我们访问一个不存在的用户的帖子索引时，我们会得到一条很好的 flash 消息，并被友好地重定向回页面路径！接下来，我们需要改变我们的新行动:

```
def new(conn, _params) do
  changeset =
    conn.assigns[:user]
    |> build_assoc(:posts)
    |> Post.changeset()
  render(conn, "new.html", changeset: changeset)
end
```

我们使用我们的**用户**模型，将其通过管道传输到 Ecto 的 build_assoc 函数，告诉它我们需要构建一个 post，然后将得到的空白模型通过管道传输到 Post.changeset 函数，以获得一个空白的变更集。对于 create 方法，我们将遵循相同的模式(除了我们的 post_params 保持不变):

```
def create(conn, %{"post" => post_params}) do
  changeset =
    conn.assigns[:user]
    |> build_assoc(:posts)
    |> Post.changeset(post_params) case Repo.insert(changeset) do
    {:ok, _post} ->
      conn
      |> put_flash(:info, "Post created successfully.")
      |> redirect(to: user_post_path(conn, :index, conn.assigns[:user]))
    {:error, changeset} ->
      render(conn, "new.html", changeset: changeset)
  end
end
```

然后修改我们的显示、编辑、更新和删除操作:

```
def show(conn, %{"id" => id}) do
  post = Repo.get!(assoc(conn.assigns[:user], :posts), id)
  render(conn, "show.html", post: post)
enddef edit(conn, %{"id" => id}) do
  post = Repo.get!(assoc(conn.assigns[:user], :posts), id)
  changeset = Post.changeset(post)
  render(conn, "edit.html", post: post, changeset: changeset)
enddef update(conn, %{"id" => id, "post" => post_params}) do
  post = Repo.get!(assoc(conn.assigns[:user], :posts), id)
  changeset = Post.changeset(post, post_params) case Repo.update(changeset) do
    {:ok, post} ->
      conn
      |> put_flash(:info, "Post updated successfully.")
      |> redirect(to: user_post_path(conn, :show, conn.assigns[:user], post))
    {:error, changeset} ->
      render(conn, "edit.html", post: post, changeset: changeset)
  end
enddef delete(conn, %{"id" => id}) do
  post = Repo.get!(assoc(conn.assigns[:user], :posts), id) # Here we use delete! (with a bang) because we expect
  # it to always work (and if it does not, it will raise).
  Repo.delete!(post) conn
  |> put_flash(:info, "Post deleted successfully.")
  |> redirect(to: user_post_path(conn, :index, conn.assigns[:user]))
end
```

当我们全部测试完毕后，我们应该会看到一切正常！除了…任何用户都可以删除/编辑/创建他们想要的任何用户 id 下的新帖子！

## 限制向用户发布

我们不能发布一个有这样安全漏洞的博客引擎，不是吗？让我们通过添加另一个插件来修复它，确保当前用户与获取的用户相同。

在底部，我们将为**web/controllers/post _ controller . ex**添加一个新函数:

```
defp authorize_user(conn, _opts) do
    user = get_session(conn, :current_user)
    if user && Integer.to_string(user.id) == conn.params["user_id"] do
      conn
    else
      conn
      |> put_flash(:error, "You are not authorized to modify that post!")
      |> redirect(to: page_path(conn, :index))
      |> halt()
    end
  end
```

在顶部，我们将添加插件调用:

```
plug :authorize_user when action in [:new, :create, :update, :edit, :delete]
```

现在一切都应该正常工作了！用户必须登录才能发帖，而且只能乱翻自己的帖子。我们所需要的是更新我们的测试套件来处理这些变化，我们应该一切就绪。让我们从运行**混合测试**开始，找出我们所处的位置。您很可能会看到如下所示的错误消息:

```
** (CompileError) test/controllers/post_controller_test.exs:14: function post_path/2 undefined
    (stdlib) lists.erl:1337: :lists.foreach/2
    (stdlib) erl_eval.erl:669: :erl_eval.do_apply/6
    (elixir) lib/code.ex:363: Code.require_file/2
    (elixir) lib/kernel/parallel_require.ex:50: anonymous fn/4 in Kernel.ParallelRequire.spawn_requires/5
```

不幸的是，我们不得不再次将 **post_path** 的每个实例修改为 **user_post_path** 。为了做到这一点，我们需要彻底改变我们的测试。我们将从添加一个设置块到**test/controllers/post _ controller _ text . exs**开始:

```
alias Pxblog.Usersetup do
  {:ok, user} = create_user
  conn = build_conn()
  |> login_user(user)
  {:ok, conn: conn, user: user}
enddefp create_user do
  User.changeset(%User{}, %{email: "test@test.com", username: "test", password: "test", password_confirmation: "test"})
  |> Repo.insert
enddefp login_user(conn, user) do
  post conn, session_path(conn, :create), user: %{username: user.username, password: user.password}
end
```

这里已经发生了很多事情。我们做的第一件事是添加一个对我们需要编写的 **create_user** 函数的调用。我们需要一些测试助手，所以我们将添加一些函数来处理这些。我们的 **create_user** 函数只是将一个样本用户插入到我们的 Repo 中，所以这就是为什么我们要从那个函数调用中进行模式匹配 **{:ok，user}** 。

接下来，我们有一个 **conn = build_conn()** 调用，您之前已经见过了。然后，我们将结果连接到这个 **login_user** 函数中。这个连接发布到我们的登录函数，因为我们所有的主要发布操作都需要一个登录的用户。这里非常重要的一点是:我们**需要**返回连接器，并在每次测试中随身携带。如果我们不这样做，用户将无法保持登录状态！

最后，我们修改了该函数的返回，以返回相同的标准:ok 和:conn 值，但是现在我们还包含了一个:用户进入 dict。

让我们来看看我们必须修改的第一个测试:

```
test "lists all entries on index", %{conn: conn, user: user} do
  conn = get conn, user_post_path(conn, :index, user)
  assert html_response(conn, 200) =~ "Listing posts"
end
```

请注意，我们将“test”方法的第二个参数改为模式匹配到包含键*:连接*和*:用户*的映射，而不仅仅是*:连接*。这确保了我们公开了我们在设置块中使用的 *:user* 键。除此之外，我们刚刚将 **post_path** 助手调用更改为 **user_post_path** ，并添加用户作为我们的第三个参数。立即显式运行此测试；您可以使用标记或通过运行以下命令指定行号来进行此测试:

```
$ mix test test/controller/post_controller_test.exs:[line number]
```

我们的测试现在应该是绿色的！太好了！但是让我们继续修改这些:

```
test "renders form for new resources", %{conn: conn, user: user} do
  conn = get conn, user_post_path(conn, :new, user)
  assert html_response(conn, 200) =~ "New post"
end
```

除了对设置处理程序和用户发布路径的更改之外，这里没有什么新的东西，所以我们将继续。

```
test "creates resource and redirects when data is valid", %{conn: conn, user: user} do
  conn = post conn, user_post_path(conn, :create, user), post: @valid_attrs
  assert redirected_to(conn) == user_post_path(conn, :index, user)
  assert Repo.get_by(assoc(user, :posts), @valid_attrs)
end
```

请记住，我们必须通过用户的关联来获取每个帖子，所以我们希望确保我们在这里这样做，并更改所有的 **post_path** 调用。

```
test "does not create resource and renders errors when data is invalid", %{conn: conn, user: user} do
  conn = post conn, user_post_path(conn, :create, user), post: @invalid_attrs
  assert html_response(conn, 200) =~ "New post"
end
```

另一个容易修改的测试，所以我们将转移到下一个更有趣的测试。请再次记住，我们必须在用户关联下构建/获取所有帖子，因此我们将修改我们的“显示所选资源”测试:

```
test "shows chosen resource", %{conn: conn, user: user} do
  post = build_post(user)
  conn = get conn, user_post_path(conn, :show, user, post)
  assert html_response(conn, 200) =~ "Show post"
end
```

以前，我们将 post 设置为一个简单的 Repo.insert！%Post{}。这对我们不再适用，因为我们需要通过适当的关联来构建它。由于这一行在剩余的测试中出现得相当频繁，我们将编写一个帮助器方法来简化这一过程。

```
defp build_post(user) do
  changeset =
    user
    |> build_assoc(:posts)
    |> Post.changeset(@valid_attrs)
  Repo.insert!(changeset)
end
```

这个方法在我们的用户关联下创建一个有效的 post 模型，然后将其插入到数据库中。**注意，Repo.insert！不返回{:ok，model}，而是只返回模型！**

回到我们正在修改的测试，其余的都是非常简单的。我将把剩下的测试贴在下面；你只会一遍又一遍地重复同样的修改，直到它们都被处理好。

```
test "renders page not found when id is nonexistent", %{conn: conn, user: user} do
    assert_raise Ecto.NoResultsError, fn ->
      get conn, user_post_path(conn, :show, user, -1)
    end
  end test "renders form for editing chosen resource", %{conn: conn, user: user} do
    post = build_post(user)
    conn = get conn, user_post_path(conn, :edit, user, post)
    assert html_response(conn, 200) =~ "Edit post"
  end test "updates chosen resource and redirects when data is valid", %{conn: conn, user: user} do
    post = build_post(user)
    conn = put conn, user_post_path(conn, :update, user, post), post: @valid_attrs
    assert redirected_to(conn) == user_post_path(conn, :show, user, post)
    assert Repo.get_by(Post, @valid_attrs)
  end test "does not update chosen resource and renders errors when data is invalid", %{conn: conn, user: user} do
    post = build_post(user)
    conn = put conn, user_post_path(conn, :update, user, post), post: %{"body" => nil}
    assert html_response(conn, 200) =~ "Edit post"
  end test "deletes chosen resource", %{conn: conn, user: user} do
    post = build_post(user)
    conn = delete conn, user_post_path(conn, :delete, user, post)
    assert redirected_to(conn) == user_post_path(conn, :index, user)
    refute Repo.get(Post, post.id)
  end
```

当你修改了所有的东西，你应该能够运行**混合测试**并且得到绿色测试！

最后，我们编写了一些新代码作为插件来处理用户查找和授权，并且我们已经很好地测试了正面情况，但是我们还应该为负面情况添加测试。我们将从一个测试开始，测试当我们试图访问一个不存在的用户的文章列表时会发生什么:

```
test "redirects when the specified user does not exist", %{conn: conn} do
  conn = get conn, user_post_path(conn, :index, -1)
  assert get_flash(conn, :error) == "Invalid user!"
  assert redirected_to(conn) == page_path(conn, :index)
  assert conn.halted
end
```

我们不需要在我们的模式匹配块中包含:user，因为我们无论如何都不会使用它。此外，我们还断言连接会在结束时中止。

最后，我们需要编写一个测试来测试我们何时编辑别人的帖子。

```
test "redirects when trying to edit a post for a different user", %{conn: conn, user: user} do
  other_user = User.changeset(%User{}, %{email: "test2@test.com", username: "test2", password: "test", password_confirmation: "test"})
  |> Repo.insert!
  post = build_post(user)
  conn = get conn, user_post_path(conn, :edit, other_user, post)
  assert get_flash(conn, :error) == "You are not authorized to modify that post!"
  assert redirected_to(conn) == page_path(conn, :index)
  assert conn.halted
end
```

我们创建另一个用户作为我们的坏用户，并将其插入到回购中。然后，我们尝试在第一个用户下访问帖子的编辑操作。这会触发我们 **authorize_user** 插头的反面案例！保存该文件并运行**混合测试**，我们将等待结果:

```
.......................................Finished in 0.4 seconds
39 tests, 0 failuresRandomized with seed 102543
```

咻！太多了！但是，我们现在有了一个功能性的(更受保护的)博客，帖子是在我们的用户下创建的，我们仍然有一些很好的测试覆盖面！休息一下，放松一下，去玩几代怪物猎人(或者至少，这是我的计划)！我们将继续这一系列教程，添加管理员角色、评论、降价支持，最后我们将通过一个实时评论系统进入频道！

如果您有兴趣了解如何在开发模式下调试这个应用程序，那么请在这里阅读更多关于[调试 Phoenix 应用程序的内容！](/@diamondgfx/debugging-phoenix-with-iex-pry-5417256e1d11)

如果你想继续学习教程，那我们继续吧！

## 本系列的下一篇文章

 [## 用 Phoenix 和 Elixir 编写博客引擎:第 3 部分，向模型添加角色

### 最新更新:2016 年 1 月 26 日

medium.com](/@diamondgfx/writing-a-blog-engine-in-phoenix-and-elixir-part-3-adding-roles-to-our-models-3be45a4afe4b) 

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