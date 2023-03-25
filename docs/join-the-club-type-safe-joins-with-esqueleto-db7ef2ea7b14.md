# 加入俱乐部:类型安全加入 Esqueleto

> 原文：<https://medium.com/hackernoon/join-the-club-type-safe-joins-with-esqueleto-db7ef2ea7b14>

在过去的四篇左右的文章中，我们对 Haskell 库进行了一次真正的旋风之旅。我们使用 Persistent 创建了一个数据库模式，并使用它以类型安全的方式编写基本的 SQL 查询。我们看到了如何通过带有 Servant 的 API 来公开这个数据库。我们还使用 Redis 为该服务器添加了一些缓存。最后，我们围绕这个 API 的行为编写了一些基本测试。通过使用 Docker，我们使这些测试具有可重复性。

在本文中，我们将通过向模式中添加另一种类型来回顾整个过程。我们将为一个`Article`类型编写一些新的端点，并用一个外键将这个类型链接到我们现有的`User`类型。然后我们再学一个库: [Esqueleto](https://hackage.haskell.org/package/esqueleto) 。Esqueleto 允许我们编写类型安全的 SQL 连接，从而改进了 Persistent。

与之前的文章一样，本系列的 [Github 库](https://github.com/jhb563/ProdHaskellSeries)上有一个特定的分支。去那里看一看 [esqueleto 分支](https://github.com/jhb563/ProdHaskellSeries/tree/esqueleto)以查看本文的完整代码。

# 将文章添加到我们的架构中

所以我们的第一步是用我们的`Article`类型扩展我们的模式。我们将为每篇文章指定一个标题、一些正文和发布时间的时间戳。我们将看到的一个新特性是，我们将添加一个外键来引用撰写文章的用户。下面是它在我们的模式中的样子:

```
PTH.share [PTH.mkPersist PTH.sqlSettings, PTH.mkMigrate "migrateAll"] [PTH.persistLowerCase|
 User sql=users
   ... Article sql=articles
   title Text
   body Text
   publishedTime UTCTime
   authorId UserId
   UniqueTitle title
   deriving Show Read Eq
|]
```

我们可以在模式中使用`UserId`作为类型。当我们在数据库中创建表时，这将创建一个外键列。实际上，当我们在 Haskell 中使用它时，我们的文章类型将如下所示:

```
data Article = Article
 { articleTitle :: Text
 , articleBody :: Text
 , articlePublishedTime :: UTCTime
 , articleAuthorId :: Key User
 }
```

这意味着它不引用整个用户。相反，它包含该用户的 SQL 键。因为我们将把文章添加到我们的 API 中，所以我们也需要添加 ToJSON 和 FromJSON 实例。这些也是非常基本的，所以如果你好奇的话，你可以在这里查看它们。如果你对 JSON 实例感兴趣，看看这篇文章。

# 添加端点

现在我们将扩展我们的 API 来公开关于这些文章的某些信息。首先，我们将编写几个基本的端点来创建一篇文章，然后通过它的 ID 获取它:

```
type FullAPI = 
      "users" :> Capture "userid" Int64 :> Get '[JSON] User
 :<|> "users" :> ReqBody '[JSON] User :> Post '[JSON] Int64
 :<|> "articles" :> Capture "articleid" Int64 :> Get '[JSON] Article
 :<|> "articles" :> ReqBody '[JSON] Article :> Post '[JSON] Int64
```

现在，我们将编写几个特殊的端点。第一个将用户 ID 作为一个键，然后它将提供用户写的所有不同的文章。我们将这个端点称为`/articles/author/:authorid`。

```
...
 :<|> "articles" :> "author" :> Capture "authorid" Int64 :> Get '[JSON] [Entity Article]
```

我们的最后一个端点将获取最新的文章，最多 10 篇。这将不带任何参数，并在`/articles/recent`路线上运行。它将返回用户及其文章的元组，两者都是实体。

```
…
 :<|> "articles" :> "recent" :> Get '[JSON] [(Entity User, Entity Article)]
```

# 添加查询(使用 Esqueleto！)

在我们实际实现这些端点之前，我们需要为它们编写基本的查询。为了创建一篇文章，我们使用标准的持久化`insert`函数:

```
createArticlePG :: PGInfo -> Article -> IO Int64
createArticlePG connString article = fromSqlKey <$> runAction connString (insert article)
```

我们可以对基本的获取端点做同样的事情。但是为了开始学习语法，我们将使用 Esqueleto 编写这个基本查询。对于 Persistent，我们使用列表参数来指定不同的过滤器和 SQL 操作。相反，Esqueleto 使用一个特殊的单子来组成不同类型的查询。esqueleto select 调用的一般格式如下所示:

```
fetchArticlePG :: PGInfo -> Int64 -> IO (Maybe Article)
fetchArticlePG connString aid = runAction connString selectAction
 where
   selectAction :: SqlPersistT (LoggingT IO) (Maybe Article)
   selectAction = select . from $ \articles -> do
     ...
```

我们使用`select . from`，然后提供一个接受表变量的函数。我们的第一个查询将只引用单个表，但我们稍后将看到一个连接。为了完成这个函数，我们将提供一元动作，它将合并查询的不同部分。

我们可以从这个单子中调用的最基本的过滤函数是`where_`。这允许我们在查询中提供一个条件，就像我们在 Persistent 中使用过滤器列表一样。

```
selectAction :: SqlPersistT (LoggingT IO) (Maybe Article)
   selectAction = select . from $ \articles -> do
     where_ (articles ^. ArticleId ==. val (toSqlKey aid))
```

首先，我们使用`ArticleId`透镜来指定我们要过滤的表的值。然后我们指定要比较的值。我们不仅需要将我们的`Int64`提升到一个`SqlKey`，而且我们还需要使用`val`函数提升那个值。

但是现在我们已经添加了这个条件，我们需要做的就是返回表变量。现在，`select`在一个列表中返回我们的结果。但是因为我们是通过 ID 搜索，所以我们只期望一个结果。我们将使用`listToMaybe`,所以我们只返回 head 元素，如果它存在的话。我们还将再次使用`entityVal`来打开文章的实体。

```
selectAction :: SqlPersistT (LoggingT IO) (Maybe Article)
   selectAction = ((fmap entityVal) . listToMaybe) <$> (select . from $ \articles -> do
     where_ (articles ^. ArticleId ==. val (toSqlKey aid))
     return articles)
```

现在我们应该知道了足够多的信息，可以写出下一个查询。它将获取特定用户写的所有文章。我们仍然会在`articles`表上进行查询。但是现在我们检查文章 ID，我们将确保`ArticleAuthorId`等于某个值。同样，我们将把我们的`Int64`用户密钥提升到一个`SqlKey`中，然后再与`val`在“SQL-land”中进行比较。

```
fetchArticleByAuthorPG :: PGInfo -> Int64 -> IO [Entity Article]
fetchArticleByAuthorPG connString uid = runAction connString fetchAction
 where
   fetchAction :: SqlPersistT (LoggingT IO) [Entity Article]
   fetchAction = select . from $ \articles -> do
     where_ (articles ^. ArticleAuthorId ==. val (toSqlKey uid))
     return articles
```

这就是完整的查询！这次我们想要一个实体列表，所以我们去掉了`listToMaybe`和`entityVal`。

现在让我们编写最后一个查询，在这里我们将找到最近的 10 篇文章，不管是谁写的。我们将在每篇文章中包含作者。所以我们返回这些不同实体元组的列表。这个查询将涉及我们的第一个连接。我们将使用`InnerJoin`构造函数将`users`表和`articles`表组合起来，而不是使用单个表进行查询。

```
fetchRecentArticlesPG :: PGInfo -> IO [(Entity User, Entity Article)]
fetchRecentArticlesPG connString = runAction connString fetchAction
 where
   fetchAction :: SqlPersistT (LoggingT IO) [(Entity User, Entity Article)]
   fetchAction = select . from $ \(users `InnerJoin` articles) -> do
```

因为我们要将两个表连接在一起，所以我们需要指定要连接的列。为此，我们将使用`on`函数:

```
fetchAction :: SqlPersistT (LoggingT IO) [(Entity User, Entity Article)]
   fetchAction = select . from $ \(users `InnerJoin` articles) -> do
     on (users ^. UserId ==. articles ^. ArticleAuthorId)
```

现在我们将使用`orderBy`根据文章的时间戳对文章进行排序。最新的文章应该排在最前面，所以我们将使用降序排列。然后我们用`limit`函数限制结果的数量。最后，我们将返回用户和文章，我们就完成了！

```
fetchAction :: SqlPersistT (LoggingT IO) [(Entity User, Entity Article)]
   fetchAction = select . from $ \(users `InnerJoin` articles) -> do
     on (users ^. UserId ==. articles ^. ArticleAuthorId)
     orderBy [desc (articles ^. ArticlePublishedTime)]
     limit 10
     return (users, articles)
```

# 缓存不同类型的项目

我们不会深入研究在 Redis 中缓存文章的细节，但是有一个潜在的问题我们想观察一下。目前，我们在 Redis 存储中使用用户的 SQL 键作为他们的键。例如，字符串“15”可以是这样一个键。如果我们试图天真地在我们的文章中使用同样的想法，我们将会有冲突！试图存储 ID 为“15”的文章将会覆盖包含该用户的条目！

但是解决这个问题的方法很简单。我们要做的是，对于用户的密钥，我们将使字符串类似于`users:15`。那么对于我们的文章，我们将它的键设为`articles:15`。只要我们以正确的方式反序列化它，就不会有问题。

# 填写服务器处理程序

既然我们已经编写了数据库查询函数，那么填充服务器处理程序就非常简单了。其中大多数都归结为遵循我们已经为其他两个端点设置的模式:

```
fetchArticleHandler :: PGInfo -> Int64 -> Handler Article
fetchArticleHandler pgInfo aid = do
 maybeArticle <- liftIO $ fetchArticlePG pgInfo aid
 case maybeArticle of
   Just article -> return article
   Nothing -> Handler $ (throwE $ err401 { errBody = "Could not find article with that ID" })createArticleHandler :: PGInfo -> Article -> Handler Int64
createArticleHandler pgInfo article = liftIO $ createArticlePG pgInfo articlefetchArticlesByAuthorHandler :: PGInfo -> Int64 -> Handler [Entity Article]
fetchArticlesByAuthorHandler pgInfo uid = liftIO $ fetchArticlesByAuthorPG pgInfo uidfetchRecentArticlesHandler :: PGInfo -> Handler [(Entity User, Entity Article)]
fetchRecentArticlesHandler pgInfo = liftIO $ fetchRecentArticlesPG pgInfo
```

然后我们将像这样完成我们的`Server FullAPI`:

```
fullAPIServer :: PGInfo -> RedisInfo -> Server FullAPI
fullAPIServer pgInfo redisInfo =
 (fetchUsersHandler pgInfo redisInfo) :<|>
 (createUserHandler pgInfo) :<|>
 (fetchArticleHandler pgInfo) :<|>
 (createArticleHandler pgInfo) :<|>
 (fetchArticlesByAuthorHandler pgInfo) :<|>
 (fetchRecentArticlesHandler pgInfo)
```

我们可以做的一件有趣的事情是，我们可以将我们的 API 类型组成不同的部分。例如，我们可以将我们的`FullAPI`分成两部分。首先，我们可以从以前的`UsersAPI`型，然后我们可以为`ArticlesAPI`做一个新的类型。我们可以用 e-plus 操作符将它们粘合在一起，就像我们可以粘合单独的端点一样！

```
type FullAPI = UsersAPI :<|> ArticlesAPItype UsersAPI =
      "users" :> Capture "userid" Int64 :> Get '[JSON] User
 :<|> "users" :> ReqBody '[JSON] User :> Post '[JSON] Int64type ArticlesAPI =
 "articles" :> Capture "articleid" Int64 :> Get '[JSON] Article
 :<|> "articles" :> ReqBody '[JSON] Article :> Post '[JSON] Int64
 :<|> "articles" :> "author" :> Capture "authorid" Int64 :> Get '[JSON] [Entity Article]
 :<|> "articles" :> "recent" :> Get '[JSON] [(Entity User, Entity Article)]
```

如果我们这样做，我们将不得不在结合端点的其他区域进行类似的调整。例如，我们需要更新服务器处理程序连接和客户端功能。

# 写作测试

因为我们已经有了一些用户测试，所以在 API 的文章部分进行一些测试也是不错的。我们将围绕创建一篇文章并获取它添加一个简单的测试。然后，我们将为“按作者分类的文章”和“最近的文章”端点各添加一个测试。

所以填充这个部分的一个棘手的部分是我们需要制作 test `Article`对象。但是我们需要它们是用户 ID 上的函数。这是因为当我们将用户插入数据库时，我们无法先验地知道我们将得到什么 SQL IDs。但是我们可以填写所有其他字段，包括发布时间。这里有一个例子，但是我们总共有 18 篇不同的“测试”文章。

```
testArticle1 :: Int64 -> Article
testArticle1 uid = Article
 { articleTitle = "First post"
 , articleBody = "A great description of our first blog post body."
 , articlePublishedTime = posixSecondsToUTCTime 1498914000
 , articleAuthorId = toSqlKey uid
 }-- 17 other articles and some test users as well
…
```

我们的 before 钩子将在数据库中创建所有这些不同的实体。一般来说，我们将直接进入数据库，而不调用 API 本身。像我们的用户测试一样，我们想要删除我们创建的任何数据库项。让我们编写一个通用的后挂钩，它将获取用户 id 和文章 id，并将它们从数据库中删除:

```
deleteArtifacts :: PGInfo -> RedisInfo -> [Int64] -> [Int64] -> IO ()
deleteArtifacts pgInfo redisInfo users articles = do
 void $ forM articles $ \a -> deleteArticlePG pgInfo a
 void $ forM users $ \u -> do
   deleteUserCache redisInfo u
   deleteUserPG pgInfo u
```

重要的是，我们首先删除文章！如果我们先删除用户，我们会遇到外键异常！

我们的基本创建和获取测试看起来很像前面的用户测试。我们测试响应是否成功，新文章是否如我们预期的那样存在于 Postgres 中。

```
beforeHook4 :: ClientEnv -> PGInfo -> IO (Bool, Bool, Int64, Int64)
beforeHook4 clientEnv pgInfo = do
 userKey <- createUserPG pgInfo testUser2
 articleKeyEither <- runClientM (createArticleClient (testArticle1 userKey)) clientEnv
 case articleKeyEither of
   Left _ -> error "DB call failed on spec 4!"
   Right articleKey -> do
     fetchResult <- runClientM (fetchArticleClient articleKey) clientEnv
     let callSucceeds = isRight fetchResult
     articleInPG <- isJust <$> fetchArticlePG pgInfo articleKey
     return (callSucceeds, articleInPG, userKey, articleKey)spec4 :: SpecWith (Bool, Bool, Int64, Int64)
spec4 = describe "After creating and fetching an article" $ do
 it "The fetch call should return a result" $ \(succeeds, _, _, _) -> succeeds `shouldBe` True
 it "The article should be in Postgres" $ \(_, inPG, _, _) -> inPG `shouldBe` TrueafterHook4 :: PGInfo -> RedisInfo -> (Bool, Bool, Int64, Int64) -> IO ()
afterHook4 pgInfo redisInfo (_, _, uid, aid) = deleteArtifacts pgInfo redisInfo [uid] [aid]
```

我们的下一个测试将创建两个不同的用户和几篇不同的文章。我们将首先插入用户并获取他们的密钥。然后我们可以使用这些密钥来创建文章。我们在这个测试中创建了五篇文章。我们将三个分配给第一个用户，两个分配给第二个用户:

```
beforeHook5 :: ClientEnv -> PGInfo -> IO ([Article], [Article], Int64, Int64, [Int64])
beforeHook5 clientEnv pgInfo = do
 uid1 <- createUserPG pgInfo testUser3
 uid2 <- createUserPG pgInfo testUser4
 articleIds <- mapM (createArticlePG pgInfo)
   [ testArticle2 uid1, testArticle3 uid1, testArticle4 uid1
   , testArticle5 uid2, testArticle6 uid2 ]
 ...
```

现在我们想测试一下，当我们调用按用户排序的文章端点时，我们只得到正确的文章。我们将返回每组文章、用户 id 和文章 id 列表:

```
beforeHook5 :: ClientEnv -> PGInfo -> IO ([Article], [Article], Int64, Int64, [Int64])
beforeHook5 clientEnv pgInfo = do
 uid1 <- createUserPG pgInfo testUser3
 uid2 <- createUserPG pgInfo testUser4
 articleIds <- mapM (createArticlePG pgInfo)
   [ testArticle2 uid1, testArticle3 uid1, testArticle4 uid1
   , testArticle5 uid2, testArticle6 uid2 ]
 firstArticles <- runClientM (fetchArticlesByAuthorClient uid1) clientEnv
 secondArticles <- runClientM (fetchArticlesByAuthorClient uid2) clientEnv
 case (firstArticles, secondArticles) of
   (Right as1, Right as2) -> return (entityVal <$> as1, entityVal <$> as2, uid1, uid2, articleIds)
   _ -> error "Spec 5 failed!"
```

现在我们可以编写断言本身，测试返回的文章是否是我们所期望的。

```
spec5 :: SpecWith ([Article], [Article], Int64, Int64, [Int64])
spec5 = describe "When fetching articles by author ID" $ do
 it "Fetching by the first author should return 3 articles" $ \(firstArticles, _, uid1, _, _) ->
   firstArticles `shouldBe` [testArticle2 uid1, testArticle3 uid1, testArticle4 uid1]
 it "Fetching by the second author should return 2 articles" $ \(_, secondArticles, _, uid2, _) ->
   secondArticles `shouldBe` [testArticle5 uid2, testArticle6 uid2]
```

然后我们会用一个类似的 after 钩子跟踪它。

最终测试将遵循类似的模式。只是这一次，我们将检查用户和文章的组合。我们还将确保包含 12 篇不同的文章，以测试 API 将结果限制为 10 个。

```
beforeHook6 :: ClientEnv -> PGInfo -> IO ([(User, Article)], Int64, Int64, [Int64])
beforeHook6 clientEnv pgInfo = do
 uid1 <- createUserPG pgInfo testUser5
 uid2 <- createUserPG pgInfo testUser6
 articleIds <- mapM (createArticlePG pgInfo)
   [ testArticle7 uid1, testArticle8 uid1, testArticle9 uid1, testArticle10 uid2
   , testArticle11 uid2, testArticle12 uid1, testArticle13 uid2, testArticle14 uid2
   , testArticle15 uid2, testArticle16 uid1, testArticle17 uid1, testArticle18 uid2
   ]
 recentArticles <- runClientM fetchRecentArticlesClient clientEnv
 case recentArticles of
   Right as -> return (entityValTuple <$> as, uid1, uid2, articleIds)
   _ -> error "Spec 6 failed!"
 where
   entityValTuple (Entity _ u, Entity _ a) = (u, a)
```

我们的 spec 将检查我们得到的 10 篇文章的列表是否符合我们的期望。然后，像往常一样，我们从数据库中删除实体。

现在我们用其他测试来调用这些测试，用小包装器来调用钩子:

```
main :: IO ()
main = do
 ...
 hspec $ before (beforeHook4 clientEnv pgInfo) $ after (afterHook4 pgInfo redisInfo) $ spec4
 hspec $ before (beforeHook5 clientEnv pgInfo) $ after (afterHook5 pgInfo redisInfo) $ spec5
 hspec $ before (beforeHook6 clientEnv pgInfo) $ after (afterHook6 pgInfo redisInfo) $ spec6
```

现在我们完成了！测试通过了！

```
…
After creating and fetching an article
 The fetch call should return a result
 The article should be in PostgresFinished in 0.1698 seconds
2 examples, 0 failuresWhen fetching articles by author ID
 Fetching by the first author should return 3 articles
 Fetching by the second author should return 2 articlesFinished in 0.4944 seconds
2 examples, 0 failuresWhen fetching recent articles
 Should fetch exactly the 10 most recent articles
```

# 结论

这就完成了我们对有用的产品库的概述。在这些文章中，我们从头开始构建了一个小的 web API。我们已经看到了一些令人敬畏的抽象，它们让我们只处理项目中最重要的部分。Persistent 和 Servant 都为我们生成了许多额外的样板文件。本文展示了 Esqueleto 库在允许我们进行类型安全连接方面的强大功能。我们还看到了向我们的 API 添加新类型和端点的端到端过程。

在接下来的几周里，我们将会处理更多在构建这类系统时可能出现的问题。特别是，我们将看到如何在 Servant 之上使用可选的单子。这样做可能会提出我们将要探讨的某些问题。我们将通过探索封装效果的不同方法来达到高潮。

一定要看看我们的 [Haskell Stack 迷你课程！](http://academy.mondaymorninghaskell.com/p/your-first-haskell-project)！它将向您展示如何使用 Stack，这样您就可以集成本系列中的所有库了！

如果你是 Haskell 新手，还没有准备好，看看我们的[入门清单](https://www.mmhaskell.com/checklist)并开始行动吧！