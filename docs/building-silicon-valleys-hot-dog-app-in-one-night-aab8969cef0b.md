# 一夜之间打造硅谷的热狗应用

> 原文：<https://medium.com/hackernoon/building-silicon-valleys-hot-dog-app-in-one-night-aab8969cef0b>

![](img/d2835c9f7c927eddb6f0499b9052c6cd.png)

在看了 HBO 最新一集的[硅谷](https://hackernoon.com/tagged/silicon valley)和看到已经建成的[而不是热狗](https://www.theverge.com/tldr/2017/5/14/15639784/hbo-silicon-valley-not-hotdog-app-download) iOS 应用程序后，我想看看我是否能建一个和 Twitter 机器人一样的东西。

首先，我分叉了 [@BryanEBraun](http://twitter.com/BryanEBraun) 的牛逼开源，Node [Twitter bot](https://github.com/bryanbraun/twitter-listbot) 。它被设置为转发特定列表中的所有内容。我的第一个任务是设置它自动回复提到我的机器人 [IsItAHotdog](https://twitter.com/IsItAHotdog) 的推文。首先，我开始用我的手柄使用 Twitter 的流媒体 API 和 [tuiter](https://www.npmjs.com/package/tuiter) NPM 包来听任何推文

```
function listen() {    
    tu.filter({        
        track: 'isitahotdog'    
    }, function(stream) {        
        console.log("listening to stream");
        stream.on('tweet', onTweet);
    })
;}
```

然后我只回复那些有图片的

```
if(tweet.entities.hasOwnProperty('media') && tweet.entities.media.length > 0)
```

最后，我用[推帖](https://www.npmjs.com/package/tuiter)发了一条回复

```
tu.update({
    status: "@" + tweet.user.screen_name + message,        
    in_reply_to_status_id: tweet.id_str    
}, onReTweet);
```

接下来，我需要进行实际的分类。最初我打算收集图像并训练一个 Keras CNN，但我决定先用 [AWS Rekognition](https://aws.amazon.com/rekognition/) 运行一些东西。我仍然想回到这里，训练我自己的网。

使用 Rekognition 非常简单。我只是把图片从 Twitter 下载到我的服务器上，通过 [AWS](https://hackernoon.com/tagged/aws) Node SDK 把字节传递给 Rekognition，然后得到分类结果。

```
rekognition.detectLabels(params, function(err, data) {
```

它能够始终如一地给含有热狗的图片贴上标签。一旦我加入一些逻辑来发布正确的信息，我就可以让一切正常工作了。我把我的机器人扔在 Heroku 上，开始四处分享。以下是一些推文示例:

有了所有优秀的开源工具和 web 服务，您可以如此快速地构建这样的东西，这真是太疯狂了。试着自己在推特上发些东西吧！另外，在这里查看我剩下的代码[。](https://github.com/ckirksey3/hot-dog-bot)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)