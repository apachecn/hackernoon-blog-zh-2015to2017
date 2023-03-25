# 介绍 exo frame(beta)—now . sh 的自托管替代方案

> 原文：<https://medium.com/hackernoon/introducing-exoframe-beta-self-hosted-alternative-to-now-sh-80643f96b84b>

早在 2016 年 9 月，我已经[展示了](https://hackernoon.com/simplifying-docker-management-with-exoframe-9275e92c7406)第一个公开版本的 Exoframe。我曾经把它定位为简化 Docker 管理的工具。这个想法非常简单——消除编写、维护 docker 映像并将其部署到服务器的痛苦。
这是通过模板和项目检测完成的——exo frame alpha 会读取你的项目目录，试图找出要使用的模板，从该模板添加 Dockerfile，然后将其部署到你的服务器上，同时问你一堆问题。
从长远来看——它对我(或其他任何人，根据下载统计)都不太管用。每次部署都要回答这么多问题，这很烦人，没有办法轻松地将域分配给演示，而且我编码到 deploy step 中的大多数 Docker 特性并没有像我想象的那样频繁使用。因此，在某个时候，我干脆完全停止使用它。

然后我从 [ZEIT](https://zeit.co/about) 发现了 [Now.sh](http://now.sh/) 。这是一个非常简单的工具，允许你在一个命令中部署节点、HTML 和 Docker 项目。不问任何问题。
尽管现在很棒，而且每次更新都会变得更好(例如[最新更新](https://zeit.co/blog/universal-now)允许你使用 GCP、AWS 和 Azure)，但它仍然有其局限性。对我来说最大的一个问题是，我不能在自己的服务器上使用它。我敢肯定，你们中的一些人也有一些演示服务器，一个或两个已经拥有的 VPS，甚至可能有一些专用服务器只是为了好玩。遗憾的是，目前没有办法运行自托管。
另一个问题是复杂的 docker 项目——虽然现在允许您从简单的 docker 文件部署项目，但现在有办法使用描述多个服务的 [docker-compose](https://docs.docker.com/compose/) 来部署项目(例如，具有后端和前端的数据库)。

这就是为什么我决定重新思考和构建 Exoframe 来解决所有这些问题。可以把它看作是 Now 的一个自托管替代方案，它允许您轻松地将项目部署到您的服务器上，同时获得自动化的域配置、负载平衡、HTTPS(多亏了 letsencrypt)和所有这些好东西。

如果你喜欢视频格式，你可以在下面的视频中观看演示和解释它是如何工作的。如果您喜欢文本—滚动:)

# Exoframe 服务器及其工作原理

Exoframe 的工作方式非常简单——你唯一需要安装的就是一个带有 [Docker 守护进程](https://docs.docker.com/engine/installation/)的服务器。一些服务器提供商甚至具有预配置的服务器(例如来自 DigitalOcean 的[一键式 Docker 服务器)。](https://www.digitalocean.com/products/one-click-apps/docker/)

Exoframe 服务器本身以一种非常简单的方式工作——在执行时，它将产生一个 [Traefik](https://traefik.io/) 实例，该实例将处理域管理、HTTPS、负载平衡和所有这些事情。然后，它简单地从 Exoframe CLI 获得打包的项目，并将其部署到您的 Docker 守护进程，同时为您配置 Traefik 和 Docker。
身份验证是使用私有-公共 RSA 密钥完成的，所以如果您已经可以使用您的私有密钥 SSH 到您的服务器，那么通常您不需要任何额外的设置。如果您仍然使用 SSH 的登录名和密码，请参见本指南[帮助您切换到更安全的密钥认证。](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)

你所需要做的就是在你的 Docker 守护进程中执行 Exoframe server，就像这样:

该命令将启动 Exoframe 服务器，允许它通过 *docker.sock* 访问您的 docker，将 config 文件夹链接到您的本地文件夹以在重启/更新/等时保存配置，链接您的 *authorized_keys* 文件以进行身份验证，最后—为 [Traefik](https://traefik.io/) 设置规则。您可以在[服务器存储库](https://github.com/exoframejs/exoframe-server)中找到每一行更详细的解释。

# Exoframe 客户端和命令

一旦 Exoframe 服务器启动，您将需要安装 Exoframe CLI。目前，这只能使用 npm 来完成，所以你需要在你的系统上安装最新的 [Node.js 和 NPM](https://nodejs.org/en/download/package-manager/)。
一旦你有了它们，你可以使用下面的命令安装 exoframe:

```
npm install exoframe -g
```

之后，您需要使用以下命令将 Exoframe CLI 指向新设置的服务器:

```
exoframe endpoint http://exoframe.server.url
```

最后使用`exoframe login`登录，并选择已经被授权访问您的服务器的私钥。

登录后，您将可以访问主要命令:

*   `exoframe [deploy]` —将当前项目部署到您的服务器
*   `exoframe config` —为当前项目生成新的配置文件
*   `exoframe list | ls` —列出活动部署
*   `exoframe remove | rm <id>` —删除活动部署
*   `exoframe logs | log <id>` —显示活动部署的日志

`deploy`命令目前理解并能够处理以下项目类型:

1.  基于静态 HTML 的项目——将使用 [nginx](http://hub.docker.com/_/nginx) 映像进行部署
2.  基于 Node.js 的项目—将使用[节点:alpine](https://hub.docker.com/_/node) 映像进行部署
3.  基于 Docker 的项目—将使用您的[docker 文件](https://docs.docker.com/engine/reference/builder/)进行部署
4.  基于 Docker-compose 的项目——将使用您的 [docker-compose](https://docs.docker.com/compose/compose-file/) 文件进行部署

使用配置文件完成对`deploy`命令的配置，该文件包含您的项目名称、您想要分配给它的域、您想要设置的环境、重启策略和内部容器主机名。配置文件的结构在 Exoframe 自述文件中有所介绍。

## 有 letsencrypt 的 HTTPS

因为 Exoframe 使用 Traefik 来管理域，所以您可以通过 letsencrypt 证书获得对 HTTPS 的现成支持！
要启用它，您需要编辑您的服务器配置文件并设置以下字段:

```
letsencrypt: true # whether to enable letsencrypt, default "false"
letsencryptEmail: your@email.com # email used for letsencrypt
```

一旦设置完成，只需启动您的 Exoframe 服务器。
**警告:**如果您已经运行了 Exoframe 服务器和 Traefik，您还需要从 Docker 守护进程中删除旧的 Traefik 实例并重启 Exoframe 服务器。这是必需的，因为 Traefik 需要用附加的 letsencrypt 参数重新创建。完成后，你所有的项目都将自动部署到 HTTPS，并带有 letsencrypt 证书！

## 子域自动生成

Exoframe server 还允许您启用自动子域名称生成(类似于 Now.sh 的方式，但使用您的域)。这可以通过将服务器中的`baseDomain`字段设置为您想要使用的顶级域名来实现。例如，将它设置为`.codezen.net`将导致部署的服务没有明确指定的域名，而获得像`exo-user-demo-d234ah3.codezen.net`
这样的域名。当您想要快速部署和测试您的项目而不使用它们的真实域名时，这很有用。

# 当前的限制

Exoframe 显然有自己的局限性:

*   最大的一个问题是对 docker-compose 项目配置的有限支持。虽然 Exoframe 可以很容易地操作它直接部署的容器，但是改变 docker-compose 服务的配置是非常有限的。因此，您必须手动设置域的 Traefik 标签。不幸的是，除了提供更好的文档，没有真正的方法来“修复”这个问题。
*   另一个限制是，目前您只能使用私有-公共密钥对登录。虽然这对人们来说工作得很好，但是从 CI/CD 守护进程中使用它并不太好。我计划在未来通过添加部署令牌来解决这个问题，这将允许重新部署一个特定的项目，而不需要使用私钥进行身份验证。
*   一个更明显的限制是只支持 4 种项目类型。虽然 Dockerfile 和 Docker-compose 项目确实允许用户部署几乎任何东西，但拥有更多自动配置的项目可能会更好。如果有足够的兴趣的话，我计划将来通过在项目部署中引入插件系统来解决这个问题。

# 结论

如果你觉得 Exoframe 有趣——试试吧！
beta 版[现已在 GitHub](https://github.com/exoframejs/exoframe) 上发布。
一如既往——感谢您的任何反馈和贡献。