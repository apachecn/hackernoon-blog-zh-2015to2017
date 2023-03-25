# JavaScript APIs:电池

> 原文：<https://medium.com/hackernoon/javascript-apis-battery-c72baa74c203>

![](img/0ddfa42e98623547b528146159217e0c.png)

是的，你没听错，电池 API！随着网络的扩展，我们希望使用我们程序的设备数量也在增加。这个 API 对于保护我们的移动用户和他们宝贵的电池寿命特别有用！所以在我们看 API 之前，让我们想一想为什么我们可能需要这样一个东西的一些原因:

*   当电池电量低时，减少处理和请求。
*   向用户提供警告。如果您的用户正在处理对时间敏感的数据或操作，或者如果您知道您的应用程序很耗电，这一点尤其有用。
*   为网站提供一个更简单、低功耗的版本。

因此，让我们来看看您可能会如何使用它。

Getting battery info from navigator.getBattery

在上面的例子中需要注意一些事情:

*   `getBattery`并非在所有浏览器中都可用。确保在尝试使用它之前进行检查！
*   `getBattery`的返回值是一个用`BatteryManager`对象解析的`Promise`。
*   当`onchange`函数被调用时，你必须重新检查承诺提供的原始`BatteryManager`对象。它将被修改并包含新的电池信息。
*   当调用`onchange`函数时，它接收一个参数`event`，这个参数包含`event.type`，告诉我们发生了哪个电池事件。这允许您重用单个事件处理程序！

# 使用数据

既然我们已经了解了如何*获取*数据，那么让我们来看看如何*使用*数据！

在本例中，我们将观察电池电量是否变低，并在低于某一水平时显示警报。

我们正在检查的电池属性:

*   `dischargeTime`是电池耗尽前的时间，以秒为单位。如果设备插上电源，其值将为`infinity`。
*   `level`是百分比。所以`0.2`是 20%。

您可以根据自己的需要对这些进行定制。在对时间高度敏感的环境中，你可能想让他们提前 20%或 20 分钟知道。

最后一点:当电池电量低时，大多数设备都会警告用户，所以您可能不需要在应用程序中添加警告消息的例子。相反，您可以限制您的进程，并显示一条消息，说明在电池充满电之前，处理是有限的。请记住，这个 API 只是允许您为用户解决问题的另一个资源。

随便看看我过去的一些帖子:[控制台 API](https://hackernoon.com/javascript-apis-console-23ebce270419#.w6dps5pi1) ，[视频 API](/@justindanielfuller/javascript-apis-video-api-db803f9fd1b7#.7vgvrfhrj) 。

嗨，我是贾斯汀·富勒。很高兴你看了我的帖子！我需要让你知道，我在这里写的一切都是我自己的观点，并不打算以任何方式代表我的雇主*。所有代码样本都是我自己的，与美国银行的代码完全无关。*

我也很乐意收到您的来信，请随时通过 [LinkedIn](https://www.linkedin.com/in/justin-fuller-8726b2b1/) 、 [Github](https://github.com/justindfuller) 或 [Medium](/@justindanielfuller) 与我联系。再次感谢阅读！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！