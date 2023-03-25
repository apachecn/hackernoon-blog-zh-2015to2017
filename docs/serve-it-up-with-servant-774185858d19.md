# 用仆人端上来！

> 原文：<https://medium.com/hackernoon/serve-it-up-with-servant-774185858d19>

[上周](https://www.mmhaskell.com/blog/2017/10/2/trouble-with-databases-persevere-with-persistent)我们通过学习[持久](https://hackage.haskell.org/package/persistent)开始了我们关于生产 Haskell 技术的系列。我们创建了一个包含单个`User`类型的模式，可以存储在 Postgresql 数据库中。我们研究了几个允许我们对这些用户进行 SQL 查询的函数。

本周，我们将看到如何使用 API 向外界公开这个数据库。我们将使用[服务](https://hackage.haskell.org/package/servant)库来构建我们的 API。Servant 涉及一些高级类型级别的构造，所以有很多东西需要考虑。肯定有比 Servant 使用的方法更简单的 HTTP 服务器方法。但是我发现仆人给我们的力量是值得努力的。

本文将对仆人做一个简要的概述。但是如果你想要更深入的介绍，你应该看看我去年春天在 Bayhac 的[演讲！那次演讲更加详尽地介绍了可以在 API 中使用的不同组合子。它还展示了身份验证技术、客户机功能和文档。您还可以查看该演示的](https://www.youtube.com/watch?v=19HTfXFmwvA)[幻灯片和代码](https://www.mmhaskell.com/bayhac)！

另外，看看这个项目的 Github repo 上的 [servant branch](https://github.com/jhb563/ProdHaskellSeries/tree/servant) 来查看本文的所有代码！

# 定义我们的 API

为我们的用户数据库编写 API 的第一步是决定不同的端点是什么。我们可以独立于使用什么语言或库来决定这一点。对于本文，我们的 API 将有两个不同的端点。第一个是对`/users`的 POST 请求。这个请求的主体将包含一个“用户”定义，结果是我们将在数据库中创建一个用户。以下是这种情况的一个示例:

```
POST /users
{
  userName : “John Doe”,
  userEmail : “john@doe.com”,
  userAge : 29,
  userOccupation: “Teacher”
}
```

然后，它将返回一个包含我们创建的用户的数据库密钥的响应。这将允许任何客户端再次获取用户。第二个端点将使用 ID 通过数据库标识符获取用户。这将是对`/users/:userid`的一个 GET 请求。例如，最后一个请求可能会返回类似于`16`的内容。然后，我们可以执行以下操作:

```
GET /users/16
```

我们的响应看起来就像上面的请求体。

# 作为类型的 API

所以我们有了非常简单的 API。在 Haskell 中，具体来说，在 Servant 中，我们如何定义它呢？嗯，Servant 做了一些非常独特的事情(据我所知)。在 Servant 中，我们通过使用类型来定义我们的 API。我们的类型将包括 API 的每个端点的子类型。我们通过使用`(:<|>)`操作符来组合不同的端点。我有时会称之为“E-plus”，意为“endpoint-plus”。这是一个类型运算符，就像我们在[依赖类型和张量流](https://mmhaskell.com/blog/2017/9/11/deep-learning-and-deep-types-tensor-flow-and-dependent-types)中看到的一些运算符一样。这是我们 API 的蓝图:

```
type UsersAPI = 
  fetchEndpoint
  :<|> createEndpoint
```

现在让我们定义一下`fetchEndpoint`和`createEndpoint`的含义。端点组合了描述端点不同信息的不同组合符。我们用`(:>)`操作符将组合子联系在一起，我称之为“C-plus”(组合子加)。这是我们最终的 API 的样子。我们将在下一节讨论每个组合子的含义:

```
type UsersAPI =
       “users” :> Capture “userid” Int64 :> Get ‘[JSON] User
  :<|> “users” :> ReqBody ‘[JSON] User :> Post ‘[JSON] Int64
```

# 不同的组合子

这两个端点都有三个不同的组合子。让我们从检查获取端点开始。它从一个字符串组合子开始。这是一个路径组件，允许我们指定调用者应该使用什么 url 扩展来访问端点。我们可以多次使用这个组合子，为端点创建一个更复杂的路径。如果我们想让这个端点在`/api/users/:userid`，那么我们可以把它改成:

```
“api” :> “users” :> Capture “userid” Int64 :> Get ‘[JSON] User
```

第二个组合子(`Capture`)允许我们从 URL 本身获取一个值。我们给这个值一个名字，然后提供一个类型参数。我们不需要自己做任何路径解析或操作。Servant 将处理解析 URL 并传递给我们一个`Int64`的棘手任务。如果您想将自己的自定义类用作一段 HTTP 数据，这并不太难。您只需从 HttpApiData 类中编写一个[的实例。所有像`Int64`这样的基本类型都已经有了实例。](https://hackage.haskell.org/package/http-api-data-0.3.7.1/docs/Web-HttpApiData.html#t:FromHttpApiData)

最终的组合符本身包含这个端点的三条重要信息。首先，它告诉我们这实际上是一个`GET`请求。其次，它给出了响应中允许的内容类型列表。这是内容格式的类型级别列表。这个列表中的每个类型都必须有不同的类来序列化和反序列化我们的数据。我们可以使用更复杂的列表，比如`’[JSON, PlainText, OctetStream]`。但是对于本文的其余部分，我们将只使用`JSON`。这意味着我们将使用`ToJSON`和`FromJSON`类型类进行序列化。

这个组合子的最后一部分是我们的端点返回的类型。因此，一个成功的请求将向调用者返回一个包含 JSON 格式的`User`的响应。注意这不是一个`Maybe User`。如果 ID 不在我们的数据库中，我们将返回 401 错误来指示失败，而不是返回`Nothing`。

我们的第二个端点有许多相似之处。它使用相同的字符串路径组件。那么它的最终组合符是相同的，除了它表明它是一个`POST`请求而不是一个`GET`请求。第二个组合子告诉我们，我们可以期望请求体看起来像什么。在这种情况下，请求体应该包含一个`User`的 JSON 表示。它需要一个可接受的内容类型列表，然后是我们想要的类型，比如`Get`和`Post`组合子。

这就完成了我们 API 的“定义”。我们将需要添加我们的`User`类型的`ToJSON`和`FromJSON`实例，以使其发挥作用。你可以看看那些在 [Github](https://github.com/jhb563/ProdHaskellSeries/blob/servant/src/Schema.hs) 上的，并且查看[这篇文章](https://mmhaskell.com/blog/2017/6/5/flexible-data-with-aeson)来获得关于创建那些实例的更多细节！

# 编写处理程序

既然我们已经定义了 API 的类型，我们需要为每个端点编写处理函数。这就是仆人的魅力所在。我们可以根据端点中的组合子将每个端点映射到一个具有特定类型的函数。因此，首先让我们记住获取用户的端点:

```
“users” :> Capture “userid” Int64 :> Get ‘[JSON] User
```

字符串路径组件没有给我们的函数添加任何参数。组件`Capture`将产生一个类型为`Int64`的参数，这是我们在函数中需要的。那么我们函数的返回类型应该是`User`。这几乎完全定义了我们的处理程序的类型签名。不过我们会注意到它需要在`Handler`单子上。这是它看起来的样子:

```
fetchUsersHandler :: Int64 -> Handler User
...
```

Servant 还可以查看我们的创建端点的类型:

```
“users” :> ReqBody ‘[JSON] User :> Post ‘[JSON] Int64
```

`ReqBody`参数的参数只是类型参数。因此它会将端点解析为处理程序类型:

```
createUserHandler :: User -> Handler Int64
...
```

现在，我们需要能够通过这两个处理程序访问我们的 Postgres 数据库。所以他们每个人都会得到一个额外的参数来引用`ConnectionString`。我们将从代码中传递该参数，以便 Servant 解析类型时，该参数已被计算在内:

```
fetchUsersHandler :: ConnectionString -> Int64 -> Handler User
createUserHandler :: ConnectionString -> User -> Handler Int64
```

在我们继续之前，我们应该讨论一下`Handler`单子。这是单子`ExceptT ServantErr IO`的包装。换句话说，这些请求中的每一个都可能失败。为了使它失败，我们可以抛出类型为`ServantErr`的错误。那么当然我们也可以调用`IO`函数，因为这些都是网络操作。

在我们实现这些函数之前，让我们先写几个简单的助手。这些将使用上周文章中的`runAction`函数来运行数据库操作:

```
fetchUserPG :: ConnectionString -> Int64 -> IO (Maybe User)
fetchUserPG connString uid = runAction connString (get (toSqlKey uid))createUserPG :: ConnectionString -> User -> IO Int64
createUserPG connString user = fromSqlKey <$> runAction connString (insert user)
```

为了完整性(并在稍后的测试中使用)，我们还将添加一个简单的`delete`函数。我们需要`where`子句进行类型推断:

```
deleteUserPG :: ConnectionString -> Int64 -> IO ()
deleteUserPG connString uid = runAction connString (delete userKey)
  where
    userKey :: Key User
    userKey = toSqlKey uid
```

现在从我们的服务处理程序中，我们将调用这两个函数。这将完全涵盖创建端点的情况。但是我们需要更多的逻辑来获取端点。因为我们的函数在`IO`单子上，我们必须把它们提升到`Handler`。

```
fetchUsersHandler :: ConnectionString -> Int64 -> Handler User
fetchUserHandler connString uid = do
  maybeUser <- liftIO $ fetchUserPG connString uid
  ...createUserHandler :: ConnectionString -> User -> Handler Int64
createuserHandler connString user = liftIO $ createUserPG connString user
```

为了完成我们的获取处理程序，我们需要考虑一个不存在的用户。在这种情况下，我们将抛出一个`ServantErr`，而不是将整个端点的类型设为`Maybe`。我们可以使用一个内置的 Servant error 函数，它对应于正常的错误代码。然后我们可以更新身体。在这种情况下，我们将抛出一个 401 错误。我们是这样做的:

```
fetchUsersHandler :: ConnectionString -> Int64 -> Handler User
fetchUserHandler connString uid = do
  maybeUser <- lift $ fetchUserPG connString uid
  case maybeUser of
    Just user -> return user
    Nothing -> Handler $ (throwE $ err401 { errBody = “Could not find user with ID: “ ++ (show uid)})createUserHandler :: ConnectionString -> User -> Handler Int64
createuserHandler connString user = lift $ createUserPG connString user
```

就是这样！我们已经完成了处理函数！

# 将所有这些整合到一个服务器中

我们的下一步是在 API 上创建一个类型为`Server`的对象。这实际上非常简单。当我们定义原始类型时，我们用`(:<|>)`操作符组合了端点。为了制作我们的`Server`，我们做了同样的事情，但是使用了处理函数:

```
usersServer :: ConnectionString -> Server UsersAPI
usersServer pgInfo = 
  (fetchUsersHandler pgInfo) :<|> 
  (createUserHandler pgInfo)
```

Servant 负责确保每个端点的类型与处理程序的类型相匹配。挺牛逼的。假设我们改变了我们的`fetchUsersHandler`的类型，这样它用了一个`Key User`而不是一个`Int64`。我们会得到一个编译错误:

```
fetchUsersHandler :: ConnectionString -> Int64 -> Handler User
…-- Compile Error!
• Couldn't match type ‘Key User’ with ‘Int’
      Expected type: Server UsersAPI
        Actual type: (Key User -> Handler User)
                     :<|> (User -> Handler Int64)
```

现在，我们的 API 定义和处理程序定义之间存在不匹配。所以仆人知道抛出一个错误！一个问题是错误消息有时很难解释。尤其是当你的 API 变得非常大的时候！上面错误的“实际类型”部分会变得海量！因此，在更改您的端点时一定要小心！[勤编](https://mmhaskell.com/blog/2017/4/3/compile-driven-learning)是你的朋友！

# 构建应用程序

拼图的最后一块是在我们的服务器上实际构建一个`Application`对象。这个过程的第一步是为我们的 API 创建一个`Proxy`。请记住，我们的 API 是一种类型，而不是一个术语。但是一个`Proxy`允许我们在术语层次上表示这种类型。概念有点复杂，但代码并不复杂！

```
import Data.Proxy…usersAPI :: Proxy UsersAPI
usersAPI = Proxy :: Proxy UsersAPI
```

现在我们可以这样创建我们的 runnable `Application`(假设我们有一个 Postgres 连接):

```
serve usersAPI (usersServer connString)
```

我们将通过使用`run`函数从端口 8000 运行这个服务器，也是从`Network.Wai`运行。(完整的进口清单见 [Github](https://github.com/jhb563/ProdHaskellSeries/blob/servant/src/API.hs) )。我们将获取我们的连接字符串，然后我们就可以开始了！

```
runServer :: IO ()
runServer = do
  pgInfo <- fetchPostgresConnection
  run 8000 (serve usersAPI (usersServer pgInfo))
```

# 结论

仆人库提供了一些真正令人敬畏的可能性。我们能够在类型级别定义 web API。然后，我们可以使用端点期望的参数来定义处理函数。Servant 处理所有在 HTTP 请求和本地 Haskell 类型之间来回编组的工作。它还确保了端点和处理函数类型之间的匹配！

如果你想看到 Servant 提供的更多可能性，你应该看看我在 Bayhac 的演讲。它经历了一些更高级的概念，如认证和客户端功能。你可以在这里获得那个演讲[的幻灯片和所有代码示例。](https://www.mmhaskell.com/bayhac)

如果您以前从未尝试过 Haskell，那么现在是开始的最佳时机！下载我们的[入门清单](https://www.mmhaskell.com/checklist)，获取一些工具来帮助您开始 Haskell 之旅！