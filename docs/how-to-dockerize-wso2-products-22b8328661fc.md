# 如何:对 WSO2 产品进行分类

> 原文：<https://medium.com/hackernoon/how-to-dockerize-wso2-products-22b8328661fc>

我曾经认为*对接是一个艰难的过程，但当我真正尝试过之后，我意识到这一点也不难。好吧，dockerizing 并不是一个真正的名字，但它是一种使用 docker 来容器化一个设置的简单方法。我没有想到这个名字，但我的同事们想到了，我只是用它，因为它实际上更容易这样称呼它:)。*

*无论如何，正如我前面提到的，一旦你真正开始做，它看起来一点也不难。让我用 docker 和 WSO2 产品一步一步地指导你。*

*首先，在你的电脑中安装 **docker** 和 **docker-compose** 。我不会详细讲述这个过程，因为你可以在网上找到几十篇解释如何做的文章。完成这一步后，创建一个文件夹(我称之为 *service_root* )并将 WSO2 产品的 zip 文件复制到其中。在 *service_root，*中创建一个名为 *Dockerfile* 的文件，并将其留空。我将在本文的后面解释在这个文件中放什么。然后在 *service_root* 中创建另一个目录，并将所有更改过的配置文件复制到该目录中(从现在开始，我将把该目录称为“资源”)。进入 resources 目录，在该目录下创建一个名为 product-setup.sh 的文件(您可以随意命名:)*

*完成初始框架后，在文本编辑器中打开 product-setup.sh 文件，使用 *apt-get install* 命令在容器中安装所有需要的包。例如，在这种情况下，您需要 unzip 程序来提取容器中的产品 zip 文件。在运行安装命令之前运行 *apt-get update* 命令以确保软件包管理器缓存得到更新也很重要。一个非常简单的示例 product-setup.sh 文件如下所示。*

```
*apt-get update
apt-get install -y unzipunzip /tmp/wso2ei-6.1.1.zip -d /wso2mv -f /tmp/resources/changed_config_files /wso2/wso2ei-6.1.1/path_to_the_relevant_config*
```

*不要担心产品 zip 文件是如何进入/tmp 的，我将在本文后面解释它。由此，您必须记住的主要思想是，首先您需要安装相关的应用程序，然后将产品 zip 解压缩到所需的文件夹，并用您放入 resources 目录的更新配置文件覆盖产品发行版中的相关配置文件。将“changed_config_files”重命名为文件名，将“path_to_the_relevant_config”重命名为分发中文件的路径。*

*完成 product-setup.sh 文件后，让我们看看*docker 文件*。这是 docker 在构建 docker 映像时调用的文件。在这个文件中，我们还可以指定 docker 映像的启动脚本。我先举一个适合当前语境的例子，逐行解释。*

```
*FROM airdock/oracle-jdk:1.8COPY wso2ei-6.1.1.zip /tmp
ADD resources /tmp/resourcesRUN chmod +x /tmp/resources/product-setup.sh && /tmp/resources/product-setup.sh
RUN chmod +x /tmp/resources/wait.shCMD ["sh", "/wso2/wso2ei-6.1.1/bin/integrator.sh"]*
```

*来自''的第一个'*'命令指定要用于当前停靠图像的基本停靠图像。这可以根据需求而改变。因为我试图在 docker 容器上运行一个需要 java 的产品，所以我选择了一个带有 java 的图像。如果您需要其他服务，那么您可以使用不同的预构建映像。请访问 [docker-hub](https://hub.docker.com/) 浏览可用的预建图像。您只需从这里选择一个图像，然后找到导入地址，然后使用来自*的*命令来使用这个预先构建的图像。**

**‘复制’*命令用于将产品分发复制到 docker 图像中。在这里，我将它复制到容器内的 */tmp* 文件夹中。然后将资源目录添加到容器中。这些路径在 product-setup.sh 文件中用于执行各种任务。然后授予对 product-setup.sh 的执行权限并运行它。" *CMD"* 命令指定*integrator . sh***必须在容器启动时运行。***

***现在我们已经完成了 *product-setup.sh* 和 *Dockerfile，*我们可以继续定义我们需要运行的任何其他服务。从包含 *Dockerfile* 的目录中出来，为其余的服务创建文件夹，以我们之前所做的相同方式，您也可以定义其余的服务。在定义了所有服务的设置之后，我们可以使用 docker-compose 来构建和运行这些服务。首先，我们需要在 *service_root* 目录之外定义 *docker-compose.yml* 文件。你可以参考 [*这个*](https://docs.docker.com/compose/compose-file/compose-file-v2/#blkio_config) 文档来更好地了解 *docker-compose.yml.* 在这篇文章中我将简要解释你如何使用 docker-compose 来构建和运行这些定义的容器。请参见下面的示例 *docker-compose.yml* 文件。***

```
***version: '2'services:
  integrator-service:
    image: integrator-img
    container_name: integrator-svc
    build: ./ei-setup
    depends_on:
      - analytics-service
      - mb-service
    ports:
      - 9443:9443
      - 8243:8243
      - 8280:8280***
```

***第一行指定了我们使用的 docker-compose 定义的版本。然后在 services 下，您可以定义您想要构建的服务的详细信息，并开始使用 docker-compose。在这个文件中，在 *services* 下，我们为我们的服务命名(在本例中为“ *integrator-service* ”)。“Image”指定构建映像的名称，“container_name”指定容器的名称，“build”指定特定服务的文件夹名称，“depends_on”指定在当前服务启动之前需要启动哪些服务。不过这有点棘手，因为当您列出一组需要首先启动的服务时，docker-compose 会确保在当前服务之前启动它们，但不会确保在启动当前服务之前这些服务已经启动并运行。因此，如果使用依赖服务，您可能会遇到问题，但是在这种情况下，您只需在服务的启动脚本中添加一行，等待这些依赖服务启动并运行。然后在“ports”部分，您可以指定哪些端口需要对外公开(*product _ port:container _ port*是公开端口的语法。***

***另外很重要的一点是，如果你想在这些服务之间进行通信，你可以把它们称为 ***协议://service_name:port*** 。协议可以是 http、tcp 等。服务名和端口必须在 *docker-compose.yml* 中定义。***

***现在，您已经设置好了，您可以运行命令***docker-compose build&&docker-compose up，*** 来构建并运行 docker 容器。***

***我希望这篇文章有助于理解如何使用 docker 封装 WSO2 产品。如果您在试用时遇到任何问题，请在评论区提出。我会尽快回答所有的问题。***