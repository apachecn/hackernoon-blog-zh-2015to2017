# 将您的首个 Docker 图片发布到 Docker Hub

> 原文：<https://medium.com/hackernoon/publish-your-docker-image-to-docker-hub-10b826793faf>

正如你所熟悉的 Docker 来自我之前的[帖子](/@rusrushal13/hello-docker-56e0bc8deede)。让我们深入探索更多。

现在你知道了如何运行一个容器和拉一个图像，现在我们也应该为其他人发布我们的图像。为什么你应该享受所有的乐趣；)

那么我们需要什么来发布我们的 Docker 图像呢？

*   一份文件
*   你的应用

对，就是这样。

## 为什么我们需要 Docker 方式的应用程序？

历史上，我们需要我们的应用程序(可能是 python 应用程序)，我们需要我们的机器上的 python(或所有依赖项)运行时环境。但这也造成了一种情况，你的机器上的环境必须如此，才能让你的应用程序按预期运行，也才能让你的服务器运行。有了 Docker，你什么都不需要(没有环境)。您可以直接获取一个可移植的 Python 运行时作为映像，不需要安装。然后，您的构建可以在您的应用程序代码旁边包含基本 Python 映像，确保您的应用程序、其依赖项和运行时一起运行。这些可移植图像是由一个叫做 Dockerfile 的东西定义的。

Dockerfile 充当容器内部的环境文件。它有助于为您的容器创建一个隔离的环境，哪些端口将暴露给外部世界，哪些文件您想要“复制”到该环境中。然而，在这样做之后，您可以预期在这个 docker 文件中定义的应用程序的构建无论在哪里运行都将完全一样。

所以让我们创建一个目录，做一个 ***Dockerfile*** 。

```
FROM python
WORKDIR /app
ADD . /app
RUN pip install -r requirements.txt
EXPOSE 80
ENV NAME world
CMD [“python”, “app.py”]
```

所以你有你的文件。您可以看到语法非常简单，一目了然。

现在我们需要我们的应用程序。我们来创建一个，一个 python 的 app)

***app.py***

```
from flask import Flask
import os
import socketapp = Flask(__name__)[@app](http://twitter.com/app).route(“/”)def hello():
     html = “<h3>Hello {name}!</h3>” \
            “<b>Hostname:</b> {hostname}<br/>”
     return html.format(name=os.getenv(“NAME”, “world”), hostname=socket.gethostname())if __name__ == “__main__”:
     app.run(host=’0.0.0.0', port=80)
```

***需求. txt***

```
Flask
```

现在，您已经具备了继续进行的所有条件。现在只需构建您的应用程序。

## 让我们建造它

ls 现在会给你看这个

```
$ ls
app.py requirements.txt Dockerfile
```

现在创建图像。

```
docker build -t imagebuildinginprocess .
```

你的形象在哪里？它在您的本地图像注册表中。

```
$ docker images
REPOSITORY               TAG     IMAGE ID       CREATED     SIZE
imagebuildinginprocess  latest  4728a04a9d39  14 minutes ago  694MB 
```

## 让我们也运行它

```
docker run -p 4000:80 imagebuildinginprocess
```

我们在这里做的是将端口 4000 映射到容器公开端口 80。您应该会在 [http://0.0.0.0:80](http://0.0.0.0:80) 看到 Python 正在为您的应用程序提供服务的通知。但是该消息来自容器内部，它不知道您将该容器的端口 80 映射到 4000，使得 URL[http://localhost:4000](http://localhost:4000)。在 web 浏览器中访问该 URL，查看网页上显示的内容，包括“Hello World”文本和容器 ID。

## 让我们分享一下:D

我们将把我们构建的映像推送到注册表，这样我们就可以在任何地方使用它。默认情况下，Docker CLI 使用 Docker 的公共注册表。

*   登录到本地机器上的 Docker 公共注册表。(如果您没有帐户，请在此处填写 cloud.docker.com)

```
docker login
```

*   标记图像:这更像是命名图像的版本。它是可选的，但是推荐使用，因为它有助于维护版本(就像 ubuntu:16.04 和 ubuntu:17.04 一样)

```
docker tag imagebuildinginprocess rusrushal13/get-started:part1
```

*   发布图像:将标记的图像上传到存储库中:一旦完成，此上传的结果将公开。如果您登录 Docker Hub，您将在那里看到新的图像及其 pull 命令。

```
docker push rusrushal13/get-started:part1
```

对，就这样，你完蛋了。现在你可以去码头中心，也可以查看一下；).你发布了你的第一张照片。

我发现这个 GitHub 存储库非常棒。看一看[https://github.com/jessfraz/dockerfiles](https://github.com/jessfraz/dockerfiles)

一定要给我改进的反馈；)