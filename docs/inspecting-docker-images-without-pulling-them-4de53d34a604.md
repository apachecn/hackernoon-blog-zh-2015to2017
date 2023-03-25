# 检查 Docker 图像，无需拉动它们

> 原文：<https://medium.com/hackernoon/inspecting-docker-images-without-pulling-them-4de53d34a604>

![](img/ceeecc9855ac61f216a650aec9c090e4.png)

嘿，

根据你要构建的内容，可能会发生这样的情况，其中一部分涉及到从注册表中检查 Docker 映像，但是你负担不起。

原来[有一个 API](https://docs.docker.com/registry/spec/api/) 可以让你做到这一点——无论是 DockerHub 还是私有注册表。

> *Docker Registry HTTP API 是一种便于向 Docker 引擎分发图像的协议。它与 docker registry 的实例进行交互，docker registry 是一种管理 docker 图像信息并支持其分发的服务。*

# 在本地测试

本地测试的第一步是从[库/注册表](https://hub.docker.com/_/registry/)映像中创建一个注册表。

检查它是否确实在工作:

让本地注册中心工作起来，我们可以直接从注册中心元数据转移到检查图像的脚本。下面的脚本包含了检索它所需要的全部内容，并且只依赖于两个依赖项:`bash`和 [jq](https://stedolan.github.io/jq/) 。

*ps。:需要注意的是，API 调用需要指定它接受的内容类型(* `*application/vnd.docker.distribution.manifest.v2+json*` *)。*

现在让我们来看看它是否真的有效:

酷，它确实有效！

虽然这对于不需要认证的注册中心来说是可行的，但它并不适合 DockerHub。在那里检查公共图像时，我们需要在获取图像摘要之前执行一个额外的步骤——检索一个令牌。有了这样的令牌，我们就可以检查公共或私有图像。

回到脚本，让我们创建一个不同的脚本来处理这种情况—称之为`get-public-image-config.sh`(这是为了简洁起见，使用一些其他编程语言，您可以放置一些条件并检测每种情况)。

额外的代码可以放在一个名为`get_token`的方法中，该方法只将`image`作为一个参数:

有了令牌，就只需要在其他呼叫中使用它了。

如果我们的目标是私有图像，我们需要稍微修改一下`get_token`:在从`auth.docker.io`获取令牌的调用中，我们需要使用 DockerHub 用户名和密码对(没有身份验证，我们只能访问公共图像)。为此，在调用中指定一个授权头(`curl`中的`--user`标志):

现在，有了这个新的令牌，我们可以检索给定图像和标签的摘要(注意我们添加的额外的`Authorization: Bearer $token`头):

这样我们就可以有一个完整的脚本来从 DockerHub 中检索公共图像(看看在`main`中我们如何首先检索一个令牌，然后我们将这个令牌传递给下面的方法):

*注意。:同样，必须添加图片的全名(官方图片使用的是* `*library*` *资源库所以* `*nginx*` *应该简称为* `*library/nginx*` *)。*

为了确保它能正常工作，对类似于 [nginx](http://hub.docker.com/_/nginx) 的图像运行它:

如果你试图检索一个不是很新的图像(比如说，有两年了)，你会注意到我上面发布的脚本可能不起作用。

原因是很久以前就被推送到 docker 注册表的图像不会使用第二版的 V2 清单。但是，即使在常规字符串中，它们仍然显示图像配置。

贝娄是一个处理这种情况的脚本:

如果你在寻找差异，看看`main`。本质上，我们放弃了检索一个`digest`的想法，简单地选择“旧配置”。在旧的配置中，我们看到了列表中的第一个 blob，它代表了最上层——包含所有信息的层。在那里，我们解析纯文本 JSON，然后获取配置。

# 结束语

与 DockerHub 或私有注册表交互并不困难，只是没有很好的文档记录。有了这些脚本，让它在您想要的任何语言上工作变得非常容易——只需添加一些检查，解析图像名称，您就可以开始工作了。

下面是文章中提到的资源:

*   [Docker 注册表 API —内容摘要](https://docs.docker.com/registry/spec/api/#content-digests)
*   [Docker 注册表 API —提取图像清单](https://docs.docker.com/registry/spec/api/#pulling-an-image-manifest)
*   [Docker 注册表 API —拉一层](https://docs.docker.com/registry/spec/api/#pulling-a-layer)
*   [要旨:这里提到的所有脚本](https://gist.github.com/cirocosta/17ea17be7ac11594cb0f290b0a3ac0d1)

在我第一次在 [ops.tips](https://ops.tips/blog/inspecting-docker-image-without-pull/) 上发表这篇文章后，我不能不提到人们建议的一些替代方案来结束这篇文章:

*   [https://github.com/GoogleCloudPlatform/container-diff](https://github.com/GoogleCloudPlatform/container-diff)—*“区分您的码头工人集装箱”*——这看起来很有趣，但是即使您可以在分析图像时为其指定`remote://`，它看起来总是会拖动整个图像。也许我做错了什么？
*   [https://github.com/projectatomic/skopeo](https://github.com/projectatomic/skopeo)—*“与远程图像注册中心合作——检索信息、图像、签署内容”* —嗯，言出必行！我完全推荐的目的是检查图像或存储库，而不拉他们👍

顺便说一下，如果你不想尝试 Skopeo，在 MacOS 上从源代码构建它非常容易:

现在，从 Dockerhub 检查图像或存储库只需要一个命令:

请注意，这里我为命令指定了`--override-os`标志。原因是，否则它将尝试通过标记有`OS=darwin`的摘要来检查图像或存储库过滤。如果您使用的是 Linux，就不需要使用该标志。

如果你不愿意用你最喜欢的语言来实现，而只想收集图像的配置，那么一定要检查 Skopeo。

如果我有错，或者有更简单的方法，请告诉我。

祝你玩得开心！

*精加工*

*原为发表于*[*ops . tips*](https://ops.tips/blog/inspecting-docker-image-without-pull/)《T21》2017 年 11 月 26 日。