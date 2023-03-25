# 现在支持 asyncio 了！

> 原文：<https://medium.com/hackernoon/httpretty-now-supports-asyncio-e310814704c6>

## 和 Python 3，因此…

你自己试试！

```
class AioHttpEntryTestCase(TestCase):
    [@httprettified](http://twitter.com/httprettified)
    def test_https_session(self):
        url = '[https://httpbin.org/ip'](http://httpbin.org/ip')
        HTTPretty.register_uri(
            HTTPretty.GET,
            url,
            body=json.dumps(dict(origin='127.0.0.1')),
        ) async def main(l):
            async with aiohttp.ClientSession(loop=l) as session:
                with async_timeout.timeout(3):
                    async with session.get(url) as get_response:
                        assert get_response.status == 200
                        assert await get_response.text() == '{"origin": "127.0.0.1"}' loop = asyncio.get_event_loop()
        loop.set_debug(True)
        loop.run_until_complete(main(loop))
```

嗯，我忘了进口封锁。

```
import jsonimport aiohttp
import asyncio
import async_timeout
from unittest import TestCasefrom mocket.plugins.httpretty import HTTPretty, httprettified
```

等等！ [Mocket](https://hackernoon.com/tagged/mocket) ？！那是什么鬼东西？我们本来是要谈论[的！](https://hackernoon.com/tagged/httpretty)

是的，你说得对，我们或多或少在谈论它，或者更好的是，我们在谈论一个诞生于 2013 年的项目，最初的灵感来自于 HTTPretty。

然后，在 4 年的时间里*桥下已经流了很多水，*在很长一段时间里，我们几乎忘记了它，Mocket 一直在增长，即使很长时间以来我都是唯一维护它的开发者。

所以，回到这个例子，这将是做同样事情的模拟方法:

```
import jsonimport aiohttp
import asyncio
import async_timeout
from unittest import TestCasefrom mocket.mocket import mocketize
from mocket.mockhttp import Entryclass AioHttpEntryTestCase(TestCase):
    [@mocketize](http://twitter.com/mocketize)
    def test_https_session(self):
        url = '[https://httpbin.org/ip'](http://httpbin.org/ip')
        Entry.single_register(
            Entry.GET,
            url,
            body=json.dumps(dict(origin='127.0.0.1')),
        ) async def main(l):
            async with aiohttp.ClientSession(loop=l) as session:
                with async_timeout.timeout(3):
                    async with session.get(url) as get_response:
                        assert get_response.status == 200
                        assert await get_response.text() == '{"origin": "127.0.0.1"}' loop = asyncio.get_event_loop()
        loop.set_debug(True)
        loop.run_until_complete(main(loop))
```

所以，如果你喜欢，我**鼓励**你做以下一件或多件事:

*   使用 Mocket
*   贡献给 Mocket
*   ★https://github.com/mindflayer/python-mocket/[的 Mocket](https://github.com/mindflayer/python-mocket/)；
*   👍我在[https://github.com/vinta/awesome-python/pull/956](https://github.com/vinta/awesome-python/pull/956)的公关；

![](img/bb8c0eae0b3a937d65f0e74375069a52.png)

Much water has flowed under the bridge

> Mocket:一个套接字模拟框架
> 
> 对于所有种类的插座动物，包括网络客户端