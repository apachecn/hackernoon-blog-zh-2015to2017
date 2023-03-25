# 如果你想要一个成功的语音应用，实现 SSML

> 原文：<https://medium.com/hackernoon/if-you-want-a-winning-voice-app-implement-ssml-87fb43eec809>

![](img/175b82f99d21f35399ddadfa74e61e97.png)

我是一个 [Alexa 冠军](https://developer.amazon.com/alexa/champions/terren-peterson)，过去一年一直在平台上写技能。其中包括 2016 年[互联网语音挑战赛的获胜者。](https://developer.amazon.com/blogs/post/Tx3HFBBGGPKDBNP/Announcing-the-Winners-of-the-Internet-of-Voice-Challenge)这个博客提供了如何使用一种叫做 SSML 的音频标记语言来提高你的 Alexa 语音技能质量的想法。

# 获得高于 3%的回报

Alexa 技能的数量正在激增，但开发人员发现这些努力并没有像他们预期的那样建立活跃的用户社区。VoiceLabs 最近的一项研究发现，普通的语音应用程序只有 3%的用户在启用后的第二周恢复了他们的技能。这是一个糟糕的表现，并且突出了用户体验中缺少的东西。缺少的是开发者还没有使用 SSML——语音合成标记语言。掌握它将是构建优秀语音应用的基础。一个很好的比较是 CSS 在浏览器中呈现的应用程序。从网络应用中去掉这一点，你会使用户体验变得平淡无奇。

# 更多语音应用即将推出

Alexa 中的语音应用领域开始变得拥挤。如果 Alexa 技能的数量像 2016 年一样再增加 10 倍，这将变得更糟。类似的语音技能增长也将出现在 Google Home 平台上。加上微软和苹果的新产品，单一技能的品牌化挑战将充满挑战。它将不再足以让一些东西出版。与众不同的是出众的质量，这样用户才会使用你的应用。

![](img/481f7167771e5e6ed8beeeff54a1cec7.png)

# 少了什么？

特色。让技能易于发布的缺点是，它们中的许多功能有限，很少会吸引用户再次访问。当然，开发人员可以在一个小时内完成部署，这很好，但是达到可笑的开发时间是一个需要跟踪的成功指标。让我们利用这些易于使用的初学者工具包，然后向前推进并为技能添加功能。

如果我们与其他平台的性能进行比较，移动应用的保留率比当前一代语音技能高 3 到 4 倍。他们在开发模式以实现特性方面也领先十年。我预计，如果功能得到改善，语音应用将会关闭。

# 让我们开始构建更好的语音应用程序吧！

有很多方法可以开始构建更健壮的语音应用程序。先说这个[技术](https://hackernoon.com/tagged/technology)的实力是什么。音频通过高品质扬声器传输，让我们充分利用这一优势。 [Alexa](https://hackernoon.com/tagged/alexa) 的一个被忽视的方面是一种叫做 SSML 的标记语言，它能够完全控制音频渲染。如果你熟悉浏览器应用的 CSS，SSML 就相当于音频应用。想象一下，如果我们最喜欢的网站只呈现基本文本，用户体验会有多差。对于声音来说也是如此，这也是为什么学习 SSML 对进一步提升音频体验如此重要。

SSML 的权威指南在[亚马逊 Alexa 网站](https://developer.amazon.com/public/solutions/alexa/alexa-skills-kit/docs/speech-synthesis-markup-language-ssml-reference)上，在这篇文章中，我提供了一些关于如何开始的简单提示。当向 Alexa 发出请求时，为了使用 SSML，我们需要像这样扩展 outputSpeech 属性。

```
"outputSpeech": {
    "type": "SSML",
    "ssml": "<speak>This output speech uses SSML.</speak>"
}
```

## 添加暂停

SSML 属性使用类似于 HTML 的传统标记。这是另一个展示 SSML 属性的例子。

```
<speak>                                         
    <p>This is a paragraph. There will be a pause after this.</p>       
    <p>Followed by another paragraph.</p> 
</speak>
```

有些技能阅读大量文本，如果没有自然停顿，听起来就开始机械。在段落之间使用分隔符可以解决这个问题，并且

标记提供了一个短暂的停顿，在编写请求脚本时可以很容易地控制这个停顿。还有一些时候需要更长的休息时间，这可以通过设置定时休息时间来解决(代码如下)。这可能长达十秒钟。

```
<speak>                                         
    Okay, let's be mindful and take a deep breath.
    <break time="3s"/>
    Now don't we feel better?
</speak> 
```

## 添加音频

假设我们正在使用扬声器，有什么比添加声音或音乐更能改善用户体验呢？同样，这需要一些基本的 SSML。这是它的样子。

```
<speak>
    Welcome to Music Teacher.
    <audio src=\"https://s3.amazonaws.com/myskill/intro.mp3\" /> 
    You can play a song or I can teach you notes. Which do you want? 
</speak>
```

创建这些音频文件时，需要注意几个不同的教训。

*   音频剪辑需要是 MP3 格式( *MPEG 版本 2* )。
*   比特率应该是 *48 kbps。*
*   MP3 文件必须驻留在使用 HTTPS 的端点上。最简单的方法是通过 AWS S3 存储桶进行托管。
*   采样速率必须为 16000 赫兹。
*   音频文件不能超过九十(90)秒。

操纵这些设置的免费工具是 [Audacity](http://www.audacityteam.org/) 。

一个简单的开始方法是在开始技能时，在欢迎消息的开头添加一个简短的音频提示，如上例所示。在我的音乐教师技能中，我加入了一小段(3-5 秒)正在弹奏的钢琴键。录制、格式化并上传到 S3 并不需要花费太多时间，只需要一行代码。

## 覆盖默认语音

在 Alexa 中，还有其他标记来覆盖默认的音频体验。你猜猜当你发出这个回应时会发生什么？

```
<speak>
    Can you call me at 8675309?
</speak>
```

Alexa 的语音响应是“八百六十七万五千，三百零九。”如果我们试图拨打一个电话号码，这不是一个很好的用户体验。如果我们像这样用“say-as”标签给它添加标记。

```
<speak>
    <say-as interpret-as="digits">Can you call me at 8675309?</say-as>
</speak>
```

音频响应就是我们要找的“8675309”。

```
<speak>
    <say-as interpret-as=”date”>20170202</say-as>
</speak>
```

有大量的变化要覆盖，包括日期格式、数字、发音等。想到 SSML 的一个简单方法是它的样式，类似于浏览器的 CSS。

# 寻找什么是可能的？

查看涵盖最受欢迎技能的[技能排行榜](https://www.amazon.com/alexa-skills/b/ref=topnav_storetab_a2s?ie=UTF8&node=13727921011)的顶部。我估计其中 80%的人都在使用 SSML，他们是如何成为佼佼者的，远远超过 3%的用户正在回归。

![](img/875e51a579cd902030f6a843f40c925c.png)

例如，[的危险技能](https://www.jeopardy.com/games/j6-alexa)一直位于“最常用技能”类别以及“本周顾客最爱”的首位。显然，该节目有利于品牌识别，但他们将频道捆绑在一起的一个简单方法是加入与广播版本相同的音频提示(蜂鸣器&钟声)。如上所述，这只是几行代码和一些 MP3 录音。把这些拿走，它是相当平淡的内容，尽管品牌庞大，但没有从数百个其他琐事技能中区分出来。

![](img/0a94ce25adef48b21c389b2acd43949c.png)

如果你还没试过[韦恩调查技能](https://www.alexaskillstore.com/The-Wayne-Investigation/635)，今天就启用吧。它还使用了大量的 MP3 声音集成，用户体验非常棒。虽然它已经推出了六个月，但它仍然停留在基于用户体验的排行榜上，并获得了 4.5 星的评级。游戏玩法本身并不复杂，通过[互动游戏工具框架](https://github.com/alexa/interactive-adventure-game-tool)就可以使用类似的玩法。

最近推出的一款顶级应用是[今夜秀技巧](https://www.amazon.com/NBC-Digital-Media-Tonight-Show/dp/B01N1SVDX4)，也是类似的场景。不同于标准的 Alexa 语音朗读前一天晚上的独白，吉米·法伦的语音记录是通过使用如上所述的 SSML 的 MP3 文件嵌入的。用户体验+1！

## 独立开发者能做到这一点吗？

这不仅仅是为了娱乐和媒体，也可以由独立开发者来完成。我最近发表的一个技能是[吉他老师](https://www.amazon.com/dp/B01N805N3E/ref=sr_1_2?s=digital-skills&ie=UTF8&qid=1486507194&sr=1-2)。

![](img/faf14e4839cc157d340da2d79bdbcac4.png)

在研究如何弹奏吉他的其他技巧时，我注意到大多数技巧都是用 Alexa 的声音构建的。这就限制了使用语言来表达如何放置手指的功能。这是一个巨大的区别，包括一个实际的吉他演奏时的声音记录，让用户再次回来时记忆深刻。

# 摘要

让我们继续庆祝已经发布的 Alexa 技能的数量，但在最初的 MVP 之后，投资几个周期学习 SSML 并使用该平台的全部功能。才是冲出 3%暴跌的路径！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)