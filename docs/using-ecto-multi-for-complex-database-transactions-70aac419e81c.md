# 使用埃克托。多用于复杂的数据库事务

> 原文：<https://medium.com/hackernoon/using-ecto-multi-for-complex-database-transactions-70aac419e81c>

维克拉姆·拉玛克里希南

最近，我们做了一个客户端项目，该项目要求在用户注册期间向服务器发送大量字段。其中一些字段(电子邮件、密码等。)是`user`模式的一部分，其他的是其他模式的一部分。由于这些其他模式依赖于`user`，我们将不得不在我们的`RegistrationController`中嵌套条件事务，这将提供多个故障点的可能性。我们不想嵌套这些条件事务，而是希望能够轻松地对事务进行排序，并匹配错误和失败。下面是我们如何使用`Ecto.Multi`来简化这一过程的解释。

考虑下面的例子。您有两个模式:`user`和`address`。一个`user` `has_many` `addresses`和一个`address` `belongs_to`一个`user`。在用户注册过程中，您希望用户提交他们的用户详细信息以及他们的邮寄地址详细信息。为了简单起见，让我们假设我们正在验证所有的字段，因此如果有任何字段没有发送到服务器，整个事务就会失败。下面是一个好的请求参数被发送到服务器的例子:

```
{
  "user": {
    "email": "vikram@quantlayer.com",
    "password": "password1",
    "phone_number": "6176176176"
  },
  "address": {
    "city": "Cambridge",
    "country": "US",
    "postal_code": "02139",
    "state_province": "MA",
    "street_line1": "5 QuantLayer Ave."
  }
}
```

由于邮寄地址属于一个用户，所以我们必须在创建地址之前创建一个用户来与该地址相关联。记住这一切，逻辑可能是这样的:

```
1\. Try creating a user
2\. If user creation fails, return an error
3\. If user creation succeeds, try creating an address
4\. If address creation fails, delete the user and return an error
5\. If address creation succeeds, return the user and jwt
```

下面是控制器中的一个示例:

```
user_changeset = User.changeset(%User{}, user_params)case Repo.insert(user_changeset) do {:ok, user} ->
  address_changeset = 
    %Address{user_id: user.id}
    |> Address.changeset(address_params) case Repo.insert(address_changeset) do
    {:ok, _address} ->
      {:ok, jwt, _full_claims} = 
        Guardian.encode_and_sign(user, :token) conn
      |> put_status(:created)
      |> render(MyApp.SessionView, "create.json", jwt: jwt, user: user) {:error, changeset} ->
      Repo.delete(user) conn
      |> put_status(:unprocessable_entity)
      |> render(MyApp.RegView, "error.json", changeset: changeset)
  end {:error, changeset} -> conn
  |> put_status(:unprocessable_entity)
  |> render(MyApp.RegView, "error.json", changeset: changeset)
end
```

有几件事我不喜欢。首先，嵌套的`case`语句很难理解。其次，我们删除了地址失败时新创建的用户，这增加了数据库事务的数量。最后，对于`user`和`address`参数，我们不会处理基于错误输入的错误。这种方法实在站不住脚。想象一下再增加一个步骤，比如要求信用卡详细信息。嵌套更多的`case`语句以及跟踪多个错误点变得很麻烦。如果事务的任何部分失败，我宁愿能够回滚整个事务。

## 输入`Ecto.Multi`

`Ecto.Multi`让我们处理多个相互依赖的回购交易。

文件([https://hexdocs.pm/ecto/Ecto.Multi.html](https://hexdocs.pm/ecto/Ecto.Multi.html))是这样描述的:

> “埃克托。Multi 使得将应该一起执行的操作打包(在单个数据库事务中)成为可能，并提供了一种无需实际执行就能自省排队操作的方法。每个操作都有一个独一无二的名称，这个名称将确定其结果，或者在发生故障时有助于确定故障的位置。”

所以，让我们用`Ecto.Multi`重写上面的例子:

```
user_changeset = User.changeset(%User{}, user_params)multi =
  Multi.new
  |> Multi.insert(:user, user_changeset)
  |> Multi.run(:address, fn %{user: user} ->
    address_changeset = 
      %Address{user_id: user.id}
      |> Address.changeset(address_params)
    Repo.insert(address_changeset)
  end)case Repo.transaction(multi) do
  {:ok, result} ->
    {:ok, jwt, _full_claims} = 
      Guardian.encode_and_sign(result.user, :token) conn
    |> put_status(:created)
    |> render(MyApp.SessionView, "create.json", jwt: jwt, user: result.user) {:error, :user, changeset, %{}} ->
    conn
    |> put_status(:unprocessable_entity)
    |> render(MyApp.RegView, "error.json", changeset: changeset) {:error, :address, changeset, %{}} ->
    conn
    |> put_status(:unprocessable_entity)
    |> render(MyApp.RegView, "error.json", changeset: changeset)
end
```

这里，我们将一个`Ecto.Multi.new`事务分配给`multi`。`Multi`通过`insert`等函数接受变更集。注意，`:user`和`:address`是我们分配给`Multi.insert/2`和`Multi.run/2`中操作的唯一名称，这就是为什么我们可以将`user`传递给`Multi.run/2`。检查变更集，如果有错误，事务不会启动并返回错误。然后我们使用`Multi.run`来传递一个任意的函数，这个函数依赖于前面行中的用户。当我们用`Repo.transaction(multi)`执行事务时，我们可以对所有可能的结果进行模式匹配，这使得以后添加更多的需求变得更加容易。

更多关于图书馆用途的好观点包含在原文`Ecto.Multi`PR:[https://github.com/elixir-ecto/ecto/issues/1114](https://github.com/elixir-ecto/ecto/issues/1114)中

有兴趣更广泛地讨论定制软件需求吗？给我在 vikram@quantlayer.com 写封短信——我很想和你聊天。在 https://twitter.com/@QuantLayer[的推特上关注我们](https://twitter.com/@QuantLayer)

> 黑客中午是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！