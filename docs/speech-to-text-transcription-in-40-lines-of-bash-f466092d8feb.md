# Bash 中 40 行的语音到文本转录

> 原文：<https://medium.com/hackernoon/speech-to-text-transcription-in-40-lines-of-bash-f466092d8feb>

有没有想过开发一款可以接收用户音频输入的应用？将音频集成到应用程序中有很多好处——从简单地转录音频文件到在文本输出上运行 NLP。但是语音识别不是一项简单的任务。你可能想专注于开发自己的应用，而不是构建和训练一个转录音频流的模型。

输入机器学习 API！使用 ML 作为 API，您可以利用完成常见 ML 任务的预训练模型，将 ML 添加到您的应用程序中，而无需重新发明轮子。

# 给我看看代码

我编写了一个简单的 bash 脚本，它使用 [SoX](http://sox.sourceforge.net/) 制作音频记录，base64 编码音频文件，并将其发送到[云语音 API](https://cloud.google.com/speech/) 。**的完整代码是** [**这里是**](https://github.com/sararob/ml-talk-demos/blob/master/speech/request.sh) ，下面我将重点介绍让它运行的关键步骤。

## 安装 SoX

如果你已经安装了自制软件，你可以用一个简单的命令来安装 SoX:`brew install sox --with-flac`(`--with-flac`标志增加了对 [FLAC 编码](https://xiph.org/flac/)的支持，这是我们在语音 API 中需要的)。也可以[直接安装 SoX](https://sourceforge.net/projects/sox/files/sox/)。SoX 给了我们`rec`命令，我们将使用它来记录我们的音频。

## 录制音频并进行 base64 编码

安装 SoX 后，您可以使用以下内容录制 5 秒钟的音频:

```
rec --channels=1 --bits=16 --rate=16000 audio.flac trim 0 5
```

这将使用 FLAC 编码录制 5 秒钟的音频，并将其保存到一个名为`audio.flac`的文件中。然后，我们将对该文件进行 base64 编码，以将其发送到语音 API(它也支持其他格式):

```
base64 audio.flac > audio.base64
```

现在可以发送到语音 API 了！我们将在一个 JSON 文件中准备我们的请求。

## 创建语音 API 请求

在 Bash 中，我们可以创建一个`request.json`文件:

```
FILENAME=”request-”`date +”%s”.json`
cat <<EOF > $FILENAME
{ 
  "config": { 
    "encoding":"FLAC", 
    "languageCode": "en-US", 
    "speechContexts": { 
      "phrases": [''] 
    }, 
    "maxAlternatives": 1 
  }, 
  "audio": { 
    "content": 
  }
}
EOF
```

请注意，我们将`content`留空，我们将用 base64 编码的音频内容来填充它:

```
sed -i '' -e '/"content":/r audio.base64' $FILENAME
```

我们已经准备好通过`curl`提出我们的请求:

```
curl -s -X POST -H "Content-Type: application/json" --data-binary @${FILENAME} [https://speech.googleapis.com/v1/speech:recognize?key=YOUR_API_KEY](https://speech.googleapis.com/v1/speech:recognize?key=YOUR_API_KEY)
```

这里，`${FILENAME}`是一个 Bash 变量，带有您的`request.json` 文件的名称(在完整的 Bash 脚本中，我给文件名附加了一个时间戳，这样它们就不会被覆盖)。用云 API 密钥替换`YOUR_API_KEY`——你可以在你的云控制台的**凭证**部分生成一个。

就是这样！在 Bash 的 40 行中，你有语音到文本的转录。

该脚本还支持用不同语言的文件调用 API。运行脚本时，只需添加一个语言代码作为命令行参数，即`bash request.sh fr`。这将把 JSON 请求的`languageCode`部分更改为您指定的语言。

# **云语音 API 还能做什么？**

我上面的 Bash 示例显示了向 API 发送一个完整的音频文件(一个批处理请求)。该 API 还支持流式传输，因此你可以向它发送连续的音频流，API 将返回临时结果。点击此处找到关于流识别[的文档。](https://cloud.google.com/speech/docs/streaming-recognize)

请求中的`speechContext`参数是我最喜欢的语音 API 特性之一。假设您有一个专有名词或一些专门针对您的应用程序的术语。预先训练的模型可能不会识别这些特定于行业的术语，但是您可以提供提示，告诉模型注意这些术语。例如，我在几个 DevFests 上讲过话——一个特定类型的 Google 开发者团体活动的名称。如果我向 API 传递一个带有“DevFest”的音频文件，它通常会将其转录为“death fest”，这绝对不是一回事！当我通过`speechContext`参数提供一个提示时，它完美地转录了“DevFest”。

# 谁在使用语音 API？

虽然转录一个 5 秒钟的音频文件很有趣，但你可能想知道这是如何在生产中使用的。这里有两个例子:

*   Twilio :他们最近宣布了一项自动语音识别服务，用于转录电话，由语音 API 提供支持。
*   [**Azar**](https://cloud.google.com/customers/hyperconnect/) :一款聊天应用，连接了超过 150 亿场比赛，使用语音 API 转录比赛之间发送的任何音频片段。

# 开始转录音频

以下是这篇文章中提到的所有资源的列表:

*   [Bash 脚本代码](https://github.com/sararob/ml-talk-demos/blob/master/speech/request.sh)
*   [语音 API 文档](https://cloud.google.com/speech/docs)
*   [我的云下一节演讲 API 上的](https://youtu.be/w1xNTLH1zlA?t=10m42s)

享受吧，如果你对 Twitter [@SRobTweets](https://twitter.com/srobtweets) 有任何语音 API 反馈，请告诉我。