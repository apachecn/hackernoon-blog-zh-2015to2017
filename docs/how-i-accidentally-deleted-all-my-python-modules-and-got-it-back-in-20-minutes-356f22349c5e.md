# 我是如何不小心删除了我所有的 Python 模块，并在 20 分钟内恢复了它

> 原文：<https://medium.com/hackernoon/how-i-accidentally-deleted-all-my-python-modules-and-got-it-back-in-20-minutes-356f22349c5e>

今天我很粗心，这给我带来了一些麻烦。我正在开发一个 Flask web 应用程序，由于我的懒惰，没有使用 virtualenv。我只是想尽快在 Heroku 上建立一个运行的原型。为了将你的应用程序部署到 Heroku，你必须将 requirements.txt 放到应用程序的目录中。没有美德

```
pip freeze > requirements.txt
```

因为它将所有全局安装的模块放入其中。在谷歌中搜索解决方案 40 秒后，我找到了声称能帮我解决问题的 pip-tools。下载之后，我快速浏览了一下 README 的用法信息，但是那次我不仅仅是*粗心*，我也是*愚蠢*，因为它清楚地说明了这一点

> “现在您有了 requirements.txt，您可以使用 pip-sync 来更新您的虚拟 env，以准确反映其中的内容。注意:这将安装/升级/卸载与 requirements.txt 内容匹配的所有必要内容。

我注意力不够，跑了 pip-sync。由于缺乏 virtualenv，它将我的 requirements.txt(只有 3 个包)与我全球安装的 Python 模块同步。是的，*它卸载了一切*。我的终端里只有这个日志:

![](img/4ba830813a5f1bc0e820c50026e0337e.png)

我对自己的错误感到愤怒，但我需要一个解决方案。在谷歌搜索了一堆之后，我发现没有人会傻到不小心删除他们所有的 Python 模块，所以我不得不自己想出一些东西，以下是我所做的:

*   从我的终端复制日志，并将其作为多行字符串粘贴到 python 脚本中
*   使用 vim 搜索和替换从日志中删除所有冗余信息
*   拆分新创建的字符串(仅包含模块名)
*   在列表上写了一个 for 循环，并运行了下面一行:

```
subprocess.call(‘sudo pip install %s’ % package_name, shell True)
```

几乎成功了！唯一的问题是 pip 需要预先安装一些包，比如 *appdirs* 、*打包*和 *pyparsing* 。在从我的发行版的仓库中安装了这些之后，我的脚本就像预期的那样工作了，并且安装了所有的东西。

# 这个故事的寓意是:

*   **别傻了(一直用 virtualenv)**
*   如果你很笨，也许你可以用一个聪明的主意来弥补

> 黑客中午是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！