# 摆脱 Docker 纯文本凭证

> 原文：<https://medium.com/hackernoon/getting-rid-of-docker-plain-text-credentials-88309e07640d>

您可能没有意识到，但是 [docker](https://hackernoon.com/tagged/docker) 引擎将您与`docker login`一起使用的凭证以纯文本形式存储在一个名为`$HOME/.docker/config.json`的文件中。好吧，当然，它不是 ASCII，它是 base64，乍一看可能像加密，**但它不是**。Base64 是一种编码方案，不是加密方案。您可以简单地复制 base64 字符串，并在几秒钟内将其转换为 ASCII。

## 天哪，我非常担心我的密码的保密性，我该怎么办？

很高兴你问了。谢天谢地，docker 提供了[凭证助手](https://github.com/docker/docker-credential-helpers)，它将使用你的操作系统的本地[凭证](https://hackernoon.com/tagged/credential)存储，而不是`config.json`。

**从 [Github](https://github.com/docker/docker-credential-helpers/releases) 和**下载包含您的操作系统助手的**档案，解压到**T3。不要忘记**使其可执行**。

如果你使用的是 Linux，这应该可以让你安装版本 0.4.2 的助手(你需要成为 root 用户):

```
wget [https://github.com/docker/docker-credential-helpers/releases/download/v0.4.2/docker-credential-secretservice-v0.4.2-amd64.tar.gz](https://github.com/docker/docker-credential-helpers/releases/download/v0.4.2/docker-credential-secretservice-v0.4.2-amd64.tar.gz) && tar -xf [docker-credential-secretservice-v0.4.2-amd64.tar.gz](https://github.com/docker/docker-credential-helpers/releases/download/v0.4.2/docker-credential-secretservice-v0.4.2-amd64.tar.gz) && chmod +x docker-credential-secretservice && mv docker-credential-secretservice /usr/local/bin/
```

一旦完成，**打开** `**~/.docker/config.json**` **并添加一个** `**credsStore**`键到 JSON 对象的根。**值将是凭证存储服务**的名称。这基本上是你刚刚安装的可执行文件的最后一个连字符后面的单词。对于 Linux，可执行文件名为`docker-credential-secretservice`，所以服务名为`secretservice`。

如果你使用的是 Linux 并且相信我的技能，你可以试着运行

```
sed -i '0,/{/s/{/{\n\t"credsStore": "secretservice",/' ~/.docker/config.json
```

从现在开始，docker 将使用外部服务来存储您的`docker login <url>`凭证。要删除文件中已经存在的内容，只需使用`docker logout <url>`。

**参考** [docker 上的官方文档登录。](https://docs.docker.com/engine/reference/commandline/login/#privileged-user-requirement)

> [黑客中午](http://bit.ly/Hackernoon)是黑客们下午的开始。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！