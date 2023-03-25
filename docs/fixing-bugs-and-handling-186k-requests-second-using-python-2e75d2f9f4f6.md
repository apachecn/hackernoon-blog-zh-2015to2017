# 使用 Python 修复错误并处理每秒 186k 的请求

> 原文：<https://medium.com/hackernoon/fixing-bugs-and-handling-186k-requests-second-using-python-2e75d2f9f4f6>

[Sanic](https://github.com/channelcat/sanic) 是一个 [Python3](https://hackernoon.com/tagged/python3) [框架](https://hackernoon.com/tagged/framework)，使用稍微新引入的[协程](https://docs.python.org/3/library/asyncio-task.html)构建，利用 uvloop 并基于 Flask。但是，它有一个问题，使其无法正确利用多个流程。前面的代码试图天真地产生新的服务器，而没有显式地继承套接字连接:

```
processes = []
for _ in range(workers):
    process = Process(target=serve, kwargs=server_settings)
    process.start()
    ...
```

这导致只有一个进程实际处理请求。在 Guido Van Rossum 和 uvloop 的创建者 Yury Selivanov(相关链接[此处为](https://github.com/python/asyncio/issues/481))的帮助下，我能够通过在父进程中创建一个套接字并将其传递给子进程来实现一个解决方案:

```
sock = socket()
sock.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
sock.bind((server_settings['host'], server_settings['port']))
set_inheritable(sock.fileno(), True)
server_settings['sock'] = sock
server_settings['host'] = None
server_settings['port'] = None
processes = []
for _ in range(workers):
    process = Process(target=serve, kwargs=server_settings)
    process.daemon = True
    process.start()
    processes.append(process)
```

我在本地验证了这些更改对吞吐量的影响(我能够使用 4 个进程实现每秒双倍的请求数)，以及多个进程实际上响应了请求(通过用进程 id 进行响应)。最后，我提供了一个 20 核数字 Ocean droplet，并使用 20 个工作进程通过`wrk -t250 -c500 [http://localhost:8000](http://localhost:8000)`对服务器进行了基准测试:

```
from sanic.response import text
from sanic import Sanicapp = Sanic()@app.route('/')
async def hello(request):
    return text("OK")if __name__ == '__main__':
     app.run(workers=20)
```

并且能够在最大运行时间内达到每秒 186k 的请求。这将继续随着 CPU 数量的增加而增加。变更还没有被合并(编辑:变更已经被合并了，所以您现在可以从主分支进行合并)，尽管它可能很快就会被合并。同时，您应该能够通过克隆 sanic 存储库，使用`worker`分支，并将上面的文件保存到`hello.py`来演示它:

```
git clone [https://github.com/channelcat/sanic](https://github.com/channelcat/sanic)
cd sanic
git checkout workers
pip3 install -r requirements.txt
python3 hello.py
```

当然，你也需要一个 20 芯的滴管。我只跑了 6 分钟，每小时花费不到 1 美元。所以如果你对复制感兴趣，请告诉我进展如何！我本来打算在 AWS 上用 36 核来做这件事，但是显然我 2 年前的账户由于某种原因仍然“悬而未决”。无论如何，感谢阅读！此外，感谢尤里和 BDFL！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！