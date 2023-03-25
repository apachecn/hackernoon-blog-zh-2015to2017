# 调试 Docker 容器中运行的远程 Node.js 应用程序

> 原文：<https://medium.com/hackernoon/debugging-remote-node-js-application-running-in-a-docker-container-b7c8ff377d5d>

假设您想要调试一个已经在远程机器上运行的 [Node.js](https://hackernoon.com/tagged/nodejs) 应用程序，该应用程序位于 [Docker](https://hackernoon.com/tagged/docker) 容器中。并且希望在不修改命令参数(启用`debug`模式)和向全世界开放 remote Node.js 调试器代理端口的情况下实现它。

> 我打赌你不知道这是可能的，也不知道如何去做。

如果你渴望学习一些新的很酷的东西，我鼓励你继续阅读这篇文章。

# TdodoMVC 演示应用程序

我将使用 **TodoMVC** Node.js 应用程序(由 Gleb Bahmutov 开发)的 [fork](https://github.com/alexei-led/todomvc-express) 作为这篇博文的演示应用程序。随意克隆和使用这个库。

这里是我为 TodoMVC 应用程序添加的`Dockerfile`。它允许在 Docker 容器中运行 TodoMVC 应用程序。

```
FROM alpine:3.5 # install node 
RUN apk add --no-cache nodejs-current tini 
RUN mkdir -p /usr/src/app 
WORKDIR /usr/src/app # Build time argument to set NODE_ENV (‘production’’ by default) 
ARG NODE_ENV 
ENV NODE_ENV ${NODE_ENV:-production} # install npm packages: clean obsolete files 
COPY package.json /usr/src/app/ 
RUN npm config set depth 0 && \ 
    npm install && \ 
    npm cache clean && \ 
    rm -rf /tmp/* # copy source files 
COPY . /usr/src/app 
EXPOSE 3000 # Set tini as entrypoint 
ENTRYPOINT [“/sbin/tini”, “--“] CMD [ “npm”, “start” ] # add VCS labels for code sync and nice reports 
ARG VCS_REF=”local” 
LABEL org.label-schema.vcs-ref=$VCS_REF \ 
      org.label-schema.vcs-url="https://github.com/alexei-led/todomvc-express.git"
```

## 在 Docker 容器中构建和运行 TodoMVC:

要为 TodoMVC 应用程序构建一个新的 Docker 映像，运行`docker build`命令。

```
$ # build Docker image; set VCS_REF to current HEAD commit (short) 
$ docker build -t local/todomvc --build-arg VCS_REF=`git rev-parse ---short HEAD` . 
$ # run TodoMVC in a Docker container 
$ docker run -d -p 3000:3000 --name todomvc local/todomvc node src/start.js
```

# 这个计划

**最终目标** —我希望能够将 Node.js 调试器附加到已经在 Docker 容器中启动并运行的 Node.js 应用程序，该应用程序运行在 AWS 云中的远程主机上，而无需修改应用程序、容器、容器配置，或者使用额外的`debug`标志重新启动它。假设应用程序正在运行，现在出现了一些问题——我想用调试器连接到它，并开始查看问题。

所以，我需要一个计划——一个帮助我实现最终目标的循序渐进的流程。

*先来探究一下库存。*

在服务器(AWS EC2 VM)上，我有一个 Node.js 应用程序运行在 Docker 容器中。在客户端(我的笔记本电脑)，我有一个 IDE(在我的例子中是 Visual Studio 代码)、Node.js 应用程序代码(`git pull/clone`)和一个 Node.js 调试器。

所以，这是我的计划:

1.  将已经运行的应用程序设置为`debug`模式
2.  公开一个新的 Node.js 调试器代理端口，以安全的方式启用远程调试
3.  同步客户机-服务器代码:两者应该在一个`git`树中进行相同的提交
4.  将本地 Node.js 调试器连接到远程服务器上的 Node.js 调试器代理端口，并以安全的方式进行连接
5.  如果一切正常，我应该能够执行常规的调试任务，比如设置断点、检查变量、暂停执行等。

# 步骤 1:将已经运行的 Node.js 应用程序设置为`debug`模式

> *V8 调试器可以通过使用* `*--debug*` *命令行标志启动节点或使用* `*SIGUSR1*` *向现有节点进程发送信号来启用和访问。(节点 API 文档)*

*爽！*所以，为了打开节点调试器代理，我只需要发送`SIGUSR1`信号给 TodoMVC 应用程序的 Node.js 进程。记住，它是在 Docker 容器中运行的。我可以使用什么命令向 Docker 容器中运行的应用程序发送进程信号？

这个`docker kill`命令——是我的选择！这个命令实际上并没有“杀死”运行在 Docker 容器中的`PID 1`进程，而是向它发送一个 [Unix 信号](https://en.wikipedia.org/wiki/Unix_signal)(默认情况下它发送`SIGKILL`)。

## 将 TodoMVC 设置为`debug`模式

因此，我需要做的就是将`SIGUSR1`发送到我的 TodoMVC 应用程序，该应用程序运行在`todomvc` Docker 容器中。

有两种方法可以做到这一点:

1.  使用`docker kill --signal`命令将`SIGUSR1`发送到 Docker 容器内运行的`PID 1`进程，如果它是一个“正确的”(信号转发正确)init 应用程序(如`tini`)，那么它将工作
2.  或者，在已经运行的 Docker 容器中执行`kill -s SIGUSR1`，向主 Node.js 进程发送`SIGUSR1`信号。

```
$ # send SIGUSR1 with docker kill (if using proper init process) 
$ docker kill --signal SIGUSR1 todomvc 
$ # OR run kill command for node process inside todomvc container 
$ docker exec -it todomvc sh -c ‘kill -s SIGUSR1 $(pidof -s node)’
```

让我们验证节点应用程序是否设置为`debug`模式。

```
$ docker logs todomvc TodoMVC server listening at [http://:::3000](http://:::3000) 
emitting 2 todos server has new 2 todos 
GET / 200 31.439 ms — 3241 
GET /app.css 304 4.907 ms 
— — 
Starting debugger agent. 
Debugger listening on 127.0.0.1:5858
```

正如您所看到的，Node.js 调试器代理已经启动，但是它只能接受来自`localhost`的连接，请参见最后一行输出:`Debugger listening on 127.0.0.1:5858`

# 步骤 2:公开节点调试端口

为了将远程 Node.js 调试器附加到运行在`debug`模式下的节点应用程序，我需要:

1.  允许从任何(或特定)IP(或 IP 范围)连接到调试器代理
2.  在 Docker 容器外打开 Node.js 调试器代理的端口

当一个应用程序已经在 Docker 容器中运行，Node.js 调试器代理准备好只与同一台机器上运行的 Node.js 调试器进行对话，并且 Node.js 调试器代理端口无法从 Docker 容器外部访问时，如何做到这一点？

当然，可以使用暴露的调试器端口启动每个 Node.js Docker 容器，并允许来自任何 IP 的连接(使用特殊的`--debug-port`和`--debug` Node.js 标志)，但我们并不寻找简单的方法:)。

从安全角度来看这不是一个好主意(允许对 Node.js 调试器进行无保护的访问)。此外，如果我使用调试标志重新启动一个已经运行的应用程序，我将失去当前的执行上下文，并且可能无法重现我想要调试的问题。

*我需要更好的解决方案！*

不幸的是，Docker 不允许为已经运行的 Docker 容器暴露额外的端口。因此，我需要以某种方式连接到一个正在运行的容器网络，并为 Node.js 调试器代理公开一个新端口。

此外，当 Node.js 进程已经启动时，不可能告诉 Node.js 调试器代理接受来自不同 IP 地址的连接。

以上两个问题都可以在 Linux 的小工具`socat` (SOcket CAT)的帮助下解决。这就像`netcat`一样，但考虑到了安全性(例如，它支持 chrooting ),并通过文件、管道、设备、TCP 套接字、Unix 套接字、SOCKS4 客户端、代理连接或 SSL 等在各种协议上工作。

来自`socat`手册页:

> `*socat*` *是一个基于命令行的实用程序，建立两个双向字节流并在它们之间传输数据。因为流可以由大量不同类型的数据汇和数据源构成(参见地址类型)，并且因为许多地址选项可以应用于流，* `*socat*` *可以用于许多不同的目的。*

*正是，我所需要的！*

因此，计划是这样的:我将运行一个新的 Docker 容器，并在其上运行`socat`实用程序，并为 TodoMVC 容器配置 Node.js 调试器端口转发。

`socat.Dockerfile`:

```
FROM alpine:3.5 
RUN apk add --no-cache socat 
CMD socat -h
```

## 建筑码头集装箱

```
$ docker build -t local/socat — < socat.Dockerfile
```

## 允许从任何 IP 连接到节点调试器代理

我需要在与`todomvc`容器相同的网络名称空间中运行一个“sidecar”`socat`容器，并定义一个端口转发。

```
$ # define local port forwarding 
$ docker run -d --name socat-nip --network=container:todomvc \
      local/socat socat TCP-LISTEN:4848,fork TCP:127.0.0.1:5858
```

现在，到达`4848`端口的任何流量都将被路由到 Node.js 调试器代理侦听`127.0.0.1:5858`。`4848`端口可以接受来自任何 IP 的流量。
也可以使用 IP 范围来限制连接到`socat`监听端口，增加了`range=&lt;ANY IP RANGE&gt;`选项。

## 从 Docker 容器公开 Node.js 调试器端口

首先，我们将获得`todomvc` Docker 容器的 IP。

```
$ # get IP of todomvc container 
$ TODOMVC_IP=$(docker inspect -f “{{.NetworkSettings.IPAddress}}” todomvc)
```

然后，配置端口转发到我们之前定义的“sidecar”`socat`端口，它与`todomvc`容器运行在同一个网络上。

```
$ # run socat container to expose Node.js 
$ # debugger agent port forwarder 
$ docker run -d -p 5858:5858 --name socat local/socat \
      socat TCP-LISTEN:5858,fork TCP:${TODOMVC_IP}:4848
```

将到达 Docker 主机上的`5858`端口的任何流量将首先被转发到`4848` socat 端口，然后被转发到在`todomvc` Docker 容器内运行的 Node.js 调试器代理。

## 为远程访问公开 Node.js 调试器端口

在大多数情况下，我希望调试运行在远程机器上的应用程序(例如，AWS EC2 实例)。我也不想将 Node.js 调试器代理端口暴露给全世界。

一个可行的解决方案是使用 SSH 隧道来访问这个端口。

```
$ # Open SSH Tunnel to gain access to servers port 5858\. 
$ # Set `SSH_KEY_FILE` to ssh key location or add it to ssh-agent 
$ # 
$ # open an ssh tunnel, send it to the bg, and wait 20 seconds 
$ # for connections, once all connections are closed 
$ # after 20 seconds then close the tunnel 
$ ssh -i ${SSH_KEY_FILE} -f -o ExitOnForwardFailure=yes \
      -L 5858:127.0.0.1:5858 ec2_user@some.ec2.host.com sleep 20
```

现在，`localhost:5858`的所有流量将通过`SSH`隧道传输到远程 Docker 主机，并在`socat`转发到运行在`todomvc`容器内的 Node.js 调试器代理。

# 步骤 3:在相同的代码提交上同步

为了能够调试远程应用程序，您需要确保您在 IDE 中使用的代码与在远程服务器上运行的代码相同。

我也将尝试自动化这一步。还记得我在 TodoMVC `Dockerfile`中用过的`LABEL`命令吗？

这些标签帮助我识别 git 存储库并提交应用程序 Docker 映像:

1.  `org.label-schema.vcs-ref`–包含用于`HEAD`提交的短 SHA
2.  `org.label-schema.vcs-url`–包含一个应用程序 git 存储库 URL(我可以在`clone/pull`中使用)

我使用(标签模式约定)[http://label-schema.org/rc1/]，因为我真的很喜欢它，并且发现它很有用，但是您也可以选择任何其他约定。

这种方法允许我为每个正确标记的 Docker 映像识别应用程序代码库和创建它的提交。

```
$ # get git repository url form Docker image 
$ GIT_URL=$(docker inspect local/todomvc 
    | jq -r ‘.[].ContainerConfig.Labels.”org.label-schema.vcs-url”’)
$ # get git commit from Docker image 
$ GIT_COMMIT=$(docker inspect local/todomvc 
    | jq -r ‘.[].ContainerConfig.Labels.”org.label-schema.vcs-ref”’)
$ 
$ # clone git repository, if needed 
$ git clone $GIT_URL 
$ # set HEAD to same commit as server 
$ git checkout $GIT_COMMIT
```

现在，我的本地开发环境和远程应用程序都在同一个 git commit 上。我终于可以开始调试我的代码了！

# 步骤 4:将本地 Node.js 调试器附加到调试器代理端口

要开始调试，我需要配置我的 IDE。在我的例子中，它是 [Visual Studio 代码](https://code.visualstudio.com/)，我需要添加一个新的`Launch`配置。

此启动配置指定要附加的远程调试器服务器和端口，以及应用程序源文件的远程位置，这些文件应该与本地文件同步(请参见上一步)。

```
{ // For more information about Node.js debug attributes, visit: [https://go.microsoft.com/fwlink/?linkid=830387](https://go.microsoft.com/fwlink/?linkid=830387) 
“version”: “0.2.0”, 
“configurations”: [ { 
    “type”: “node”, 
    “request”: “attach”, 
    “name”: “Debug Remote Docker”, 
    “address”: “127.0.0.1”, 
    “port”: 5858, “localRoot”: 
    “${workspaceRoot}/”, 
    “remoteRoot”: “/usr/src/app/” } 
] }
```

# 摘要

最后，我实现了我的目标:我能够将 Node.js 调试器附加到 Node.js 应用程序，该应用程序已经在远程机器上的 Docker 容器中启动并运行。找到合适的解决方案是一个漫长的旅程，但在我找到它之后，这个过程看起来一点也不复杂。

现在，一旦我在我们的环境中遇到新问题，我可以轻松地将 Node.js 调试器附加到正在运行的应用程序，并开始探索问题。很好，不是吗？

我已经录制了一个短片，只是为了演示所有步骤，证明事情正在顺利进行，正如我在这篇文章中描述的那样。

希望，你觉得这篇帖子有用。我期待您的评论和任何问题。

*原载于 2017 年 6 月 6 日*[*code fresh . io*](https://www.codefresh.io/blog/debug_node_in_docker/)*。*

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[赞/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！