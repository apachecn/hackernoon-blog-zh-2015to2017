# 有效地组织我们的效果

> 原文：<https://medium.com/hackernoon/organizing-our-effects-effectively-38bd95029339>

在过去的 5 周左右，我们已经建立了一个 web 应用程序，公开了一个小的 API。该应用程序非常狭窄，仅包含少量功能。但它仍然很深入，涵盖了几种不同的库和技术。

在接下来的几周里，我们将讨论一些架构上的考虑。我们将观察这个系统的一些弱点，以及如何改进它们。本周我们将关注一种使用类型类和单子变形器的方法。几周后，我们将考虑免费单子，以及如何使用它们。

您可以跟随 Github repo 的 [effects-1](https://github.com/jhb563/ProdHaskellSeries/tree/effects-1) 分支上的代码。

# 弱点

在我们当前的系统中，有很多类似这样的不同功能:

```
fetchUserPG :: PGInfo -> Int64 -> IO (Maybe User)
createUserPG :: PGInfo -> User -> IO Int64
cacheUser :: RedisInfo -> Int64 -> User -> IO ()
```

现在，参数告诉我们每个函数应该访问什么。但是函数还是常规的`IO`函数。这意味着一个程序员新手可能会认为使用任意效果是没问题的。例如，为什么不从 Redis 函数中获取 Postgres 信息呢？毕竟，`fetchPGInfo`也是一个`IO`函数:

```
fetchPostgresConnection :: IO PGInfo
...cacheUser :: RedisInfo -> Int64 -> User -> IO ()
cacheUser = do
  pgInfo <- fetchPostgresConnection
  -- Connect to Postgres instead of Redis :(
```

我们的 API 在处理函数中也有一些不舒服的提升。我们必须调用`liftIO`，因为我们所有的数据库函数都是`IO`函数。

```
fetchUsersHandler :: PGInfo -> RedisInfo -> Int64 -> Handler User
fetchUsersHandler pgInfo redisInfo uid = do
  -- liftIO #1
  maybeCachedUser <- liftIO $ fetchUserRedis redisInfo uid
  case maybeCachedUser of
    Just user -> return user
    Nothing -> do
      -- liftIO #2
      maybeUser <- liftIO $ fetchUserPG pgInfo uid
      case maybeUser of
        -- liftIO #3
        Just user -> liftIO (cacheUser redisInfo uid user) >> return user
        Nothing -> Handler $ (throwE $ err401 { errBody = "Could not find user with that ID" })
```

至少，我们的连接参数在这里是显式的。如果我们把它们藏在阅读器里，这将会带来更多的提升。

这篇文章将集中在使用类型类来限制我们如何使用效果。如果运气好的话，我们还会稍微清理一下代码，让测试变得更容易。但是下周我们会更加关注更多的测试。

现在，根据项目的规模和范围，这些弱点可能不是问题。但这绝对是一个有用的练习，可以看到组织代码的其他方法。

# 定义我们的类型类

限制效果的第一步是创建两个类型类。我们将有一个用于主数据库，一个用于缓存。我们将努力使这些函数与底层数据库表示无关。因此，我们将改变我们的 API 来移除`Entity`的概念。我们将用`KeyVal`的概念来代替它，它是一个元组的包装器。

```
newtype KeyVal a = KeyVal (Int64, a)
```

至此，我们有了 8 个访问数据库的功能:

```
class (Monad m) => MonadDatabase m where
  fetchUserDB :: Int64 -> m (Maybe User) 
  createUserDB :: User -> m Int64 
  deleteUserDB :: Int64 -> m ()
  fetchArticleDB :: Int64 -> m (Maybe Article)
  createArticleDB :: Article -> m Int64
  deleteArticleDB :: Int64 -> m ()
  fetchArticlesByAuthor :: Int64 -> m [KeyVal Article]
  fetchRecentArticles :: m [(KeyVal User, KeyVal Article)]
```

然后，我们有三个函数用于与缓存交互:

```
class (Monad m) => MonadCache m where
  cacheUser :: Int64 -> User -> m ()
  fetchCachedUser :: Int64 -> m (Maybe User)
  deleteCachedUser :: Int64 -> m ()
```

我们现在可以为我们想要使用的任何不同的 monad 创建这些类型类的实例。让我们从描述现有库的实现开始。

# 编写实例

我们从`SqlPersistT`开始。我们想为它制作一个`MonadDatabase`的实例。我们将收集前几篇文章中所有不同的功能。

```
instance (MonadIO m, MonadLogger m) => MonadDatabase (SqlPersistT m) where
  fetchUserDB uid = get (toSqlKey uid) createUserDB user = fromSqlKey <$> insert user deleteUserDB uid = delete (toSqlKey uid :: Key User) fetchArticleDB aid = ((fmap entityVal) . listToMaybe) <$> (select . from $ \articles -> do
    where_ (articles ^. ArticleId ==. val (toSqlKey aid))
    return articles) createArticleDB article = fromSqlKey <$> insert article deleteArticleDB aid = delete (toSqlKey aid :: Key Article) fetchArticlesByAuthor uid = do
    entities <- select . from $ \articles -> do
      where_ (articles ^. ArticleAuthorId ==. val (toSqlKey uid))
      return articles
    return $ unEntity <$> entities fetchRecentArticles = do
    tuples <- select . from $ \(users `InnerJoin` articles) -> do
      on (users ^. UserId ==. articles ^. ArticleAuthorId)
      orderBy [desc (articles ^. ArticlePublishedTime)]
      limit 10
      return (users, articles)
    return $ (\(userEntity, articleEntity) -> (unEntity userEntity, unEntity articleEntity)) <$> tuples
```

因为我们从 API 中移除了`Entity`，所以我们使用了这个`unEntity`函数。它会把钥匙和价值还给我们作为`KeyVal`:

```
unEntity :: (ToBackendKey SqlBackend a) => Entity a -> KeyVal a
unEntity (Entity id_ val_) = KeyVal (fromSqlKey id_, val_)
```

现在我们将对缓存函数做同样的事情。我们将为`Redis`单子制作一个`MonadCache`的实例:

```
instance MonadCache Redis where
  cacheUser uid user = void $ setex (pack . show $ uid) 3600 (pack . show $ user)
  fetchCachedUser uid = do
    result <- get (pack . show $ uid)
    case result of
      Right (Just userString) -> return $ Just (read . unpack $ userString)
      _ -> return Nothing
  deleteCachedUser uid = void $ del [pack . show $ uid]
```

这就是全部了！让我们看看如何在我们的 API 中将这些结合起来以便于使用。

# 制作我们的应用程序 Monad

我们想要描述一个“应用单子”，它将允许我们轻松地访问这两个功能。我们将制作一个 monad 转换器的包装器，包含一个 Redis 信息和`SqlPersistT` monad 的阅读器。我们使用`GeneralizedNewtypeDeriving`推导出此类型的`Monad`:

```
{-# LANGUAGE GeneralizedNewtypeDeriving #-}newtype AppMonad a = AppMonad (ReaderT RedisInfo (SqlPersistT (LoggingT IO)) a)
  deriving (Functor, Applicative, Monad)
```

现在，我们想创建`MonadDatabase`和`MonadCache`的实例。尽管实例很简单；我们将使用底层单子的实例。首先，让我们定义一个从`SqlPersistT`动作到`AppMonad`动作的转换。我们需要为此构建`ReaderT RedisInfo`。我们将使用`ReaderT`构造函数并忽略带有`const`的信息。

```
liftSqlPersistT :: SqlPersistT (LoggingT IO) a -> AppMonad a
liftSqlPersistT action = AppMonad $ ReaderT (const action)
```

我们还可以定义 Redis 操作的转换:

```
liftRedis :: Redis a -> AppMonad a
liftRedis action = do
  info <- AppMonad ask
  connection <- liftIO $ connect info
  liftIO $ runRedis connection action
```

我们将像这样应用我们的底层实例:

```
instance MonadDatabase AppMonad where
  fetchUserDB = liftSqlPersistT . fetchUserDB
  createUserDB = liftSqlPersistT . createUserDB
  deleteUserDB = liftSqlPersistT . deleteUserDB
  fetchArticleDB = liftSqlPersistT . fetchArticleDB
  createArticleDB = liftSqlPersistT . createArticleDB
  deleteArticleDB = liftSqlPersistT . deleteArticleDB
  fetchArticlesByAuthor = liftSqlPersistT . fetchArticlesByAuthor
  fetchRecentArticles = liftSqlPersistT fetchRecentArticlesinstance MonadCache AppMonad where
  cacheUser uid user = liftRedis (cacheUser uid user)
  fetchCachedUser = liftRedis . fetchCachedUser 
  deleteCachedUser = liftRedis . deleteCachedUser
```

就是这样！我们有自己的例子。现在，我们想继续前进，并弄清楚如何将这个新的单子合并到我们的 API 中。

# 写一个自然的转变

我们想让我们的处理函数可以使用`AppMonad`而不是`Handler`单子。但是 Servant 被硬编码为使用`Handler`，那么我们该怎么办呢？答案是我们定义的“自然转化”。

我发现这个术语有点像“类别”。这似乎无关痛痒，但实际上是指一些深刻的数学问题。但是用起来不需要了解太多。类型运算符`(:~>)`定义了一个自然转换。我们需要做的只是一个函数，它在我们的 monad 中获取一个动作，并将其转换为处理程序 monad 中的一个动作。我们将需要传递我们的连接信息，以使这一工作。

```
transformAppToHandler :: PGInfo -> RedisInfo -> AppMonad :~> Handler
```

我们将从定义一个“处理程序”开始，它将捕捉我们抛出的任何错误，并将它们重新转换为服务错误。一般来说，您希望列出将要捕获的特定类型的异常。像这样捕捉每个异常并不是一个好主意。但是对于这个例子，我们将保持简单:

```
handler :: SomeException -> IO (Either ServantErr a)
handler e = return $ Left $ err500 { errBody = pack (show e)}
```

注意这返回了一个`Either`，它总是一个`Left`。现在让我们定义如何将一个动作从我们的“AppMonad”转换成一个`Either`。我们将得到结果，并将其作为一个`Right`值传递。

```
runAppAction :: Exception e => AppMonad a -> IO (Either e a)
runAppAction (AppMonad action) = do
  result <- runPGAction pgInfo $ runReaderT action redisInfo
  return $ Right result
```

综合起来，这就是我们的转变。我们捕捉错误，然后将结果包装在`Handler`中。

```
transformAppToHandler :: PGInfo -> RedisInfo -> AppMonad :~> Handler
transformAppToHandler pgInfo redisInfo = NT $ \action -> do
  result <- liftIO (handleAny handler (runAppAction action))
  Handler $ either throwError return result
  ...
```

# 整合应用 Monad

我们现在要做的就是将新的单子合并到我们的处理程序中。首先，让我们更改 API 以删除实体:

```
type FullAPI =
       "users" :> Capture "userid" Int64 :> Get '[JSON] User
  :<|> "users" :> ReqBody '[JSON] User :> Post '[JSON] Int64
  :<|> "articles" :> Capture "articleid" Int64 :> Get '[JSON] Article
  :<|> "articles" :> ReqBody '[JSON] Article :> Post '[JSON] Int64
  :<|> "articles" :> "author" :> Capture "authorid" Int64 :> Get '[JSON] [KeyVal Article]
  :<|> "articles" :> "recent" :> Get '[JSON] [(KeyVal User, KeyVal Article)]
```

我们想更新每个函数的类型。`AppMonad`包含所有配置信息。所以我们不需要显式传递连接信息。相反，我们可以在我们的 monad 类型类上使用约束来暴露这些效果。下面是我们的类型签名的样子:

```
fetchUsersHandler :: (MonadDatabase m, MonadCache m) => Int64 -> m User
createUserHandler :: (MonadDatabase m) => User -> m Int64
fetchArticleHandler :: (MonadDatabase m) => Int64 -> m Article
createArticleHandler :: (MonadDatabase m)=> Article -> m Int64
fetchArticlesByAuthorHandler :: (MonadDatabase m) => Int64 -> m [KeyVal Article]
fetchRecentArticlesHandler :: (MonadDatabase m) => m [(KeyVal User, KeyVal Article)]
```

现在我们的很多函数都是简单的一元调用。我们甚至不需要使用“电梯”！

```
createUserHandler :: (MonadDatabase m) => User -> m Int64
createUserHandler = createUserDBcreateArticleHandler :: (MonadDatabase m)=> Article -> m Int64
createArticleHandler = createArticleDBfetchArticlesByAuthorHandler :: (MonadDatabase m) => Int64 -> m [KeyVal Article]
fetchArticlesByAuthorHandler = fetchArticlesByAuthorfetchRecentArticlesHandler :: (MonadDatabase m) => m [(KeyVal User, KeyVal Article)]
fetchRecentArticlesHandler = fetchRecentArticles
```

“获取”函数有点复杂，因为我们想先检查缓存。但是，我们所有的函数都是简单的一元调用，没有使用任何提升。下面是我们的获取处理程序的样子:

```
fetchUsersHandler :: (MonadDatabase m, MonadCache m) => Int64 -> m User
fetchUsersHandler uid = do
  maybeCachedUser <- fetchCachedUser uid
  case maybeCachedUser of
    Just user -> return user
    Nothing -> do
      maybeUser <- fetchUserDB uid
      case maybeUser of
        Just user -> cacheUser uid user >> return user
        Nothing -> error "Could not find user with that ID"fetchArticleHandler :: (MonadDatabase m) => Int64 -> m Article
fetchArticleHandler aid = do
  maybeArticle <- fetchArticleDB aid
  case maybeArticle of
    Just article -> return article
    Nothing -> error "Could not find article with that ID"
```

现在我们将改变我们的`Server`函数。我们将更新它，使它把我们的自然转换作为一个参数。然后我们将使用与转换相结合的`enter`函数。这就是仆人如何知道我们想要什么单子给我们的处理程序:

```
fullAPIServer :: (AppMoand :~> Handler) -> Server FullAPI
fullAPIServer naturalTransformation =
  enter naturalTransformation $
    fetchUsersHandler :<|>
    createUserHandler :<|>
    fetchArticleHandler :<|>
    createArticleHandler :<|>
    fetchArticlesByAuthorHandler :<|>
    fetchRecentArticlesHandlerrunServer :: IO ()
runServer = do
  pgInfo <- fetchPostgresConnection
  redisInfo <- fetchRedisConnection
  -- Pass the natural transformation as an argument!
  run 8000 (serve usersAPI (fullAPIServer (transformAppToHandler pgInfo redisInfo)))
```

现在我们完成了！

# 这种方法的缺点

当然，这个系统也不是没有弱点。特别是，有相当多的样板文件。如果我们不想固定单子堆栈的顺序，这一点尤其正确。例如，如果我们应用程序的另一部分将`SqlPersistT`放在`Redis`的上面会怎么样？如果我们想把其他的单子变形金刚混进去呢？为此，我们需要`MonadDatabase`和`MonadCache`的新实例。我们最终会写出更多简单的定义。当我们看自由单子时，我们将在几周内检查这个弱点的解决方案。

每当我们想要更新它们的功能时，我们还需要向我们的类型类添加新的函数。然后，我们将不得不更新该类型类的每个实例，这可能是相当痛苦的。我们拥有的实例越多，添加新功能就越困难。

# 结论

因此，通过一些有用的技巧，我们可以得到更简洁的代码。我们使用类型类来限制应用程序中的效果。通过为不同的单子编写这些类的实例，我们可以改变应用程序的行为。下周，我们将看到如何使用这个行为来编写更简单的测试！

当管理一个依赖如此之多的应用程序时，您需要合适的工具。我用 [Stack](https://docs.haskellstack.org/en/stable/README/) 来组织我所有的 Haskell 项目。查看我们免费的[堆栈迷你课程](http://academy.mondaymorninghaskell.com/p/your-first-haskell-project)了解更多信息！

但是如果你从来没有尝试过 Haskell，那就试试吧！看看我们的[入门清单](https://www.mmhaskell.com/checklist)。