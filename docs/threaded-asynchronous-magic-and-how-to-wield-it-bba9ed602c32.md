# 线程异步魔法及其使用方法

> 原文：<https://medium.com/hackernoon/threaded-asynchronous-magic-and-how-to-wield-it-bba9ed602c32>

![](img/469be7cbc4b75f46dda2b29334c5185f.png)

Photo Credit: [Daniel Schwen](https://en.wikipedia.org/wiki/File:CTA_loop_junction.jpg) via Wikipedia

## 深入 Python 的 asyncio 任务和事件循环

好吧，面对现实吧。时钟速度不再决定计算机处理器改进的速度。相反，我们看到晶体管密度增加，内核数量增加。翻译成软件术语，这意味着代码不会运行得更快，但更多的代码可以并行运行。

尽管充分利用我们新发现的硅资源需要改进软件，但许多编程语言已经通过添加有助于并行执行的功能开始走上这条道路。事实上，他们已经在那里等了好几年，等着我们去利用。

那我们为什么不呢？一个好的工程师总是耳听八方，倾听他所在行业的最新趋势，所以让我们看看 Python 正在为我们构建什么。

## 到目前为止我们有什么？

Python 通过线程库和多处理库实现了并行性。然而直到 3.4 分支才给了我们 *asyncio* 库来帮助单线程并发。这一增加是从版本 2 开始交换的更有说服力的最终推动力的关键。

asyncio 包允许我们定义协程。这些代码块能够将执行让给其他代码块。它们运行在一个*事件循环*中，该循环遍历调度的任务并逐个执行它们。当到达`await`语句或当前任务完成时，发生任务切换。

任务执行本身与单线程系统中的情况相同。意思是，这不是并行的实现，它实际上更接近于多线程。在代码块依赖于外部动作的情况下，我们可以察觉到并发性。

这种错觉是可能的，因为块可以在等待时产生执行，这使得任何依赖于外部 IO 的东西，如网络或磁盘存储，都是一个很好的候选对象。当 IO 完成时，协程接收到一个中断，可以继续执行。与此同时，其他任务也在执行。

asyncio 事件循环也可以用作任务调度程序。异步函数和阻塞函数都可以根据需要排队执行。

## 任务

一个`Task` 表示为在事件循环中异步执行而设计的可调用代码块。它们单线程执行，但可以通过不同线程上的循环并行运行。

用`async`关键字作为函数定义的前缀会把它变成一个异步协程。尽管任务本身在添加到循环中之前是不存在的。这在调用大多数循环方法时通常是隐式的，但是`asyncio.ensure_future(your_coroutine)`是更直接的机制。

为了表示可以产生执行的操作或指令，我们使用`await`关键字。尽管它只在协程块中可用，如果在其他地方使用会导致语法错误。

请注意，直到 Python 版才实现 async 关键字。所以当使用旧版本时，使用`@asyncio.coroutine`装饰器和`yield from`关键字。

## 行程安排

为了执行一个任务，我们需要一个对运行它的事件循环的引用。使用`loop = asyncio.get_event_loop()`给了我们执行线程中的当前循环。现在的问题是调用`loop.run_until_complete(*your_coroutine*)`或`loop.run_forever()`让它做一些工作。

让我们看一个简短的例子来说明几点。我强烈建议您打开一个解释器，然后跟着做:

```
import time
import asyncioasync def do_some_work(x):
    print("Waiting " + str(x))
    await asyncio.sleep(x)loop = asyncio.get_event_loop()
loop.run_until_complete(do_some_work(5))
```

这里我们将`do_some_work()`定义为一个协程，它等待外部工作负载的结果。通过`asyncio.sleep`模拟工作量。

运行代码可能会令人惊讶。你以为`run_until_complete`会是一个*阻塞*的呼叫吗？请记住，我们正在使用来自当前 **线程**的事件循环来执行任务。我们稍后将更详细地讨论替代方案。所以现在，重要的部分是理解当执行阻塞时，`await`关键字仍然支持并发。

为了更好地了解情况，让我们稍微改变一下测试代码，看看如何批量执行任务:

```
tasks = [asyncio.ensure_future(do_some_work(2)), 
         asyncio.ensure_future(do_some_work(5))]loop.run_until_complete(asyncio.gather(*tasks))
```

引入`asyncio.gather()`函数可以实现结果聚合。它等待同一个线程中的几个任务完成，并将结果放在一个列表中。

这里的主要观察是两个函数调用没有按顺序执行。它没有等待 2 秒，然后 5 秒，总共 7 秒。相反，它*开始*等待 2s，然后移动到下一个项目*开始*等待 5s，当较长的任务完成时返回，总共 5s。如果有助于可视化，可以随意向基本函数添加更多的打印语句。

这意味着我们可以将带有*代码的长时间运行的任务放在一个执行批处理中，然后让 Python 并行运行它们，直到它们全部完成。如果计划得当，这将比按顺序运行更快。*

可以把它看作是`threading`包的一种替代，在这个包中，在旋转了一些`Threads`之后，我们等待它们完成`.join()`。主要区别在于，与为每个函数创建一个新线程相比，开销更少。

当然，指出你的里程数可能因手头的任务而异总是好的。如果您正在做计算繁重的工作，几乎没有时间等待，那么您得到的唯一好处就是将代码分组到逻辑批中。

## 在不同的线程中运行循环

如果我们不是在当前线程中做所有的事情，而是产生一个单独的线程来为我们做这些事情，会怎么样呢？

```
from threading import Thread
import asynciodef start_loop(loop):
    asyncio.set_event_loop(loop)
    loop.run_forever()new_loop = asyncio.new_event_loop()
t = Thread(target=start_loop, args=(new_loop,))
t.start()
```

注意，这次我们通过`asyncio.new_event_loop()`创建了一个新的事件循环。想法是产生一个新的线程，给它传递新的循环，然后调用线程安全函数(稍后讨论)来调度工作。

这种方法的优点是由另一个事件循环执行的工作不会阻塞当前线程中的执行。从而允许主线程管理工作，并启用新类别的执行机制。

## 队列工作在不同的线程中

使用前面代码块中的线程和事件循环，我们可以用`call_soon()`、`call_later()`或`call_at()`方法轻松完成工作。它们能够在一个事件循环中运行常规的函数代码块(那些没有被定义为协程的代码块)。

然而，最好使用他们的`_threadsafe`替代品。让我们看看这是什么样子:

```
import timedef more_work(x):
  print("More work %s" % x)
  time.sleep(x)
  print("Finished more work %s" % x)new_loop.call_soon_threadsafe(more_work, 6)
new_loop.call_soon_threadsafe(more_work, 3)
```

现在我们正在谈话！执行这段代码不会阻塞主解释器，允许我们给它更多的工作。因为工作是按顺序执行的，所以我们现在实际上有了一个任务队列。

我们刚刚讨论了单线程代码的多线程执行，但是并发性不是我们从 asyncio 获得的一部分吗？当然是！工作线程上的那个循环仍然是异步的，所以让我们通过给它一个合适的协程来启用并行性。

这样做是使用`asyncio.run_coroutine_threadsafe()`的问题，如下所示:

```
new_loop.call_soon_threadsafe(more_work, 20)
asyncio.run_coroutine_threadsafe(do_some_work(5), new_loop)
asyncio.run_coroutine_threadsafe(do_some_work(10), new_loop)
```

这些指令说明了 python 是如何执行的。对`more_work`的第一个调用阻塞 20 秒，而对`do_some_work`的调用在`more_work`完成后立即并行执行。

## 真实世界示例#1 —发送通知

如今，一种常见的情况是由于任务或事件而发送通知。这通常很简单，但与电子邮件服务器对话以提交新邮件可能需要时间，制作电子邮件本身也是如此。

在很多情况下，我们没有闲工夫等待任务完成。这样做对最终用户没有好处。一个主要的例子是密码重置请求，或者触发存储库构建并通过电子邮件发送结果的 webhook 事件。

到目前为止，推荐的实践是在像`rabbitmq`这样的消息队列服务器之上使用像`celery`这样的任务队列系统来调度工作。我在这里告诉你，对于那些可以从你的主应用程序的另一个线程中轻松执行的小事情，只使用 asyncio 并不是一个坏主意。这种模式与我们到目前为止看到的代码示例非常相似:

```
import asyncio
import smtplib
from threading import Threaddef send_notification(email):
    """Generate and send the notification email""" # Do some work to get email body
    message = ...

    # Connect to the server
    server = smtplib.SMTP("smtp.gmail.com:587")
    server.ehlo()
    server.starttls()
    server.login(username, password) # Send the email
    server.sendmail(from_addr, email, message) server.quit()def start_email_worker(loop):
    """Switch to new event loop and run forever"""

    asyncio.set_event_loop(loop)
    loop.run_forever()# Create the new loop and worker thread
worker_loop = asyncio.new_event_loop()
worker = Thread(target=star_email_worker, args=(worker_loop,))# Start the thread
worker.start()# Assume a Flask restful interface endpoint
@app.route("/notify")
def notify(email):
    """Request notification email""" worker_loop.call_soon_threadsafe(send_notification, email)
```

这里我们假设一个 Flask web API，其端点安装在`/notify`上，请求某种通知邮件。

注意`send_notification`不是协程，所以每封邮件都是阻塞调用。工作线程的事件循环将作为跟踪传出电子邮件的队列。

你想知道为什么 SMTP 调用是同步的吗？嗯，虽然这是一个很好的例子，说明了什么样的 IO 应该是一个合适的 IO，但我目前还不知道异步 SMTP 库。如果你真的找到了一个，请随意用`async def`、`await`和`run_coroutine_threadsafe`来代替。

## 真实世界示例#2 —并行 Web 请求

下面是一个批处理并发运行到几个服务器的 HTTP 请求的例子，在处理之前等待响应。我希望它对那些做大量搜集工作的人有用，同时也是对`[aiohttp](http://aiohttp.readthedocs.io/en/stable/)`模块的一个快速介绍。

```
import asyncio
import aiohttpasync def fetch(url):
    """Perform an HTTP GET to the URL and print the response""" response = await aiohttp.request('GET', url)
    return await response.text()# Get a reference to the event loop
loop = asyncio.get_event_loop()# Create the batch of requests we wish to execute
requests = [asyncio.ensure_future(fetch("https://github.com")),
            asyncio.ensure_future(fetch("https://google.com"))]# Run the batch
responses = loop.run_until_complete(asyncio.gather(*requests))# Examine responses
for resp in responses:
  print(resp)
```

非常简单，就是将工作分组到一个任务列表中，并使用`run_until_complete`来获得响应。这可以很容易地更改为使用一个单独的线程来发出请求，通过前面描述的线程安全方法添加所有的 URL 会很简单。

我想指出的是，`[requests](http://docs.python-requests.org/en/v0.10.6/user/advanced/#asynchronous-requests)`库通过`gevent`提供了异步支持，但是我还没有弄清楚这是如何绑定到`asyncio`的。相比之下，我不知道流行的抓取框架`scrapy`的`asyncio`计划，但我假设他们正在努力。

## 停止循环

如果在任何时候你发现自己想要停止一个无限的事件循环，或者想要取消尚未完成的任务，我倾向于使用`KeyboardInterrupt`异常子句来触发取消，如下所示。虽然同样可以通过使用`signal`模块并为`signal.SIGINT`注册一个处理程序来完成。

```
try:
    loop.run_forever()except KeyboardInterrupt:
    # Canceling pending tasks and stopping the loop
    asyncio.gather(*asyncio.Task.all_tasks()).cancel() # Stopping the loop
    loop.stop() # Received Ctrl+C
    loop.close()
```

这一次，我们将介绍使用`Task.all_tasks()`来生成所有当前正在运行或计划的任务的列表。当与`gather()`结合时，我们可以向每个人发送`cancel()`命令，并让他们停止执行或从队列中删除。

请注意，由于 Windows 中信号不足，如果循环为空，键盘中断将永远不会被触发。这种情况的一种变通方法是将休眠几秒钟的任务排队。这保证了如果任务休眠时中断到达，循环会在唤醒时注意到。

异步编程可能非常令人困惑。我必须承认，我开始时有一些基本的假设，结果证明是错误的。直到我深入其中，我才意识到到底发生了什么。

我希望这能很好地介绍 asyncio 事件循环和任务，以及它们可能的用途。我知道还有很多其他的文章，但是我想做一些事情，把事情和一些真实世界的例子联系起来。如果您有任何问题或评论，请在下面留言，我将尽我所能提供帮助。

[![](img/4e728960fa1d1a8a3847021955abc3a4.png)](https://twitter.com/intent/follow?original_referer=https%3A%2F%2Fpublish.twitter.com%2F&ref_src=twsrc%5Etfw&region=follow_link&screen_name=tryexceptpass&tw_p=followbutton)

如果你喜欢这篇文章并想跟上我的工作，请推荐它，访问[tryexecptpass.org](http://tryexceptpass.org)获取更多话题，并在推特上[关注我。](https://twitter.com/intent/follow?original_referer=https%3A%2F%2Fpublish.twitter.com%2F&ref_src=twsrc%5Etfw&region=follow_link&screen_name=tryexceptpass&tw_p=followbutton)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客午间](http://bit.ly/Hackernoon)是黑客们下午的开始。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家族的一员。我们现在[接受提交](http://bit.ly/hackernoonsubmission)并很高兴[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)