# 用 Docker 中的无头 Chrome 运行 karma 测试

> 原文：<https://medium.com/hackernoon/running-karma-tests-with-headless-chrome-inside-docker-ae4aceb06ed3>

在 Docker 容器中运行构建步骤、处理环境之间的隔离(防止依赖版本之间的冲突)、支持在不同操作系统(macOS、Linux、Windows)上开发和可靠的构建(稳定的构建环境)可能会很有用。

在尝试建立这样的环境时，您会遇到一些障碍。我浏览了它们，并创建了一个示例样板项目，展示了一些设置。(注意我设置了一个使用[纱](https://yarnpkg.com/lang/en/)代替 npm 的项目；使用 npm 创建一个类似的设置是完全可能的。)

您可以在 GitHub 上查看完整的代码示例:

【https://github.com/eirslett/chrome-karma-docker 号

## 向 Docker 图像添加 Google Chrome

从 node.js 官方图片来看，从官方安装 Chrome 是有可能的。deb 套餐。

还要注意，我添加了[dumb-init](https://github.com/Yelp/dumb-init)；它将你从许多痛苦和烦恼中解救出来，比如当你试图使用 Ctrl+C 来停止你的容器时。

```
**FROM** node:8.2.1

*# OPTIONAL: Install dumb-init (Very handy for easier signal handling of SIGINT/SIGTERM/SIGKILL etc.)* **RUN** wget https:**//**github.com**/**Yelp**/**dumb-init**/**releases**/**download**/**v1.2.0**/**dumb-init_1.2.0_amd64.deb
**RUN** dpkg **-**i dumb-init_*****.deb
**ENTRYPOINT** [**"dumb-init"**]

*# Install Google Chrome* **RUN** wget **-**q **-**O **-** https:**//**dl-ssl.google.com**/**linux**/**linux_signing_key.pub | apt-key add **-
RUN** sh **-**c **'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list'
RUN** apt-get update **&&** apt-get install **-**y google-chrome-stable**WORKDIR /**opt**/**app**ADD** package.json yarn.lock **/**opt**/**app**/
RUN** yarn
```

## 检测 Docker，并配置 Karma

事实证明，在 Docker 中运行 Chrome 存在一些问题。除非你在特权模式下运行容器，否则 Chrome 的沙箱不会工作。我们使用 [is-docker](https://github.com/sindresorhus/is-docker) 包来检测进程是否在 docker 容器中运行。基于此，我们必须调整我们的构建配置:

```
**const** isDocker = require(**'is-docker'**)();

module.exports = config => config.set({
  **customLaunchers**: {
    **ChromeCustom**: {
      **base**: **'ChromeHeadless'**,
      *// We must disable the Chrome sandbox when running Chrome inside Docker (Chrome's sandbox needs
      // more permissions than Docker allows by default)* **flags**: isDocker ? [**'--no-sandbox'**] : []
    }
  }
  // more karma configuration here...
}
```

Karma 应该与 ChromeCustom 浏览器一起运行。

## 额外部分/非主题:web pack/web pack-dev-Docker 内部的服务器

如果您想在容器中运行 Webpack 或 webpack-dev-server，您应该使用 is-docker 检测并调整一些配置:

1.  如果在容器内部运行，将 webpack-dev-server 的主机设置为“0.0.0.0 ”,这样就可以从外部访问它
2.  如果在容器内运行，则将 watchOptions 设置为{ aggregateTimeout: 300，poll: 1000 }

## 设置 docker 撰写

我们可以使用 docker-compose.yml 来设置配置参数，而不是使用数量惊人的参数来运行 docker run:

```
**version: '3'

services:
  dev:
    build:
      context:** .
      **dockerfile:** docker/dev/Dockerfile
    **working_dir:** /opt/app
    **container_name: "dev"
    volumes:** - **"./:/opt/app"** - /opt/app/node_modules
```

这将让我们运行“docker-compose run dev XXX”，例如，它将使用“XXX”作为命令来启动 docker 容器

*   docker-compose 运行开发纱线
*   docker-合成运行开发纱线运行测试
*   docker-合成运行开发纱线运行构建

## 为了当地的发展

如果可以在不运行 Docker 的情况下处理您的项目，那可能更好，因为您可以避免容器化的开销。然后，你应该可以直接“yarn run [whatever-command]”。

如果您更喜欢在容器中处理项目，您可以使用“docker-compose run dev yarn run[whatever-command]”在容器中运行。

提示:将一个助手脚本“container.sh”添加到您的工作目录中:

```
**#!/bin/bash** docker-compose run dev $@
```

然后你就可以跑了”。/container . sh yarn run[whatever-command]”，稍微短一点。

## 在 CI 上运行

确保 Docker 安装在 CI 实例上。(咄！)

您可能希望为每次 CI 运行重建容器:

```
docker-compose build
```

之后，您可以以同样的方式在容器内部运行 CI 构建脚本；

```
docker-compose run dev yarn run build
```

仅此而已。祝你的容器化版本好运！