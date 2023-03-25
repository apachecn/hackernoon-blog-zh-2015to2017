# 让发展再伟大！

> 原文：<https://medium.com/hackernoon/make-development-great-again-faab769d264e>

## 这是一个 Python 故事，有一些 RESTful 和一堆 Redis

今天我想通过一个简单的，希望不会太无聊的例子来展示最新版本的 [*Mocket*](https://github.com/mindflayer/python-mocket) 。

![](img/491070ce932b1e4043d0bf3459a018ba.png)

Source: [http://www.dhgate.com/product/home-office-wall-socket-hidden-covert-camera/233012186.html](http://www.dhgate.com/product/home-office-wall-socket-hidden-covert-camera/233012186.html)

> 关于 *Mocket* 的简要介绍，请阅读[我之前的博文](/@mindflayer/mocket-is-alive-and-is-fighting-with-us-b2810d52597a)。

首先，我们需要一个新的`virtualenv`。

```
virtualenv -p python3 todos
source todos/bin/activate
cd todos
```

我们还需要一些包装。

```
pip install requests redis mocket ipython
```

额外要求:一个本地运行的 [*Redis*](http://redis.io/) 数据库。在一个现代的 [*Ubuntu*](https://hackernoon.com/tagged/ubuntu) 盒子上，安装它就像启动一个

```
sudo apt install redis-server redis-tools
```

您可以使用`redis-cli`来监控 *Redis* ，在 CLI 中键入`MONITOR`作为第一个命令。

现在是时候看看 Pythonic 的东西了。这是一个简单的故事，通过 JSON API 可以获得一些用户和他们发布的帖子。有了这些数据，我们想使用 IPython 和一堆 Redis 命令做一些统计，看看用户作为作者的表现如何。

这些是我们启动项目所需的代码行。

```
from collections import Counter
import requests
import redis# get all users
users = requests.get(
    '[http://jsonplaceholder.typicode.com/users'](http://jsonplaceholder.typicode.com/users')
).json()# users will be retrieved by userId
users_by_id = {u['id']: u for u in users}todos = requests.get(
    '[http://jsonplaceholder.typicode.com/todos'](http://jsonplaceholder.typicode.com/todos')
).json()# let's put some salt and pepper, skipping some todos
filtered_todos = [
    t for t in todos if 'et' in t['title']
]
```

在一个 *IPython* 会话中复制并粘贴它们，您应该能够得到如下结果:

```
In [9]: len(users_by_id)
Out[9]: 10In [10]: len(filtered_todos)
Out[10]: 62
```

我们需要一个*计数器*实例，来计算每个用户已经发布了多少帖子。

```
# use a counter for user todos
user_todos = Counter()
for t in filtered_todos:
    user_todos[t['userId']] += 1
```

同样，IPython 应该显示我们的*计数器*表示:

```
In [11]: user_todos
Out[11]: Counter({1: 6, 2: 4, 3: 7, 4: 5, 5: 8, 6: 8, 7: 7, 8: 7, 9: 5, 10: 5})
```

在我们的例子中，*用户* #5 和#6 是最有生产力的，因为他们各自发表了 8 篇文章。

是时候使用一些 Redis 命令了。

```
# it's going to connect to localhost on port 6379
r = redis.StrictRedis()# be careful when you flush your DB
# in case you need it, it's as simple as
# r.flushdb()for k, v in user_todos.items():
    r.zadd("todos", {users_by_id[k]["username"]: v})
```

我们刚刚填充了一个 Redis *排序集*，它有一个类似于前面的计数器，但是使用用户名作为键。

在 IPython 上，我们可以看到发生了什么:

```
In [12]: r.zrange('todos', 0, len(todos), desc=True, withscores=True)
Out[12]: 
[(b'Leopoldo_Corkery', 8.0),
 (b'Kamren', 8.0),
 (b'Samantha', 7.0),
 (b'Maxime_Nienow', 7.0),
 (b'Elwyn.Skiles', 7.0),
 (b'Bret', 6.0),
 (b'Moriah.Stanton', 5.0),
 (b'Karianne', 5.0),
 (b'Delphine', 5.0),
 (b'Antonette', 4.0)]
```

这是我们的用户，以及他们的用户名。你看我们在*柜台*发现的两张顶级海报。 [*模仿*](https://hackernoon.com/tagged/mocket) 的时间，及其新的*录制*功能。让我们在第一个脚本中添加几行代码，就在导入定义之后。

```
from collections import Counter
import requests
import redis
from mocket import MocketMocket.enable('example', '.')# get all users
users = requests.get(
    '[http://jsonplaceholder.typicode.com/users'](http://jsonplaceholder.typicode.com/users')
).json()[...]
```

使用 *Mocket* 的经典方式是定义一堆*条目*实例——也就是模仿一些资源。在这个例子中，我们的目标是让 *Mocket* 将实际套接字上运行的内容转储到一个名为`example.json`的文件中，该文件将在本地目录中创建，该目录由我们作为第二个参数输入的`.`指定。

这正是我们的`Mocket.enable('example', '.')`所做的。

再次运行我们的脚本。看里面有什么`example.json`，纯魔术。

现在反复运行这个脚本，您会看到它有多快，因为每个响应都直接来自我们的 JSON 转储。

> 它是 *HTTPretty* 、 *vcrpy* 等等。
> 
> 是 [*Mocket*](https://github.com/mindflayer/python-mocket) ，socket mock 框架。

[https://github.com/mindflayer/python-mocket](https://github.com/mindflayer/python-mocket)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)