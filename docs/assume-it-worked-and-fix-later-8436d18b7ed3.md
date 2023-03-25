# 假设它工作了，以后再修复

> 原文：<https://medium.com/hackernoon/assume-it-worked-and-fix-later-8436d18b7ed3>

# 如何让你的应用更快更可靠

在帐户注册期间，web 服务器将发出 HTTP 请求来发送电子邮件。不仅同步请求很慢，而且如果远程主机没有响应，应用程序也会变得没有响应。

提高[性能](https://hackernoon.com/tagged/performance)的一个简单方法是使用像`[async](https://hackage.haskell.org/package/async)`这样的库，在进行其他计算的同时并发地发出请求。然而，如果您需要一个传出的[网络](https://hackernoon.com/tagged/network)请求的结果，如果远程主机关闭，您仍然会遇到可靠性问题。

通常，当处理程序发出请求时，从客户端的角度来看，不需要响应。注册电子邮件可以满足这个标准，但时间敏感的通知是一个更好的例子，因为它们通常是尽力而为的服务。

如果您的电子邮件服务中断，不管怎样，让注册成功都是有益的。通过将电子邮件请求的成功与帐户注册的成功分离，您可以提高应用程序的可靠性。这是标题的“假设它工作了”部分，您仍然需要持久化发送了哪些消息的记录和发送它们的定期作业，这是“以后修复它”部分。根据您的需求，可能根本没有“修复”阶段。

在理想情况下，您应该有一个像 Kafka 这样的持久队列服务，与您的服务器位于同一位置，具有低延迟或亚毫秒延迟。这个神奇的卡夫卡是比我将要提出的更好更简单的解决方案。然而，你可能不会发现自己处在这样一个幸运的环境中。

我将通过一个例子，使用亚马逊简单邮件服务和相应的`[amazonka](https://hackage.haskell.org/package/amazonka)`包、`[amazonka-ses](https://hackage.haskell.org/package/amazonka-ses)`，让邮件变得不阻塞。

# 同步基线

最简单的方法是打电话给 AWS SES inline 发送电子邮件。

```
post "/user" $ do
  input <- Scotty.body
  email <- maybe missingEmailError return
         $ input ^? key "email" . _String resp <- liftIO
        $ runResourceT
        $ runAWS env
        $ AWS.send
        $ makeEmail email logFailedRequest resp -- Imagine there is code here for 
  -- inserting a user into the database json $ object ["id" .= email]
```

# 尝试 1:分叉线程

实现非阻塞异步行为的一个简单方法是在每次需要发送电子邮件时分叉一个线程。

```
liftIO $ forkIO $ handle logExcept $ do
  resp <- liftIO
        $ runResourceT
        $ runAWS env
        $ AWS.send
        $ makeEmail email logFailedRequest resp
```

如果 AWS 变慢，或者超时，我的线程就会排队。线程将开始消耗资源，如果情况变得足够糟糕，我的应用程序可能会变得无响应和崩溃。

分叉另一个线程已经解决了典型案例中的性能问题，但是如果 AWS SES 下降，我增加了**系统风险。一个关闭的电子邮件服务现在可以导致我的整个应用程序崩溃。以前，只有账户创建请求会失败。**

# 解决方案 1:添加超时线程

为了限制可以累积的线程数量，我们可以添加一个超时:

```
liftIO $ forkIO $ handle logExcept $ 
  logTimeout <=< timeout (60 * 1000000) $ do
    resp <- liftIO
          $ runResourceT
          $ runAWS env
          $ AWS.send
          $ makeEmail email
    logFailedRequest resp
```

只要注册率低于我们的最大并发请求数，有问题的电子邮件服务就不会关闭我们的网站。

不利的一面是，我们是否阻止了灾难性的失败还不太清楚。首先，我们需要估计并发注册的最大数量。如果我们的注册率是每分钟 10，000 人，而电子邮件服务正在下降，我们可能会有麻烦……但即使电子邮件服务在上升，我们也可能会有麻烦。报名的人很多。此外，我们选择了一分钟的任意时间作为超时时间。这可能是一个太小的值，我们正在超时的潜在成功的电子邮件请求。

我们也没有任何方法来限制同时请求的并发性，或者确保所有的线程在我们关闭之前已经完成。这种变化可能是一个解决方案，但它留下了改进的空间。

# 解决方案 2:有界队列

我们需要一种快速排队通知请求的方法，而不是为每个请求分配一个线程。队列应该是有界的，有非阻塞写(我们将只记录失败)和阻塞读，所以`TBMQueue`就足够了。

首先，我们在服务器启动期间创建队列和工作线程:

```
worker :: Env -> TBMQueue SendEmail -> IO ()
worker env queue = do
  -- Make a loop enclosing the thread, env, and queue vars.
  let go = do
        -- Block waiting for a new email to send
        mpayload <- liftIO $ atomically $ readTBMQueue queue
        case mpayload of
         -- Nothing means the queue is closed and empty.
         -- Stop the loop ending the thread
          Nothing -> return ()
          Just payload -> do
            resp <- AWS.send payload
            logFailedRequest resp -- Start the loop again
            go handle logExcept $ runResourceT $ runAWS env gomain = do 
  env      <- newEnv Discover  
  queue    <- newTBMQueueIO 100000
  threadId <- forkIO $ worker env queue
  scotty ...
```

我们为排队编写了一个简单的助手函数:

```
enqueueEmail :: TBMQueue SendEmail -> Text -> IO ()
enqueueEmail queue email = do
  msuccess <- atomically
            $ tryWriteTBMQueue queue
            $ makeEmail email case msuccess of 
    Nothing -> putStrLn "Wat!! The email queue is closed?"
    Just success -> unless success 
                  $ putStrLn "Failed to enqueue email!"
```

然后我们可以在处理程序中使用`queue`:

```
post "/user" $ do
  input <- Scotty.body
  email <- maybe missingEmailError return
         $ input ^? key "email" . _String liftIO $ enqueueEmail queue email
  json $ object ["id" .= email]
```

我们结束了。无论电子邮件服务的速度有多慢，我们的应用程序最多只会使用我们有限队列中的内存(很小),并且只使用工作线程所需的资源。我们最坏的情况是，我们将无法发送一些电子邮件，但我们的应用程序将保持稳定，性能将是好的。

# 让它成真

好吧，我们还没完。我们必须在关机时优雅地处理排队。我们还必须在异常后重启线程。

为了帮助我们进行关机和重启，我们将使用一个名为`immortal`的库。`immortal`提供在异常后重启的线程，我们可以等待它们的完成。它还使用适当的异常屏蔽卫生来在新产生的线程上设置异常处理程序，我在上面的示例中省略了这一点(但是在[示例项目](https://github.com/jfischoff/asynchronous-email-example)中更好，对于这些示例来说也没什么关系)。

我们的新员工功能如下所示:

```
worker :: Thread -> Env -> TBMQueue SendEmail -> IO ()
worker thread env queue = do
  -- Make a loop enclosing the thread, env, and queue vars.
  let go :: AWS ()
      go = do
        -- Block waiting for a new email to send
        mpayload <- liftIO $ atomically $ readTBMQueue queue
        case mpayload of
         -- Nothing means the queue is closed and empty.
         -- Stop the loop and kill the thread.
          Nothing -> liftIO $ stop thread
          Just payload -> do
            resp <- AWS.send payload
            logFailedRequest resp -- Start the loop again
            gohandle logExcept $ runResourceT $ runAWS env go
```

唯一改变的是，我们现在接收一个`Thread`并在队列为空时停止`Thread`，并用:

```
Nothing -> liftIO $ stop thread
```

为了在我们的`main`函数中创建线程，我们编写:

```
thread <- create $ \thread -> worker thread env queue
```

就在`main`结束之前，我们添加:

```
atomically $ closeTBMQueue queue
wait thread
```

这将关闭`queue`并防止程序退出，直到`queue`被排空。我将此扩展到了[示例项目](https://github.com/jfischoff/asynchronous-email-example)中的多个工人。

# 进一步的考虑

在我们的简单例子中，我们仅仅是记录问题，但是一个真实的系统可能想要填充丢失的电子邮件；拥有一种存储哪些客户已经收到电子邮件的方法可能会有所帮助。

此外，应该扩展循环，以发出对监控有用的事件，比如将队列大小发送到度量服务器。我将在以后的博客文章中介绍这一点，作为奖励，将包括一个测试命令式代码的技巧。

# 结论

这里的步骤越来越复杂，开发人员的工作量也越来越大。编写最终版本并不需要花费太多时间，但是可以根据您的需求和经验进行扩展(我会跳过尝试一…只需添加超时)；只要确保你明白权衡。最终拥有一个像 Kafka 一样持久的队列可能是最好的，但是有选择总是好的。

“稍后修复”部分将需要轮询数据库中未发送的电子邮件。我还不知道怎么做。通过使用内存中的队列，可以减少及时发送电子邮件所需的轮询时间。此外，在除注册电子邮件之外的其他情况下，例如对时间敏感的实时通知，您可能**而**根本不需要回填丢失的通知。很容易使用，所以我选择它作为例子。

如果你想试试上面的例子，看看这个展示 web 服务器项目，它突出了这些例子:[https://github.com/jfischoff/asynchronous-email-example](https://github.com/jfischoff/asynchronous-email-example)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！