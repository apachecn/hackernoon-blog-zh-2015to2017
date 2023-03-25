# 教程:使用无服务器实现更快的人工智能开发

> 原文：<https://medium.com/hackernoon/tutorial-faster-ai-development-with-serverless-684f3701b004>

两个最热门的技术是人工智能和无服务器，你猜怎么着？他们甚至很配。在进入一些很酷的例子之前，让我们从一些 AI 基础知识开始:

人工智能涉及一个[学习](https://hackernoon.com/tagged/learning)阶段，在这个阶段中，我们观察历史数据集中的模式，通过训练识别或学习模式，并建立机器学习模型。一旦创建了模型，我们就用它来进行推理(服务)以预测一些结果或对一些输入或图像进行分类。

传统的机器学习方法包括长时间的批处理或迭代过程，但我们看到了向更连续的过程或强化学习的转变。推理部分变得越来越受事件驱动，例如，一个机器人接受来自聊天的一行文本，并立即做出响应；电子商务网站接受客户特征并提供购买建议；交易平台监控市场反馈并以交易来响应；或者对一个图像进行实时分类，打开一扇智能门。

AI 有很多类别。不同的库和[工具](https://hackernoon.com/tagged/tools)可能更擅长某些任务，或者只支持特定的编码语言，所以我们需要学习如何开发和部署它们。扩展推理逻辑，使其高度可用，解决持续的开发、测试和操作使其更加困难。

这就是无服务器解决问题的地方，它提供了以下好处:

——加速发展

-简化部署和操作

-集成事件触发器和自动缩放

-支持多种编码语言和简化的包依赖关系

无服务器也有一些性能和可用性方面的缺点([在早先的帖子](https://www.iguazio.com/nuclio-future-serverless-computing/)中提到过)，但这些都可以通过新的高性能和开源无服务器平台 [nuclio](https://nuclio.io/) 来解决。

我们使用 [TensotFlow](https://www.tensorflow.org/) 、Azure APIs 和 VADER 编写了几个 nuclio 函数，以展示用无服务器构建一个人工智能解决方案是多么简单。这些解决方案将快速、自动扩展且易于部署。

[nuclio](https://nuclio.io/) 的独立版本可以在笔记本电脑上通过一个 Docker 命令进行部署，这使得使用下面的示例更加简单。这些函数可以用于其他无服务器平台，如 AWS Lambda，只需做一些简单的修改。

# 情感分析

在下面的例子中，我们使用了[Vader perspection](https://github.com/cjhutto/vaderSentiment)，这是一个用于检测文本情感的 Python 库。我们通过像 HTTP 这样的事件源输入一个文本字符串，并用一个分类结果进行响应。

在 nuclio 中，我们可以通过在函数头中添加特殊注释来指定构建依赖关系，如下所示，这可以节省很多麻烦。请注意 nuclio 中内置的日志记录功能，它有助于我们调试和自动化功能测试。

```
# @nuclio.configure
#
# function.yaml:
#   spec:
#     runtime: "python"
#     build:
#       commands:
#       - "pip install requests vaderSentiment"

from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer

def handler(context, event):
    body = event.body.decode('utf-8')
    context.logger.debug_with('Analyzing ', 'sentence', body)

    analyzer = SentimentIntensityAnalyzer()

    score = analyzer.polarity_scores(body)

    return str(score)
```

可以使用带有正文的 HTTP post 来激活该功能，并且该功能可能会以下面的格式用情感来响应:

{'neg': 0.0，' neu': 0.323，' pos': 0.677，' compound': 0.6369}

为了测试这个功能，使用下面的 Docker 命令运行 nuclio 的 playground，并通过浏览到 <host-ip>:8070(端口 8070)来访问 UI。在 GitHub 或预填充函数列表中找到这个和其他例子[。根据您的需要修改它，然后按 deploy 来构建它。](https://github.com/nuclio/nuclio/tree/master/hack/examples)</host-ip>

**docker run-p 8070:8070-v/var/run/docker . sock:/var/run/docker . sock-v/tmp:/tmp nucl io/playground:stable-amd64**

![](img/db765209299e66be858c399f513385ca.png)

nuclio playground UI (in port 8070)

有关在不同配置中使用和安装 nuclio 的更多详细信息，请参见 [nuclio 的网站](https://nuclio.io/)。

# 张量流图像分类

最受欢迎的人工智能工具之一是谷歌开发的 TensorFlow。它实现了神经网络算法，可用于分类图像，语音和文本。

在下面的例子中，我们将使用预先训练的初始模型来确定图片中的内容。参见 [nuclio 范例库](https://github.com/nuclio/nuclio/tree/master/hack/examples)中的完整源代码。

[TensorFlow](https://www.tensorflow.org/) 为我们带来了一些挑战，因为我们需要使用更大的基线 Docker 映像，如带有更多工具的“Jessie ”( nucl io 默认使用微小的 alpine 映像，以最大限度地减少占用空间和函数加载时间),我们需要添加添加请求、TensorFlow 和 numpy python 包。

AI 模型可以是一个大文件，加载和解压缩一次比每晚一次更实际。我们将通过在函数头添加以下注释/声明，通过构建指令将模型加载到函数映像中，或者我们可以在函数配置 UI 选项卡中指定构建指令:

```
# @nuclio.configure
#
# function.yaml:
#   spec:
#     runtime: "python:3.6"
#     build:
#       baseImageName: jessie
#       commands:
#       - "apt-get update && apt-get install -y wget"
#       - "wget http://download.tensorflow.org/models/image/imagenet/inception-2015-12-05.tgz"
#       - "mkdir -p /tmp/tfmodel"
#       - "tar -xzvf inception-2015-12-05.tgz -C /tmp/tfmodel"
#       - "rm inception-2015-12-05.tgz"
#       - "pip install requests numpy tensorflow"
```

我们还将使用一个线程在第一次调用时将模型加载到内存中，并在随后的调用中保存它。我们通过使用可选的环境变量来指定各种参数，使我们的函数更加灵活。

函数的主要部分(参见[完整代码](https://github.com/nuclio/nuclio/blob/master/hack/examples/python/tensorflow/tensor.py)的链接):

```
def classify(context, event):

    # create a unique temporary location to handle each event,
    # as we download a file as part of each function invocation
    temp_dir = Helpers.create_temporary_dir(context, event)

    # wrap with error handling such that any exception raised
    # at any point will still return a proper response
    try:

        # if we're not ready to handle this request yet, deny it
        if not FunctionState.done_loading:
            context.logger.warn_with('Model data not done loading yet, denying request')
            raise NuclioResponseError('Model data not loaded yet, cannot serve this request',
                                      requests.codes.service_unavailable)

        # read the event's body to determine the target image URL
        # TODO: in the future this can also take binary image data
        # if provided with an appropriate content-type
        image_url = event.body.decode('utf-8').strip()

        # download the image to our temporary location
        image_target_path = os.path.join(temp_dir, 'downloaded_image.jpg')
        Helpers.download_file(context, image_url, image_target_path)

        # run the inference on the image
        results = Helpers.run_inference(context, image_target_path, 5, 0.3)

        # return a response with the result
        return context.Response(body=str(results),
                                headers={},
                                content_type='text/plain',
                                status_code=requests.codes.ok)

    # convert any NuclioResponseError to a response.
    # the response's description and status will appropriately
    # convey the underlying error's nature
    except NuclioResponseError as error:
        return error.as_response(context)

    # in case of any error, respond with internal server error
    except Exception as error:
        context.logger.warn_with('Unexpected error occurred, responding with internal server error',
                                 exc=str(error))

        message = 'Unexpected error occurred: {0}\n{1}'.format(error, traceback.format_exc())
        return NuclioResponseError(message).as_response(context)

    # clean up regardless of whether we succeeded or failed
    finally:
        shutil.rmtree(temp_dir)
```

使用 nuclio 上下文和事件详细信息对每个事件执行“分类”功能。该事件可以由各种来源触发(例如 HTTP、RabbitMQ、Kafka、Kinesis 等。)并在正文中包含一个图像的 URL，它是通过以下方式获得的:

image _ URL = event . body . decode(' utf-8 ')。条状()

该函数将图像下载到新创建的临时目录中，对其进行分类(Helpers.run_inference ),并在响应中返回最高分数及其概率。

我们通过调用 shutil.rmtree(temp_dir)在调用结束时删除临时文件，以确保该函数不会占用内存。

请注意 nuclio 函数中结构化和非结构化日志记录的广泛使用，因为我们可以在其中一个级别(调试、信息、警告、错误)记录每一步，并向日志事件添加参数。日志条目可用于在开发和生产过程中控制功能执行和调试。我们可以在运行时或每个函数调用时定义所需的日志记录级别(例如，通过 playground)，例如，仅在诊断问题时打印调试消息，从而避免正常生产操作中的性能和存储开销。

日志使用示例:

context.logger.debug_with('创建的临时目录'，path=temp_dir)

请注意，拥有结构化日志简化了功能监控或测试。当运行回归测试时，我们使用结构化的调试消息来自动验证函数行为。

nuclio 支持二进制内容。可以修改每个函数，通过 HTTP POST 事件直接接受 JPEG 图像，而不是通过较慢的获取 URL 和获取其内容的过程。详见[图片大小调整示例](https://github.com/nuclio/nuclio/blob/master/hack/examples/golang/image/main.go)。

我们计划使用 nuclio 进一步简化这一过程，并添加“卷数据绑定”，这将允许在函数中安装文件共享，并允许我们动态更改模型。我们还计划在 Kubernetes 中添加 GPU 支持，从而实现更快、更具成本效益的分类。敬请关注。

# 使用 nuclio 的云人工智能服务(Azure Face API)

领先的云提供商现在正在提供可以通过 API 访问的预学习人工智能模型。一个这样的例子是 [Azure 的 Face API](https://azure.microsoft.com/en-us/services/cognitive-services/face/) ，它接受一个图片 URL，并返回在图片中找到的 Face 对象列表。

我们创建了一个 nuclio 函数，它接受一个 URL，用适当的凭证将它传递给 Azure 的 Face APIs，解析结果，并以 Face 对象表的形式返回它们，这些对象按照它们在给定图片中的中心位置排序，从左到右，然后从上到下。对于每张脸，该函数返回该脸的矩形位置、估计年龄、性别、情绪以及是否包含眼镜。

我们使用构建指令来指定库依赖项，使用环境变量来指定所需的凭证。在家里尝试之前，请确保您获得并设置了自己的 Azure 密钥(参见代码注释中的说明)。

这里有[源](https://github.com/nuclio/nuclio/blob/master/hack/examples/python/facerecognizer/face.py)，或者在我们的预装操场示例列表中找到它们(称为“face”)。

# 摘要

nuclio 等无服务器平台有助于更快地测试、开发和产品化 AI。我们计划定期向 nuclio 添加更多人工智能示例，允许开发人员获取预先开发和测试的代码，并根据他们的需求进行修改。

注册我们的[在线黑客马拉松](https://nuclio.devpost.com/)，在 nuclio 上构建最棒的无服务器应用，帮助我们扩展 nuclio 的函数库示例。你可能会赢得一架 Phantom 4 Pro 无人机…更多详情请访问 [devpost nuclio 网站](https://nuclio.devpost.com/)。也在 Github 上给 nuclio 一颗[星](https://github.com/nuclio/nuclio)和[加入我们的 Slack](https://lit-oasis-83353.herokuapp.com/) 社区。

特别感谢 Omri Harel 实现并调试了上面的 AI 函数。