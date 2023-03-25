# 使用 Exoframe 在 10 分钟内完成 Node.js 项目的持续部署

> 原文：<https://medium.com/hackernoon/continuous-deployment-for-your-node-js-projects-in-10-minutes-with-exoframe-bdf48340c1be>

为您的项目设置连续部署可能非常棘手。Puppet、Ansible、Chef——所有这些工具都很棒，但有时您只是想要一些小而简单的工具来将您喜欢的项目部署到您的虚拟或专用服务器上。
我想向你展示如何使用 [Exoframe](https://github.com/exoframejs/exoframe) 在大约 10 分钟内为你的 Node.js 项目设置持续部署，包括滚动更新、HTTPS 和所有那些花哨的东西。

出于演示的目的，我们将基于 Express.js 部署简单的 Node.js 项目，你可以在 GitHub repo 中找到源代码(以及所有其他提到的部分):[https://github.com/exoframejs/node-cd-demo](https://github.com/exoframejs/node-cd-demo)

如果你更喜欢视频格式而不是阅读，你可以在这里看我做整个演练:

# 准备服务器

在开始之前，您显然需要一台服务器。如果你已经有了一个——太棒了。如果没有，就在[Hetzner](https://www.hetzner.com/?country=ot)/[DO](https://www.digitalocean.com/)/[Vultr](https://www.vultr.com/)或任何你喜欢的旅馆买一个。

一旦您用您喜欢的操作系统设置了服务器，您将需要[配置公钥 SSH 认证](https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server)。
这是必需的，因为 Exoframe 使用私有-公共密钥对进行认证过程。
如果您使用登录密码对进行 SSH 认证，我强烈建议切换到[公钥-私钥方法](https://security.stackexchange.com/questions/69407/why-is-using-an-ssh-key-more-secure-than-using-passwords)。
还要注意链接文章中关于在完成公钥认证部分后禁用密码认证的部分——在我看来，这也是必须的。

一旦你完成了这些——你需要[安装 Docker](https://docs.docker.com/engine/installation/) ,因为 Exoframe 依赖它来执行和管理服务。完成 docker 后，启动 [Exoframe 服务器](https://github.com/exoframejs/exoframe-server)只需执行以下命令:

```
docker run -d \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /path/to/exoframe-folder:/root/.exoframe \
  -v /home/user/.ssh/authorized_keys:/root/.ssh/authorized_keys:ro \
  -e EXO_PRIVATE_KEY=your_private_key \
  --label traefik.backend=exoframe-server \
  --label traefik.frontend.rule=Host:exoframe.your-host.com \
  --name exoframe-server \
  exoframe/server
```

确保更改参数以适应您的服务器，您至少希望:

1.  将`EXO_PRIVATE_KEY`设置为独特的东西。它在身份验证令牌的生成过程中使用。
2.  设置`--label traefik.frontend.rule=Host:exoframe.your-host.com`指向您的主机。它用于公开 Exoframe 服务器 API 端点。

*重要提示*:这也将启动一个 [Traefik](https://traefik.io/) 实例，它将占用端口 80 和 443。它用于将请求路由到您部署的服务，设置 HTTPS 等。

有关这些参数和启动程序的更多详细说明，请参见 Exoframe server GitHub 资源库中的[文档。](https://github.com/exoframejs/exoframe-server#installation-and-usage)

# 从 CLI 测试部署

在配置连续部署之前，我们需要确保 Exoframe 服务器工作正常。
为此，我们将在本地安装 Exoframe CLI，并手动部署我们的项目，看看是否可行。
目前，Exoframe CLI 仅在节点 8+上工作，因为它依赖于 async/await，所以请确保您安装了最新的节点。要在本地安装 Exoframe CLI，只需执行:

```
npm install exoframe -g
```

现在我们已经安装了它，我们需要将它指向我们的新服务器。您可以通过执行以下命令来实现这一点:

```
exoframe endpoint http://you.server.url
```

完成后，您将需要通过运行以下命令登录到您的服务器，并选择用于与您的服务器进行身份验证的私钥(并可能输入您的私钥的密码):

```
exoframe login
```

成功登录后，您可以部署您的项目。但是在部署之前，我们将做最后一件事—生成一个新的项目配置文件。这可以通过在项目文件夹中执行以下命令来完成:

```
exoframe config
```

该命令将以交互方式引导您完成配置创建。对于这个演示，唯一重要的字段是“域”。

*重要提示*:由于 Exoframe 不知道 Node.js 项目的入口点是什么，它将简单地在部署时执行`npm start`，并期望服务监听端口 80。所以，确保你的`package.json`中有`start`脚本，并使用端口 80！

一旦为您的新部署设置了所需的域，您就可以通过在项目文件夹中运行以下命令来部署项目:

```
exoframe
```

在 Exoframe CLI 报告项目已经部署后，您应该在配置步骤中输入的域上测试它的可用性。
如果一切按预期运行——我们准备好设置连续交付了！

# 使用 Travis-CI 配置连续交付

我们将使用 Travis-CI 来测试和部署我们的项目。
下面是 Node.js 项目的一个简单的`.travis.yml`配置:

Travis 为我们做了测试 Node.js 项目的大部分工作，所以我们在这里需要关心的是指定语言和 Node.js 版本。你可以在官方 Travis-CI 文档中了解更多信息。

现在我们已经有了 Travis 配置文件，并且我们已经确保它能够工作，我们需要向它添加 Exoframe 部署。
Exoframe 有一个名为“部署令牌”的特殊功能，允许您将项目部署到您的服务器，而无需使用您的私钥登录。因此，要设置部署，我们首先需要生成这样的令牌。这可以通过执行以下命令轻松完成:

```
exoframe token
```

在执行时，您应该得到一个新生成的令牌，它可以用来将项目部署到当前的 Exoframe 服务器。

*重要提示*:一定要保存好令牌，因为无法再次读取，只能生成一个新的。

一旦有了令牌，您需要使用 [Travis-CI 加密系统](https://docs.travis-ci.com/user/encryption-keys/)将加密的令牌存储在您的 Travis 配置中，如下所示:

```
travis encrypt EXO_TOKEN="your-token-here"
```

之后，您需要调整配置本身，以便在运行测试之前安装 Exoframe CLI，并在测试成功后触发部署。
配置文件应该是这样的:

在您将它推送到 GitHub 之后，您应该能够打开 Travis 日志并看到您的项目被部署到您的服务器上。

## 额外收获:滚动更新

注意上面配置中微小的`-u`标志。这告诉 Exoframe 服务器为当前部署执行滚动更新。
它的工作方式非常简单——它将部署新版本，等待它启动，然后拆除旧版本，这将实现零停机部署。

## 额外奖励:其他项目类型

Exoframe 还支持其他项目类型！
目前，除了 Node.js 项目，还可以部署静态 HTML 项目、Docker 项目和 Docker-Compose 项目。

# 试试看！

如果你觉得 Exoframe 有趣——试试吧！
GitHub 上现在已经有[了。
一如既往——感谢您的任何反馈和贡献。](https://github.com/exoframejs/exoframe)