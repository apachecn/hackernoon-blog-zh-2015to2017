# 测试 PostgreSQL 的乐趣

> 原文：<https://medium.com/hackernoon/testing-postgresql-for-fun-af891047e5fc>

![](img/9d60ae5909716f6c6e1cb988f6060ce8.png)

Image copyright Lucina Lubin

# 当编程不好玩的时候

当迭代循环很长时，编程会变得令人沮丧。

假设您必须更改一个缺乏直接测试方法的底层系统。为了测试新代码，您必须手动执行一系列复杂、耗时且繁琐的步骤。当出现问题时，您所知道的要么是您的代码被破坏(由于某种未知的原因)，要么是您暴露了依赖于您的更改的巨大系统中的现有问题。更糟糕的是，代码不能以可重复的方式进行测试，并且每次运行都会得到不同的行为。

如果这还不够令人沮丧的话，想象一下您无法实现您设想的解决方案。您不能直接访问低级系统，因为一系列组件——旨在抽象、简化和创建低级系统的安全替代方案——挡道。这些组件很少实现它们的既定目标，但是会迫使你的最终设计比必要的更慢更复杂。

最后，您了解到您正在测试的开发环境在关键的、重要的方面与生产的行为不匹配。

这就是为许多网站编写[数据库](https://hackernoon.com/tagged/database)查询的感觉。一点都不好玩。让我们解决这个问题。

在这篇文章中，我将展示如何通过使用使测试变得容易的包来使数据库编程变得有趣。我将介绍一个用 PostgreSQL 构建持久队列的例子。

# 隔离的临时数据库

为了让数据库编程变得有趣，我们需要一种快速迭代的方法。我们需要打开数据库的实例，在我的例子中是 PostgreSQL，并直接测试我们正在编写的查询。

一个极好的选择是`[pg_tmp](http://ephemeralpg.org/)`。我将利用我编写的名为`[tmp-postgres](http://hackage.haskell.org/package/tmp-postgres)`的 Haskell 模拟，以及基于它的测试助手:`[hspec-pg-transact](http://hackage.haskell.org/package/hspec-pg-transact)`。

`[hspec-pg-transact](http://hackage.haskell.org/package/hspec-pg-transact)`提供了一个助手来设置一个测试套件，该套件创建一个临时的`postgres`流程。测试可以使用可选的`[itDB](https://hackage.haskell.org/package/hspec-pg-transact-0.1.0.0/docs/Test-Hspec-DB.html#v:itDB)`，它添加一个测试并运行用于测试的 db 事务。

下面是一个用临时数据库测试 db 查询的简单示例。

```
describeDB migrate "Query” $ 
  itDB "work" $ do
    [execute_](https://hackage.haskell.org/package/pg-transact-0.1.0.1/docs/Database-PostgreSQL-Transact.html#v:execute_) [[sql](https://hackage.haskell.org/package/postgresql-simple-0.5.0.0/candidate/docs/Database-PostgreSQL-Simple-SqlQQ.html)| 
      INSERT INTO things 
      VALUES (‘me’) |]
    [query_](https://hackage.haskell.org/package/pg-transact-0.1.0.1/docs/Database-PostgreSQL-Transact.html#v:query_) [sql| 
      SELECT name 
      FROM things |]
      `[shouldReturn](https://hackage.haskell.org/package/hspec-expectations-lifted-0.10.0/docs/Test-Hspec-Expectations-Lifted.html#v:shouldReturn)` [Only "me"]
```

(你需要确保`initdb`在`PATH`上。macOS 上的`brew`就是这种情况，但是对于 Ubuntu，你必须在`PATH`上加上`/usr/lib/postgresql/VERSION/bin`

完整的代码，包括导入语句，在这里是。让我们看一个更大的例子。

# 制作 postgresql 简单队列

在我之前的一篇文章中，我讨论了一个用于发送电子邮件的内存队列。正如凯尔·吉巴德指出的，排队的问题在于它不耐用。然而，PostgreSQL 是持久的，所以让我们像 Cale 那样使用它来创建队列。

我们将用`enqueueDB`添加新的有效载荷。`tryLockDB`会抓取最新的有效载荷给我们处理。一旦处理完成，我们将调用有效载荷上的`dequeueDB`:

```
data Payload = Payload
  { pId         :: PayloadId
  , pValue      :: Value
  , pState      :: State
  , pCreatedAt  :: UTCTime
  , pModifiedAt :: UTCTime
  } deriving (Show, Eq)enqueueDB :: Value -> DB PayloadId
enqueueDB value = do
  pid <- liftIO randomIO
  execute 
    [sql| INSERT INTO payloads 
          (id, value) VALUES (?, ?)
    |]
    (pid, value)
  return $ PayloadId pidtryLockDB :: DB (Maybe Payload)
tryLockDB = listToMaybe <$> query_
  [sql| UPDATE payloads
        SET state='locked'
        WHERE id in
          ( SELECT id
            FROM payloads
            WHERE state='enqueued'
            ORDER BY created_at ASC
            LIMIT 1
          )
        RETURNING id, value, state, 
                  created_at, 
                  modified_at
  |]dequeueDB :: PayloadId -> DB ()
dequeueDB payloadId = void $ execute
  [sql| UPDATE payloads
        SET state='dequeued'
        WHERE id=?
  |]
  payloadId
```

所有操作都发生在一个`[DB](http://hackage.haskell.org/package/pg-transact-0.1.0.0/docs/Database-PostgreSQL-Transact.html#t:DB)` monad 中，因此我们可以将它们与其他 db 操作组合在一起，并让它们在同一个事务中运行。例如，我们可以使用这个库来`enqueueDB`并在单个事务中创建一个客户。

# 回到测试

我们可以利用`[hspec-pg-transact](http://hackage.haskell.org/package/hspec-pg-transact)`来编写我们的测试:

```
main :: IO ()
main = hspec specspec :: Spec
spec = describeDB init "Queue" $ do
  itDB "empty gives nothing" $ do
    tryLockDB 
      `shouldReturn` Nothing itDB "enqueue/lock/dequeue" $ do
    theId <- enqueueDB $ String "!"
    Just Payload {..} <- tryLockDB pId `shouldBe` theId
    pValue `shouldBe` String "!"
    tryLockDB 
      `shouldReturn` Nothing dequeueDB pId `shouldReturn` ()
    tryLockDB 
      `shouldReturn` Nothing
```

我们可以加载我们的单个测试文件`[test/Database/QueueSpec.hs](https://github.com/jfischoff/postgresql-queue/blob/master/test/Database/PostgreSQL/Simple/QueueSpec.hs)`，并单独运行它:

```
> :l test/Database/QueueSpec.hs
> :main --match=locks
Database.Queue
postgresql:///test?host=/tmp/tmp-postgres26345&port=57473empty locks nothing
enqueue/lock/dequeue
Shutting DownFinished in 1.8647 seconds
2 examples, 0 failures
```

在单元测试的世界里，两秒钟并不算多，但是对于数据库测试来说，我们做得很好。不仅测试速度快，而且我们还可以通过重新加载和重新运行单个文件来快速迭代，所有这些都在个位数秒内完成。

这实际上很容易扩展。查询本身需要几毫秒；几乎所有的时间都用于临时数据库启动，我们只需要做一次。

# 压力测试

简单的测试很容易，但是让我们编写一个更复杂的压力测试。与这个简单的测试不同，压力测试将需要运行多个事务，所以我们不能使用`itDB`测试助手。

在这个测试中，我们创建了从队列中读取数据的`10`消费者线程和发送`enqueue`消息的`1000`线程:

```
it "stress queue" $ \testDB -> do
  let withPool' = withPool testDB
      elemCount = 1000 :: Int
      expected = [0 .. elemCount-1] ref <- newIORef [] -- Make 10 queue consumer threads
  loopThreads <- replicateM 10 
               $ async 
               $ fix $ \next -> do
    mpayload <- withPool' tryLock
    case mpayload of
      Nothing -> next
      Just Payload {..}  -> do
        last <- 
          atomicModifyIORef ref $ 
            \xs -> ( pValue : xs
                   , length xs + 1
                   )
        withPool' $ \conn -> 
          dequeue conn pId
        when (last < elemCount) 
             next -- Fork a 1000 threads and 
  -- enqueue an index
  forM_ [0 .. elementCount - 1] $ 
    \i -> forkIO $ void $ 
      withPool' $ flip enqueue $ 
        toJSON iwaitAnyCancel loopThreads
  Just decoded <- 
    mapM (decode . encode) 
      <$> readIORef ref
  sort decoded 
    `shouldBe` sort expected
```

这个测试是有效的，但是很明显我们的设计有问题。我们必须不断地轮询数据库，看看是否有新的数据。

# 获得全额奖学金

幸运的是 PostgreSQL 为我们提供了一个解决方案，正如 [lpsimth 指出的](https://www.reddit.com/r/haskell/comments/6fbqg3/assume_it_worked_and_fix_later_jonathan_fischoff/diiqy47/) : `[NOTIFY](https://www.postgresql.org/docs/current/static/sql-notify.html)`和`[LISTEN](https://www.postgresql.org/docs/current/static/sql-listen.html)`。我们创建了一个新函数`lock`，以利用 PostgreSQL 的 pubsub 特性:

```
notifyPayload :: Connection 
              -> IO ()
notifyPayload conn = do
  Notification {..} <- 
    getNotification conn
  unless (notificationChannel
         == "enqueue") $ 
         notifyPayload connlock :: Connection 
     -> IO Payload
lock conn = bracket_
  (Simple.execute_ 
     conn "LISTEN enqueue")
  (Simple.execute_ 
     conn "UNLISTEN enqueue")
  $ fix $ \continue -> do
      m <- tryLock conn
      case m of
        Nothing -> do
          notifyPayload conn
          continue
        Just x -> return x
```

我们将`enqueueDB`扩展到`NOTIFY`我们的`enqueue`通道，在它将一个有效载荷入队之后

```
enqueueDB :: Value -> DB PayloadId
enqueueDB value = do
  pid <- liftIO randomIO
  execute 
    [sql| INSERT INTO payloads 
          (id, value) VALUES (?, ?)
          NOTIFY enqueue;
    |]
    (pid, value)
  return $ PayloadId pid
```

我们可以为`lock`编写一个类似的测试，我们的循环变成:

```
loopThreads <- replicateM 10 $ 
  async $ fix $ \next -> do
    -- blocks waiting for 
    -- new payload
    x <- withPool testDB lock 
    lastCount <- 
      atomicModifyIORef ref $ 
        \xs -> ( pValue x : xs
               , length xs + 1
               )
    withPool testDB $ \conn -> 
      dequeue conn (pId x)
    when (last < elemCount) 
         next
```

好得多(忽略移动格式的超小边距:p)。最终队列在 [hackage](http://hackage.haskell.org/package/postgresql-simple-queue) 和 [github](https://github.com/jfischoff/postgresql-queue) 上打包为`postgresql-simple-queue`。

# 有趣是很难写的

在我把它推向黑客时代之前，我们经历了许多中间状态。我从简单的东西开始，写了压力测试，幸好发现了 bug，并添加了 pubsub。最不可思议的是，这很有趣，而且不令人沮丧。

不要相信我的话:试试看。`[pg-transact](https://hackage.haskell.org/package/pg-transact)`和`[hspec-pg-transact](https://hackage.haskell.org/package/hspec-pg-transact)`正在打包/堆叠。

# 额外积分:电子邮件队列需要 2 分钟

我们现在可以编写一个更好的电子邮件处理队列，在我的另一篇文章中讨论过。首先，我们需要一个处理循环:

```
forever $ do
  payload <- withResource 
    connectionPool lock 
  sendEmail payload
  withResource connectionPool $ 
    \conn -> dequeue conn 
                     (pId payload)
```

`postgres-simple-queue`的`[defaultMain](https://github.com/jfischoff/postgresql-queue/blob/master/src/Database/PostgreSQL/Simple/Queue/Main.hs#L133)`为我们提供了这个消费者循环，并且还进行命令行参数处理。我们可以很容易地使队列消费者可执行，就像这样:

```
main :: IO ()
main = do
  env <- newEnv Discover
  runResourceT $ runAWS env $ defaultMain "aws-email-queue-consumer" 
    $ \payload _ -> do
      case fromJSON $ pValue payload of
        Success email -> do
          resp <- AWS.send $ makeEmail email
          logFailedRequest resp
        Error x -> throwIO $ userError
                 $ "Failed to decode payload as an Email: " 
                 ++ show x
```

完整的例子见`[EmailQueue](https://github.com/jfischoff/postgresql-queue/blob/master/examples/EmailQueue.hs)`。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客们下午的开始。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)