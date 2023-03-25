# 使用 Python 同步监视的状态 Plex 服务器

> 原文：<https://medium.com/hackernoon/syncing-plex-servers-with-python-b9454fe81b00>

大约 8 个月前，我安装了一台 [Plex](https://www.plex.tv/) 服务器来满足我的电视瘾。我的一个朋友也这样做了，我们共享我们的服务器。我们在电视上有非常相似的品味，当我需要定位时，我不会太在意我连接到哪个服务器。

这带来了一个问题，观看状态在服务器之间不同步，所以当我在服务器之间来回切换时，我必须记住我在节目中的位置。幸运的是， [Plex](https://hackernoon.com/tagged/plex) 有一个 API，所以让我们编写一个脚本来自动同步它们。

更好的是，有人为 API 编写了一个 [python](https://hackernoon.com/tagged/python) 包装器:[https://github.com/pkkid/python-plexapi](https://github.com/pkkid/python-plexapi)

在开始之前，我做了一些假设:

*   被监视的状态胜过未被监视的状态。如果我在其中一个上看过一集，它应该会显示为在两个上看过
*   我们可能不会有彼此相同的情节。

这是一个比较简单的问题。似乎应该只有几个步骤:

1.  连接到两台服务器
2.  找出两台服务器的所有共同点
3.  遍历列表并获取每个节目的剧集
4.  对于每一集，如果它在任何一台服务器上看过，就标记它在另一台服务器上看过

既然我们已经有了想做什么的想法，那就让我们在 REPL 尝试一下吧。GitHub 自述文件中的文档向我们展示了如何做几乎所有我们想要做的事情，因此我们将遵循它。

```
$ mkvirtualenv -p /usr/bin/python3 plex_sync
$ pip install plexapi
$ pythonPython 3.5.2 (default, Nov 17 2016, 17:05:23) 
[GCC 5.4.0 20160609] on linux
Type “help”, “copyright”, “credits” or “license” for more information.
>>> from plexapi.myplex import MyPlexAccount
>>> username = ‘username’
>>> password = ‘password’
>>> account = MyPlexAccount(username, password)
Traceback (most recent call last):
 File “<stdin>”, line 1, in <module>
 File “/home/nolan/.virtualenvs/plex/lib/python3.5/site-packages/plexapi/myplex.py”, line 20, in __init__
 self.authenticationToken = data.attrib.get(‘authenticationToken’)
AttributeError: ‘str’ object has no attribute ‘attrib’
>>>
```

嗯。那可能不应该发生。看起来问题来自于`plexapi.myplex`让我们深入了解一下这里发生了什么。

```
git clone [git@github.com](mailto:git@github.com):pkkid/python-plexapi.git
```

根据异常，我们在 myplex.py 中查找第 20 行

```
$ find -name “myplex.py” | xargs cat — | head -25 | tail -10lass MyPlexAccount(PlexObject):
 “”” MyPlex account and profile information. The easiest way to build
 this object is by calling the staticmethod :func:`~plexapi.myplex.MyPlexAccount.signin`
 with your username and password. This object represents the data found Account on
 the myplex.tv servers at the url [https://plex.tv/users/account](https://plex.tv/users/account).Parameters:
 username (str): Your MyPlex username.
 password (str): Your MyPlex password.
 session (requests.Session, optional): Use your own session object if you want to
```

这是个问题。`MyPlexAccount`的构造函数并不像异常中所说的那样在第 20 行。PyPi 上的代码肯定不一样。它看起来像什么？

```
$ pip download plexapi
$ tar -xvzf PlexAPI-2.0.2.tar.gz
$ find -name “myplex.py” | xargs cat — | head -25 | tail -10
 BASEURL = ‘[https://plex.tv/users/account'](https://plex.tv/users/account')
 SIGNIN = ‘[https://my.plexapp.com/users/sign_in.xml'](https://my.plexapp.com/users/sign_in.xml')

 def __init__(self, data, initpath=None):
     self.authenticationToken = data.attrib.get(‘authenticationToken’)
     self.certificateVersion = data.attrib.get(‘certificateVersion’)
     self.cloudSyncDevice = data.attrib.get(‘cloudSyncDevice’)
     self.email = data.attrib.get(‘email’)
     self.guest = utils.cast(bool, data.attrib.get(‘guest’))
     self.home = utils.cast(bool, data.attrib.get(‘home’))
```

好吧，这样好一点了。构造函数在它应该在的地方，但是它不需要用户名和密码。这是怎么回事？

花费的时间比我想承认的要长，但最终我意识到 PyPi 上的文档与 GitHub 上的不同。我需要使用`MyPlexAccount.signin()`而不是`MyPlexAccount`构造函数。第二次尝试:

```
$ pythonPython 3.5.2 (default, Nov 17 2016, 17:05:23) 
[GCC 5.4.0 20160609] on linux
Type “help”, “copyright”, “credits” or “license” for more information.
>>> from plexapi.myplex import MyPlexAccount
>>> username = ‘username’
>>> password = ‘password’
>>> account = MyPlexAccount.signin(username, password)
>>>
```

进步！接下来，我们需要创建到两台服务器的连接。

```
>>> server_1_name = ‘server_1_name’
>>> server_2_name = ‘server_2_name’
>>> server_1 = account.resource(server_1_name)
>>> server_2 = account.resource(server_2_name)
>>> conn_1 = server_1.connect()
>>> conn_2 = server_2.connect()
Traceback (most recent call last):
 File “<stdin>”, line 1, in <module>
 File “/home/nolan/.virtualenvs/plex/lib/python3.5/site-packages/plexapi/myplex.py”, line 157, in connect
 raise NotFound(‘Unable to connect to resource: %s’ % self.name)
plexapi.exceptions.NotFound: Unable to connect to resource: server_2_name
```

啊哦。这是否意味着我不能通过 API 连接到不属于我的服务器？这说不通。我可以看到一堆在我朋友的服务器的网络界面上看不到的信息。

```
>>> dir(server_2)
[‘BASEURL’, ‘__class__’, ‘__delattr__’, ‘__dict__’, ‘__dir__’, ‘__doc__’, ‘__eq__’, ‘__format__’, ‘__ge__’, ‘__getattribute__’, ‘__gt__’, ‘__hash__’, ‘__init__’, ‘__le__’, ‘__lt__’, ‘__module__’, ‘__ne__’, ‘__new__’, ‘__reduce__’, ‘__reduce_ex__’, ‘__repr__’, ‘__setattr__’, ‘__sizeof__’, ‘__str__’, ‘__subclasshook__’, ‘__weakref__’, ‘_connect’, ‘accessToken’, ‘clientIdentifier’, ‘connect’, ‘connections’, ‘createdAt’, ‘device’, ‘home’, ‘lastSeenAt’, ‘name’, ‘owned’, ‘platform’, ‘platformVersion’, ‘presence’, ‘product’, ‘productVersion’, ‘provides’, ‘synced’]
```

如果不允许我连接到它，我可能不会得到像`accessToken`或`platform`这样的东西。为什么它会显示为我的帐户下的资源？让我们再次深入研究一下 plexapi 代码。

我能够很好地创建一个`MyPlexResource`对象(`server_2`)，异常来自`connect`函数，所以让我们从这里开始。`connect`的代码如下所示:

Copyright Michael Shepanski

嗯，那件事看起来是个问题。从注释来看，API 似乎只检查非本地连接中不属于我们的资源。但这似乎不是代码所做的。它过滤掉不属于本地的连接。那是行不通的。我想连接到我朋友的服务器，但它不是我自己的，也不是本地的。我敢打赌，如果我们把第 6 行和第 7 行中的`if forcelocal(c)`去掉，我们就可以连接到`server_2`。移除`if forcelocal(c)`后:

```
$ pythonPython 3.5.2 (default, Nov 17 2016, 17:05:23) 
[GCC 5.4.0 20160609] on linux
Type “help”, “copyright”, “credits” or “license” for more information.
>>> from plexapi.myplex import MyPlexAccount
>>> username = ‘username’
>>> password = ‘password’
>>> account = MyPlexAccount.signin(username, password)
>>> server_1_name = ‘server_1_name’
>>> server_2_name = ‘server_2_name’
>>> server_1 = account.resource(server_1_name)
>>> server_2 = account.resource(server_2_name)
>>> conn_1 = server_1.connect()
>>> conn_2 = server_2.connect()
>>>
```

啊哈！那似乎已经解决了问题。让我们通过找到他有但我没有的节目来确保它实际上正确地连接到我们想要的服务器。依靠(这次是正确的)文档:

```
>>> conn_2.library.section(‘TV Shows’).get(‘Police Squad!’).episodes()
[<Episode:13132:b’A.Substantial.Gift.(‘>, <Episode:13133:b’Ring.of.Fear.(A.Dang’>, <Episode:13134:b’The.Butler.Did.It.(A’>, <Episode:13135:b’Revenge.and.Remorse.’>, <Episode:13136:b’Rendezvous.at.Big.Gu’>, <Episode:13137:b’Testimony.of.Evil.(D’>]
```

看起来很有效。现在，我们必须执行步骤 2 到 4。希望我们现在已经过了棘手的阶段。文档没有指定如何列出库中的所有节目，但我怀疑搜索什么都会返回所有内容。

```
>>> len(conn_2.library.section(‘TV Shows’).search())
58
```

那起了作用。找到两个服务器之间所有共同节目的最简单方法是使用集合，并在每个服务器上找到节目集合的交集。

```
>>> server_1_shows = set(list(map((lambda x: x.title), conn_1.library.section(‘TV Shows’).search())))
>>> server_2_shows = set(list(map((lambda x: x.title), conn_2.library.section(‘TV Shows’).search())))
>>> common_shows = server_1_shows & server_2_shows 
>>> 
```

好了，我们得到了两台服务器共有的所有节目的名字。接下来的两个步骤是遍历这个列表，并同步每个剧集的观看状态。这不是很简单，因为每一集都在一个列表中，我不知道顺序是否有保证。也有可能一集只存在于其中一个服务器上。这意味着我们只能用线性搜索来匹配剧集。

这很有效，所以让我们把它从 REPL 中取出来，放在一个 python 文件中，这样我们就可以做一些调整了。

我们成功地做了我们计划要做的事情，但进展相当缓慢。怎么才能加快速度呢？对`markWatched`的调用需要一个网络调用，我们调用它的次数比我们需要的要多。如果一集已经在两个服务器上被标记为已观看，我们仍然调用`markWatched`。当我们将来运行这个工具时，将会有很多不必要的网络调用。让我们快速改变一下。

那应该差不多了。剩下要做的就是提交我对 plexapi 所做更改的 pull 请求。在那之前，我已经分叉了库，并在我的 GitHub 上应用了补丁。最终剧本:

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！