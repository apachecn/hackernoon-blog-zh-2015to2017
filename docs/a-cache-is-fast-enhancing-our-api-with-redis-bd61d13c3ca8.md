# 缓存更快:用 Redis 增强我们的 API

> 原文：<https://medium.com/hackernoon/a-cache-is-fast-enhancing-our-api-with-redis-bd61d13c3ca8>

在过去的几周里，我们已经使用[持久化](https://hackage.haskell.org/package/persistent)在 Postgresql 数据库中存储了一个`User`类型。然后我们能够使用 [Servant](https://hackage.haskell.org/package/servant) 创建一个非常简单的 API，将这个数据库暴露给外界。本周，我们将看看如何使用 [Redis](https://redis.io/) 缓存来提高 API 的性能。

无论怎样强调缓存在软件和硬件中的重要性都不为过。内存类型有一个层次结构，从寄存器到 RAM，到文件系统，再到远程数据库。访问每一个都变得越来越慢(数量级)。但是更快的存储方式更贵，所以我们不能总是想要多少就有多少。

但是内存使用遵循一个非常重要的原则。当我们使用一块内存一次，我们很可能在不久的将来再次使用它。所以当我们把一些东西从长期记忆中提取出来时，我们也可以把它暂时储存在短期记忆中。这样，当我们再次需要它时，我们可以更快地得到它。在某个点之后，该项目将被其他更紧急的项目覆盖。这就是缓存的本质。

# Redis 101

Redis 是一个允许我们创建项目的键值存储的应用程序。它的功能类似于数据库，只是它只使用这些键。它缺少复杂的连接、外部表引用和索引。所以我们不能运行 SQL 数据库上可能的复杂查询。但是我们可以运行简单的键查找，而且速度更快。在本文中，我们将使用 Redis 作为用户对象的短期缓存。

对于本文，我们有一个缓存集成的主要目标。每当我们使用 API 中的`GET`端点“获取”用户时，我们都希望将该用户存储在 Redis 缓存中。下一次有人从我们的 API 中请求该用户时，我们将从缓存中获取他们。这将省去我们长时间调用 Postgres 数据库的麻烦。

# 连接到 Redis

Haskell 的 Redis 库与 Persistent 和 Postgres 有很多相似之处。首先，我们需要某种数据来告诉我们在哪里寻找我们的数据库。对于 Postgres，我们使用了一个简单的具有特定格式的`ConnectionString`。Redis 使用一种叫做`ConnectInfo`的完整数据类型。

```
data ConnectInfo = ConnectInfo
  { connectHost :: HostName -- String
  , connectPort :: PortId   -- (Can just be a number)
  , connectAuth :: Maybe ByteString
  , connectDatabase :: Integer
  , connectMaxConnection :: Int
  , connectMaxIdleTime :: NominalDiffTime
  }
```

其中有许多与我们存储在 PG 字符串中的字段相同的字段，比如主机 IP 地址和端口号。本文的其余部分假设您正在端口 6379 上运行一个本地 Redis 实例。这意味着我们可以使用`defaultConnectInfo`。和往常一样，在真实的系统中，您希望从配置中获取这些信息，所以您需要`IO`。

```
fetchRedisConnection :: IO ConnectInfo
fetchRedisConnection = return defaultConnectInfo
```

对于 Postgres，我们使用`withPostgresqlConn`来实际连接到数据库。在 Redis 中，我们用`connect`函数来完成这项工作。我们将得到一个`Connection`对象，我们可以用它来运行 Redis 操作。

```
connect :: ConnectInfo -> IO Connection
```

有了这个联系，我们就简单的用`runRedis`，然后结合一个动作。这是我们为此编写的包装器`runRedisAction`:

```
runRedisAction :: ConnectInfo -> Redis a -> IO a
runRedisAction redisInfo action = do
  connection <- connect redisInfo
  runRedis connection action
```

# 雷迪斯单子

正如我们将`SqlPersistT`单子用于 Persist 一样，我们将使用`Redis`单子与 Redis 缓存进行交互。我们的 API 很简单，所以我们将坚持使用三个基本函数。这些函数的真实类型稍微复杂一些。但这是因为与事务相关的多态性，我们不会使用它们。

```
get :: ByteString -> Redis (Either x (Maybe ByteString))
set :: ByteString -> ByteString -> Redis (Either x ())
setex :: ByteString -> ByteString -> Int -> Redis (Either x ())
```

Redis 是一个键值存储，所以我们在这里设置的所有东西都将使用`ByteString`项。但是一旦我们完成了这些，我们就只需要使用这些函数了。`get`函数获取键的一个`ByteString`，并将该值作为另一个`ByteString`传递。`set`函数获取序列化的键和值，并将它们存储在缓存中。`setex`函数的功能与`set`相同，只是它还为我们存储的项目设置了一个到期时间。

到期是一个非常有用的特性，因为大多数关系数据库没有这个特性。缓存的本质是，在任何给定的时间，它只能存储我们信息的一个子集。如果我们从来没有过期或删除任何东西，它最终可能会存储我们的整个数据库。这将违背使用缓存的目的！与我们的数据库相比，它的内存占用应该很低。所以我们将在我们的 API 中使用`setex`。

# 在 Redis 中保存用户

所以现在让我们继续我们将在 API 中实际使用的动作。首先，我们将编写一个函数，它将在数据库中实际存储一个`Int64`键和`User`键-值对。我们从这里开始:

```
cacheUser :: ConnectInfo -> Int64 -> User -> IO ()
cacheUser redisInfo uid user = runRedisAction redisInfo $ setex ??? ??? ???
```

我们现在需要做的就是将我们的键和值转换成`ByteString`值。我们将保持它的简单，并结合我们的`Show`和`Read`实例使用`Data.ByteString.Char8`。然后我们将使用`setex`创建一个`Redis`动作，并在 3600 秒(一小时)后使密钥过期。

```
import Data.ByteString.Char8 (pack, unpack)...cacheUser :: ConnectInfo -> Int64 -> User -> IO ()
cacheUser redisInfo uid user = runRedisAction redisInfo $ void $ 
  setex (pack . show $ uid) 3600 (pack . show $ user)
```

(我们使用`void`来忽略 Redis 调用的结果)。

# 从 Redis 获取

获取用户是一个类似的过程。我们会带走连接信息和我们要找的密钥。我们将创建的动作使用字节字符串表示并调用`get`。但是我们不能像以前那样忽视这次通话的结果！找回任何东西都会给我们`Either e (Maybe ByteString)`。一个`Left`响应指示一个错误，而`Right Nothing`指示该键不存在。我们将忽略这些错误，并将结果视为`Maybe User`。如果出现任何错误，我们将返回`Nothing`。这意味着我们运行一个简单的模式匹配:

```
fetchUserRedis :: ConnectInfo -> Int64 -> IO (Maybe User)
fetchUserRedis redisInfo uid = runRedisAction redisInfo $ do
  result <- Redis.get (pack . show $ uid)
  case result of
    Right (Just userString) -> return $ Just (read . unpack $ userString)
    _ -> return Nothing
```

如果我们为那个键找到了什么，我们将把它从它的`ByteString`格式中读取出来，然后我们就有了最终的`User`对象。

# 将此应用于我们的 API

现在我们已经设置好了 Redis 函数，我们需要更新`fetchUsersHandler`来使用这个缓存。首先，我们现在需要将 Redis 连接信息作为另一个参数传递。为了便于阅读，从现在开始，我们将使用类型同义词(`PGInfo`和`RedisInfo`)来引用它们:

```
type PGInfo = ConnectionString
type RedisInfo = ConnectInfo…fetchUsersHandler :: PGInfo -> RedisInfo -> Int64 -> Handler User
fetchUsersHandler pgInfo redisInfo uid = do
  ...
```

我们要做的第一件事是通过 Redis 缓存中的 ID 来查找用户。如果用户存在，我们将立即返回该用户。

```
fetchUsersHandler :: PGInfo -> RedisInfo -> Int64 -> Handler User
fetchUsersHandler pgInfo redisInfo uid = do
  maybeCachedUser <- liftIO $ fetchUserRedis redisInfo uid
  case maybeCachedUser of
    Just user -> return user
    Nothing -> do
      ...
```

如果用户不存在，那么我们将进入在数据库中获取用户的逻辑。如果我们发现用户实际上不存在，我们将重复抛出错误的逻辑。但是如果找到用户，我们还需要一步。在我们返回它之前，我们应该调用`cacheUser`并存储它以备将来使用。

```
fetchUsersHandler :: PGInfo -> RedisInfo -> Int64 -> Handler User
fetchUsersHandler pgInfo redisInfo uid = do
  maybeCachedUser <- liftIO $ fetchUserRedis redisInfo uid
  case maybeCachedUser of
    Just user -> return user
    Nothing -> do
      maybeUser <- liftIO $ fetchUserPG pgInfo uid
      case maybeUser of
        Just user -> liftIO (cacheUser redisInfo uid user) >> return user
        Nothing -> Handler $ (throwE $ err401 { errBody = "Could not find user with that ID" })
```

因为我们改变了我们的类型签名，我们还必须做一些其他的更新，但是这些都很简单:

```
usersServer :: PGInfo -> RedisInfo -> Server UsersAPI
usersServer pgInfo redisInfo =
  (fetchUsersHandler pgInfo redisInfo) :<|> 
  (createUserHandler pgInfo) runServer :: IO ()
runServer = do
  pgInfo <- fetchPostgresConnection
  redisInfo <- fetchRedisConnection
  run 8000 (serve usersAPI (usersServer pgInfo redisInfo))
```

就是这样！我们有一个带有过期条目的正常工作的缓存。这意味着对我们的获取端点的重复查询应该快得多！

# 结论

缓存是一种非常重要的方式，我们可以用它来为用户编写更快的软件。Redis 是一个键值存储，我们可以将它用作最常用数据的缓存。我们可以用它来代替强制每个 API 调用访问我们的数据库。在 Haskell 中，Redis API 要求所有东西都是`ByteString`。所以我们必须处理一些编码和解码的逻辑。但除此之外，它的运行方式与 Persistent 和 Postgres 非常相似。

一定要在 [Github](https://github.com/jhb563/ProdHaskellSeries/tree/redis) 上看看这段代码！这篇文章有一个`redis`分支。它包括所有的代码样本，包括我跳过的东西，比如导入！

我们开始在 Haskell 应用程序中使用许多不同的库！知道如何组织一切是值得的，所以包管理是至关重要的！我倾向于使用[栈](https://docs.haskellstack.org/en/stable/README/)来管理我所有的包。将所有这些不同的库整合在一起非常容易。如果您想学习如何使用 Stack，请查看我们免费的 [Stack 迷你课程](http://academy.mondaymorninghaskell.com/p/your-first-haskell-project)！

如果你以前从未学习过 Haskell，你应该尝试一下！下载我们的[入门清单](https://www.mmhaskell.com/checklist)！