# 控制 Python 异步蔓延

> 原文：<https://medium.com/hackernoon/controlling-python-async-creep-ec0a0f4b79ba>

![](img/51eb74abd68e0f2db6ee60c7c0ea43ee.png)

Photo Credit: [Christian Jourdy](https://unsplash.com/collections/597524/growth?photo=aO_jMXTduUE) via [Unsplash](http://unsplash.com)

不久前，Python 在基础语言中加入了正式的异步性。玩[asyncio](https://docs.python.org/3/library/asyncio.html)和`coroutines`很有趣，它们是几乎并行执行的基本构造。但是当你开始更多地集成常规代码库时，你可能会发现事情会变得棘手。尤其是当您被迫与同步代码交互时。

当调用*一个合适的*函数时，情况变得复杂了。这样做需要一个`async`定义的代码块或协程。除了如果你的调用者必须是异步的，那么你也不能调用*它*，除非它的调用者是异步的。然后强制其调用者进入异步块，依此类推。这就是“异步蠕变”。

它可以迅速升级，进入代码的各个角落。如果代码库是异步的，这没什么大不了的，但是在混合使用时会阻碍开发。

下面是我用来解决这个问题的两个主要机制。两者都假设我们正在构建一个受益于异步执行的应用程序。

## 等待异步代码块

无论是构建异步应用程序还是增强线性应用程序，确定将从异步执行中获益最多的部分都很重要。这通常不难回答，但没有其他人能替你做到。一般的指导方针是从等待 I/O 的事情开始，比如文件或套接字访问、HTTP 请求等。

一旦您知道了哪些部分需要优化，就开始识别那些可以在彼此之上运行的部分。越多可以组合在一起越好。一个很好的例子是代码需要来自几个互不依赖的 REST APIs 的信息。您可以使用`aiohttp`并并行进行所有调用，而不是等待每个调用完成后再进行下一个调用。

现在的问题是将这些代码块加载到主事件循环中。有几种方法可以做到这一点，我喜欢将它们放入异步函数中，并使用`asyncio.ensure_future()`将它们放入循环中，使用`loop.run_until_complete()`等待完成:

```
import asyncio
import aiohttpasync def fetch(url):
    response = await aiohttp.request('GET', url)
    return await response.text()loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.gather(
    asyncio.ensure_future(fetch("http://www.google.com")),
    asyncio.ensure_future(fetch("http://www.github.com")),
    asyncio.ensure_future(fetch("http://www.reddit.com"))
))
```

这是一个类似于我在上一篇文章中使用的例子:[线程异步魔法以及如何使用它](https://hackernoon.com/threaded-asynchronous-magic-and-how-to-wield-it-bba9ed602c32)。

`asyncio.ensure_future()`将函数转换成协程，`asyncio.gather()`将它们组合在一起，而`loop.run_until_complete()`阻塞执行，直到所有调用完成。它的输出是每个调用的结果列表。

遵循到目前为止所讨论的要点将产生运行同步块的代码。但是其中一些块将一起执行几个异步函数。

## 用一根线

在我之前的[文章中也讨论过，创建一个独立的线程作为一个工作者并不难。它运行自己的事件循环，您使用线程安全的 asyncio 方法让它工作。好的方面是你可以让它与`call_soon()`同步工作，或者与`run_coroutine_threadsafe()`异步工作。](https://hackernoon.com/threaded-asynchronous-magic-and-how-to-wield-it-bba9ed602c32)

```
from threading import Thread...def start_background_loop(loop):
    asyncio.set_event_loop(loop)
    loop.run_forever()# Create a new loop
new_loop = asyncio.new_event_loop()# Assign the loop to another thread
t = Thread(target=start_background_loop, args=(new_loop,))
t.start()# Give it some async work
future = asyncio.run_coroutine_threadsafe(
    fetch("http://www.google.com"), 
    new_loop
)# Wait for the result
print(future.result())# Do it again but with a callback
asyncio.run_coroutine_threadsafe(
    fetch("http://www.github.com"),
    new_loop
).add_done_callback(lambda future: print(future.result()))
```

我们从`run_coroutine_threadsafe`得到一个`Future`，我们可以使用`result(timeout)`方法等待它，或者用`add_done_callback(function)`添加一个回调。回调函数将接收未来作为参数。

## 在相同的 API 方法中支持异步和同步调用

让我们来看看更复杂的东西。如果您有一个库或模块，其中大多数函数可以并行运行，但您只希望调用方是异步的，那该怎么办？

这里我们可以利用线程模型，因为调度器方法是同步的。这意味着用户不需要声明自己是异步的，也不需要在代码中处理异步爬行。异步块仍然包含在您的模块中。

它还允许 api 接口选择是否使用 asyncio。事实上，我们甚至可以更进一步，使用 inspect magic 自动检测何时异步。

没有线程，你就无法控制你的事件循环。用户可以做他们自己的异步摆弄，干扰你的方法如何执行。线程将至少保证在您操作的事件循环中异步执行。需要时可以开始和停止。这导致更可预测、可重复的结果。

让我们看一个建立在前面的例子上的例子。这里我们制作了一个包装器方法，它根据调用者调用适当的同步或异步函数。

```
import inspect
import requests...def is_async_caller():
    """Figure out who's calling.""" # Get the calling frame
    caller = inspect.currentframe().f_back.f_back # Pull the function name from FrameInfo
    func_name = inspect.getframeinfo(caller)[2] # Get the function object
    f = caller.f_locals.get(
        func_name, 
        caller.f_globals.get(func_name)
    ) # If there's any indication that the function object is a 
    # coroutine, return True. inspect.iscoroutinefunction() should
    # be all we need, the rest are here to illustrate. if any([inspect.iscoroutinefunction(f),
            inspect.isgeneratorfunction(f),
            inspect.iscoroutine(f), inspect.isawaitable(f),
            inspect.isasyncgenfunction(f) , inspect.isasyncgen(f)]):
        return True
    else:
        return Falsedef fetch(url):
    """GET the URL, do it asynchronously if the caller is async"""

    # Figure out which function is calling us
    if is_async_caller():
        print("Calling ASYNC method") # Run the async version of this method and
        # print the result with a callback
        asyncio.run_coroutine_threadsafe(
            _async_fetch(url), 
            new_loop
        ).add_done_callback(lambda f: print(f.result())) else:
        print("Calling BLOCKING method") # Run the synchronous version and print the result
        print(_sync_fetch(url)) def _sync_fetch(url):
    """Blocking GET""" return requests.get(url).content async def _async_fetch(url):
    """Async GET""" resp = await aiohttp.request('GET', url)
    return await resp.text() def call_sync_fetch():
    """Blocking fetch call""" fetch("[http://www.github.com](http://www.github.com)") async def call_async_fetch():
    """Asynchronous fetch call (no different from sync call 
       except this function is defined async)""" fetch("[http://www.github.com](http://www.github.com)") # Perform a blocking GET
call_sync_fetch()# Perform an async GET
loop = asyncio.get_event_loop()
loop.run_until_complete(call_async_fetch())
```

我们在`is_async_caller()`中使用`inspect`来获取调用我们的函数对象，并确定它是否是协程。虽然这很奇特，说明了可能性，但它可能不是很有性能。我们可以很容易地用`fetch`包装器中的`async_execute`参数替换该机制，并让用户决定。

`call_sync_fetch`和`call_async_fetch`函数展示了用户如何调用我们的包装器。如您所见，不需要等待 fetch 调用，因为它是通过在一个单独的线程中运行自动完成的。

这对于任何想要在支持遗留代码的同时增加异步执行支持的 python 包来说都是有用的。我敢肯定有利弊，请在下面的评论中开始讨论。

[![](img/4e728960fa1d1a8a3847021955abc3a4.png)](https://twitter.com/intent/follow?original_referer=https%3A%2F%2Fpublish.twitter.com%2F&ref_src=twsrc%5Etfw&region=follow_link&screen_name=tryexceptpass&tw_p=followbutton)

如果你喜欢这篇文章，并想了解我正在做的事情，请推荐它，访问[tryexecptpass.org](http://tryexceptpass.org)了解更多主题，并[在 Twitter 上关注我](https://twitter.com/intent/follow?original_referer=https%3A%2F%2Fpublish.twitter.com%2F&ref_src=twsrc%5Etfw&region=follow_link&screen_name=tryexceptpass&tw_p=followbutton)。