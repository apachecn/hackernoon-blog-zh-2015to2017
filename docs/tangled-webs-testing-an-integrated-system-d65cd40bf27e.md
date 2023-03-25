# 纠结的网:测试集成系统

> 原文：<https://medium.com/hackernoon/tangled-webs-testing-an-integrated-system-d65cd40bf27e>

在过去的几篇文章中，我们结合了几个有用的 Haskell 库来制作一个小型的 web 应用程序。我们使用 [Persistent](https://hackage.haskell.org/package/persistent) 为我们的数据库创建一个自动迁移的模式。然后我们使用 [Servant](https://hackage.haskell.org/package/servant) 通过几个简单的查询将这个数据库公开为 API。最后，我们使用 [Redis](https://redis.io/) 作为缓存，以便对同一用户的重复请求发生得更快。

下一步，我们将处理一个棘手的问题:测试。我们如何测试一个有这么多移动部件的系统？我们可以采用几种不同的通用方法。在光谱的一端，我们可以模仿我们的大多数 API 调用和服务。这有助于给我们的测试确定性的行为。这是可取的，因为我们希望将部署与测试结果联系起来。但是我们也想忠实地测试我们的 API。另一方面，这是我们将在本文中尝试的方法。我们将设置函数来运行我们的 API 和其他服务，然后使用 before 和 after 钩子来使用它们。

# 为我们的 API 创建客户端函数

从测试中调用我们的 API 意味着我们需要一种以编程方式进行 API 调用的方法。通过使用`servant-client`库，我们可以用一个服务 API 非常轻松地做到这一点。这个库有一个主要功能:`client`。这个函数接受我们的 API 的代理，并生成可编程的客户端函数。让我们记住我们的基本端点类型(在解析连接信息参数之后):

```
fetchUsersHandler :: Int64 -> Handler User
createUserHandler :: User -> Handler Int64
```

我们希望能够用使用相同参数的函数调用这些 API。这些类型可能如下所示:

```
fetchUserClient :: Int64 -> m User
createUserClient :: User -> m Int64
```

其中`m`是一些单子。在这种情况下，`ServantClient`库提供了这样一个单子，`ClientM`。所以让我们重写这些类型签名，但是让它们看起来没有实现:

```
fetchUserClient :: Int64 -> ClientM User
createUserClient :: User -> ClientM Int64
```

现在我们将构建一个模式匹配，用`:<|>`操作符组合这些函数名。和往常一样，我们需要确保我们按照与原始 API 类型相同的顺序来做这件事。然后，我们将这个模式设置为在 API 的代理上调用`client`的结果:

```
fetchUserClient :: Int64 -> ClientM User
createUserClient :: User -> ClientM Int64
(fetchUserClient :<|> createUserClient) = client (Proxy :: Proxy UsersAPI)
```

就是这样！仆人库为我们填充细节并实现这些功能！我们将在本文的后面看到如何实际调用这些函数。

# 设置测试

我们想要开始决定我们的测试用例并编写它们。但是首先我们需要确保我们的测试有一个合适的环境。这意味着三件事。首先，我们需要获取数据存储和 API 的连接信息。这意味着我们将使用`PGInfo`、`RedisInfo`和`ClientEnv`来调用我们编写的客户端函数。第二，我们需要实际迁移我们的数据库，以便它有正确的表。第三，我们需要确保我们的服务器确实在运行。让我们从连接信息开始，因为这很简单:

```
import Database (fetchPostgresConnection, fetchRedisConnection)...setupTests = do
  pgInfo <- fetchPostgresConnection
  redisInfo <- fetchRedisConnection
  ...
```

现在要创建我们的客户机环境，我们需要两件主要的东西。我们需要一个网络连接管理器和 API 的基本 URL。因为我们在本地运行 API，所以我们将使用本地主机 URL。来自`Network`库的默认管理器对我们来说很好:

```
import Network.HTTP.Client (newManager)
import Network.HTTP.Client.TLS (tlsManagerSettings)
import Servant.Client (ClientEnv(..))main = do
  pgInfo <- fetchPostgresConnection
  redisInfo <- fetchRedisConnection
  mgr <- newManager tlsManagerSettings
  baseUrl <- parseBaseUrl "http://127.0.0.1:8000"
  let clientEnv = ClientEnv mgr baseUrl
```

现在我们可以运行我们的迁移，这将确保我们的`users`表存在:

```
import Schema (migrateAll)main = do
  pgInfo <- fetchPostgresConnection
  ...
  runStdoutLoggingT $ withPostgresqlConn pgInfo $ \dbConn ->
    runReaderT (runMigrationSilent migrateAll) dbConn
```

最后，我们将使用 API 模块中的`runServer`来启动服务器。我们将把它分叉到一个单独的线程，否则它将阻塞测试线程！之后，我们将等待一秒钟，以确保它在测试运行之前实际加载(当然，有更简单的方法可以做到这一点)。但是我们将返回我们需要的所有重要信息，并且我们完成了测试设置:

```
main :: IO (PGInfo, RedisInfo, ClientEnv, ThreadID)
main = do
  pgInfo <- fetchPostgresConnection
  redisInfo <- fetchRedisConnection
  mgr <- newManager tlsManagerSettings
  baseUrl <- parseBaseUrl "http://127.0.0.1:8000"
  let clientEnv = ClientEnv mgr baseUrl
  runStdoutLoggingT $ withPostgresqlConn pgInfo $ \dbConn ->
    runReaderT (runMigrationSilent migrateAll) dbConn
  threadId <- forkIO runServer
  threadDelay 1000000
  return (pgInfo, redisInfo, clientEnv, serverThreadId)
```

# 组织我们的 3 个测试案例

现在我们都设置好了，我们可以决定我们的测试用例了。我们来看看 3。首先，如果我们有一个空的数据库，我们通过任意的 ID 获取一个用户，我们将会遇到一个错误。此外，我们应该预期用户不存在于数据库或缓存中，即使在调用了`fetch`之后。

在我们的第二个测试案例中，我们将看看调用创建端点的效果。我们将保存从该端点获得的密钥。然后，我们将验证该用户存在于数据库中，但不存在于缓存中。最后，我们的第三个案例将使用创建端点插入用户，然后获取用户。我们希望在结束时，用户实际上同时存在于数据库和缓存中。

我们将每个测试组织成三个部分:“挂钩之前”、测试断言和“挂钩之后”。“挂钩之前”是我们将运行的一些 IO 代码，它将向我们的测试断言返回特定的结果。我们想确保它在任何测试断言之前都已经运行了。这样，在我们的测试输出和 API 调用之间就不会有交错的影响。每个 before hook 将首先进行我们想要的 API 调用。然后他们会调查我们不同的数据库，并确定是否存在某些用户。

我们还希望我们的测试是数据库中立的。也就是说，在测试之后，数据库和缓存应该处于与之前相同的状态。所以我们还会有“after hooks”在我们的测试完成后运行(如果我们真的创建了什么的话)。after 挂钩将删除任何新条目。这意味着我们的 before 钩子也必须为它们创建的任何数据库实体传递键。这样 after 钩子就知道该删除什么了。

最后，当然，我们实际上需要断言结果的测试代码。这些将非常简单，我们将在下面看到。

# 测试#1

对于我们的第一个测试，我们将从客户端调用我们的 API 开始。我们用`runClientM`结合我们的`clientEnv`和`fetchUserClient`函数。接下来，我们将确定该调用实际上会返回一个错误。然后，我们将添加两行代码，检查我们的数据库和缓存中是否有任意 ID 的条目。最后，我们返回所有三个布尔值:

```
beforeHook1 :: ClientEnv -> PGInfo -> RedisInfo -> IO (Bool, Bool, Bool)
beforeHook1 clientEnv pgInfo redisInfo = do
  callResult <- runClientM (fetchUserClient 1) clientEnv
  let throwsError = isLeft (callResult)
  inPG <- isJust <$> fetchUserPG pgInfo 1
  inRedis <- isJust <$> fetchUserRedis redisInfo 1
  return (throwsError, inPG, inRedis)
```

现在我们将写出我们的断言。因为我们使用一个 before hook 返回三个布尔值，所以我们的`Spec`的类型将是`SpecWith (Bool, Bool, Bool)`。每个`it`断言都将这个布尔元组作为参数，尽管我们每行只使用一个。

```
spec1 :: SpecWith (Bool, Bool, Bool)
spec1 = describe "After fetching on an empty database" $ do
  it "The fetch call should throw an error" $ \(throwsError, _, _) -> throwsError `shouldBe` True
  it "There should be no user in Postgres" $ \(_, inPG, _) -> inPG `shouldBe` False
  it "There should be no user in Redis" $ \(_, _, inRedis) -> inRedis `shouldBe` False
```

这就是我们第一次测试所需要的！我们不需要 after 挂钩，因为它不会给我们的数据库添加任何东西。

# 测试 2 和 3

现在我们对这段代码的工作原理有了更多的了解，让我们快速看一下下一个 before hook。这次我们将首先尝试创建我们的用户。如果由于某种原因失败了，我们将抛出一个错误并停止测试。然后，我们可以使用密钥来检查用户是否存在于我们的数据库和 Redis 中。我们返回布尔值和密钥。

```
beforeHook2 :: ClientEnv -> PGInfo -> RedisInfo -> IO (Bool, Bool, Int64)
beforeHook2 clientEnv pgInfo redisInfo = do
  userKeyEither <- runClientM (createUserClient testUser) clientEnv
  case userKeyEither of
    Left _ -> error "DB call failed on spec 2!"
    Right userKey -> do 
      inPG <- isJust <$> fetchUserPG pgInfo userKey
      inRedis <- isJust <$> fetchUserRedis redisInfo userKey
      return (inPG, inRedis, userKey)
```

现在我们的规范看起来很相似。这次我们期望在 Postgres 中找到一个用户，而不是在 Redis 中。

```
spec2 :: SpecWith (Bool, Bool, Int64)
spec2 = describe "After creating the user but not fetching" $ do
  it "There should be a user in Postgres" $ \(inPG, _, _) -> inPG `shouldBe` True
  it "There should be no user in Redis" $ \(_, inRedis, _) -> inRedis `shouldBe` False
```

现在我们需要添加 after 钩子，它将从数据库和缓存中删除用户。当然，我们预计用户不会存在于缓存中，但是我们包括了这一点，因为我们在最后的示例中需要它:

```
afterHook :: PGInfo -> RedisInfo -> (Bool, Bool, Int64) -> IO ()
afterHook pgInfo redisInfo (_, _, key) = do
  deleteUserCache redisInfo key
  deleteUserPG pgInfo key
```

最后，我们将再编写一个测试用例。这将模拟前一种情况，除了我们将在中间加入一个对`fetch`的调用。因此，我们希望用户同时在 Postgres 和 Redis 中:

```
beforeHook3 :: ClientEnv -> PGInfo -> RedisInfo -> IO (Bool, Bool, Int64)
beforeHook3 clientEnv pgInfo redisInfo = do
  userKeyEither <- runClientM (createUserClient testUser) clientEnv
  case userKeyEither of
    Left _ -> error "DB call failed on spec 3!"
    Right userKey -> do 
      _ <- runClientM (fetchUserClient userKey) clientEnv 
      inPG <- isJust <$> fetchUserPG pgInfo userKey
      inRedis <- isJust <$> fetchUserRedis redisInfo userKey
      return (inPG, inRedis, userKey)spec3 :: SpecWith (Bool, Bool, Int64)
spec3 = describe "After creating the user and fetching" $ do
  it "There should be a user in Postgres" $ \(inPG, _, _) -> inPG `shouldBe` True
  it "There should be a user in Redis" $ \(_, inRedis, _) -> inRedis `shouldBe` True
```

它将使用与案例 2 相同的 after hook，所以我们完成了！

# 连接并运行测试

最后一步是用`hspec`、`before`和`after`把我们所有的碎片粘在一起。下面是我们的主函数，一旦完成，它也会杀死运行服务器的线程:

```
main :: IO ()
main = do
  (pgInfo, redisInfo, clientEnv, tid) <- setupTests
  hspec $ before (beforeHook1 clientEnv pgInfo redisInfo) spec1
  hspec $ before (beforeHook2 clientEnv pgInfo redisInfo) $ after (afterHook pgInfo redisInfo) $ spec2
  hspec $ before (beforeHook3 clientEnv pgInfo redisInfo) $ after (afterHook pgInfo redisInfo) $ spec3
  killThread tid 
  return ()
```

现在我们的测试应该通过了！

```
After fetching on an empty database
  The fetch call should throw an error
  There should be no user in Postgres
  There should be no user in RedisFinished in 0.0410 seconds
3 examples, 0 failuresAfter creating the user but not fetching
  There should be a user in Postgres
  There should be no user in RedisFinished in 0.0585 seconds
2 examples, 0 failuresAfter creating the user and fetching
  There should be a user in Postgres
  There should be a user in RedisFinished in 0.0813 seconds
2 examples, 0 failures
```

# 使用 Docker

所以当我说“测试通过”时，它们现在在我的系统上工作。但是如果你克隆代码并试图运行它们，你将会失败。测试依赖于 Postgres 和 Redis，所以如果你不运行它们，它们就会失败！让你的测试依赖于这些外部服务是很烦人的。这是我们设计测试的弱点。它增加了任何人进入你的代码库的时间。新人必须弄清楚他们需要运行哪些东西，安装它们，等等。

那么我们如何解决这个问题呢？一个答案是通过使用 [Docker](https://www.docker.com/) 。Docker 允许您创建包含特定服务的容器。这使您不必担心在本地机器上设置服务的细节。更重要的是，您可以将 docker 映像部署到远程环境中。因此 develop 和 prod 将与您的本地系统相匹配。为了设置这个过程，我们将创建一个我们希望在 Docker 容器上运行的服务的描述。我们用一个`docker-compose`文件来做这件事。这是我们的样子:

```
version: '2'services:
  postgres:
    image: postgres:9.6
    container_name: prod-haskell-series-postgres
    ports:
      - "5432:5432" redis:
    image: redis:4.0
    container_name: prod-haskell-series-redis
    ports:
      - "6379:6379"
```

然后，您可以使用`docker-compose up`为您的 Docker 机器启动这些服务。当然，你必须安装并运行 Docker。但如果你有几种不同的服务，这是一个更容易的入门过程。更好的是,“compose”文件确保每个人都使用这些服务的相同版本。

即使这个容器在运行，测试仍然会失败！这是因为您还需要测试本身在您的 Docker 集群上运行。但是有了 Stack，这就简单了！我们将把以下标志添加到我们的`stack.yaml`文件中:

```
docker:
  enable: true
```

现在，无论你何时构建和测试你的程序，你都可以在 Docker 上完成。第一次这样做时，Docker 需要在容器上设置好一切。这意味着它将不得不下载栈和你使用的所有不同的软件包。所以第一次运行需要一段时间。但是随后的运行将是正常的。所有这些完成后，现在测试应该工作了！

# 结论

测试集成系统很难。我们可以尝试模仿外部服务的行为。但是这可能导致我们的程序的测试表示不忠实于生产系统。但是使用 Hspec 的`before`和`after`挂钩是确保所有外部事件首先发生的好方法。然后，您可以将这些结果传递给更简单的测试断言。

当需要运行您的系统时，如果您可以用一个命令调出所有的外部服务，那会很有帮助！Docker 允许您通过在`docker-compose`文件中列出不同的服务来做到这一点。然后，Stack 使得在 docker 容器上运行您的程序和测试变得容易，因此您可以使用这些服务！

栈是所有这些集成的关键。如果你以前从未使用过 Stack，你应该看看我们的[免费迷你课程](http://academy.mondaymorninghaskell.com/p/your-first-haskell-project)。它将教你使用 Stack 组织一个 Haskell 项目的所有基础知识。

如果这是你第一次接触 Haskell，我希望它有一些令人敬畏的可能性！看看我们的[入门清单](https://www.mmhaskell.com/checklist)，开始学习吧！