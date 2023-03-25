# 满足调查 j

> 原文：<https://medium.com/hackernoon/meet-surveyjs-d8ac6a61db62>

*麻省理工学院许可下功能丰富的开源调查 JavaScript 库*

在网上创建调查有大量的解决方案。尽管如此，SurveyJS 团队坚信 SurveyJS 是正确的选择。请花几分钟时间了解为什么 SurveyJS 是正确的选择。

# 将调查整合到您的网页中

SurveyJS 库有几个流行的 JavaScript 框架的版本: **angular** ， **jQuery** ， **knockout** ， **react** 和 **vue** 。请按照[这些简单的步骤](https://surveyjs.io/Documentation/Library/?id=LibraryGettingStarted)将调查整合到您的网站或网页中。

“product feedback” example ( knockout version )

# 十几种问题类型

SurveyJS 库有十几个内置的问题类型，像[一个简单的文本](https://surveyjs.io/Examples/Library/?id=questiontype-text)或者一个具有不同输入类型的[复杂矩阵](https://surveyjs.io/Examples/Library/?id=questiontype-matrixdynamic)，还有一个[面板(容器)](https://surveyjs.io/Examples/Library/?id=questiontype-panel)。我们有您可能需要的所有问题类型。

jquery example

# 集成您最喜欢的 JavaScript 小部件

不管一个调查库有多少问题类型，总会遗漏一些东西。我们看到的所有调查解决方案都只允许使用内置问题和功能。SurveyJS 为您提供了一种集成任何 JavaScript 小部件的简单方法。网络上有成千上万种不同的 JavaScript 小部件。绝对大多数，你可以使用我们的[自定义部件支持功能](https://surveyjs.io/Examples/Library/?id=custom-widget-select2-tagbox)集成到 SurveyJS 中。它给图书馆带来了真正无限的功能。

nouislider widget

# 设计和引导支持

所有专业和商业调查解决方案都有漂亮的设计。有时您可能会选择不同的外观和感觉。大多数商业调查解决方案走得更远，例如，允许在您自己的调查中包含您自己的徽标。什么是大事！他们的设计和你的网站设计匹配吗？如果是的话，你可能会很幸运。不幸的是，他们没有办法为所有的顾客创造一个设计。SurveyJS 允许你以你需要的方式定制每一个元素。此外，它支持引导。如果你的网站使用 bootstrap，只需告诉 SurveyJS 使用你的 bootstrap 样式来渲染自己。

# 降价支持

这是另一个开放透明的解决方案击败旧的封闭系统的好例子。SurveyJS 支持降价。然而，我们不只是创建另一个降价库；我们让你能够使用任何你想要的降价。当然，我们可以再花几个月的时间来解决所有问题。我们确信，最终，我们会有一个好的我们自己的 markdown 库。但是，为什么要做别人已经做过的事情呢？在商业和麻省理工学院许可下，有几个很好的 markdown 库，你可以用 5-10 行代码集成到 SurveyJS 中，[只需查看代码](https://surveyjs.io/Examples/Library/?id=survey-markdown-radiogroup)。

markdown by [http://showdownjs.github.io](http://showdownjs.github.io)

# 将调查模型表示为 JSON

您知道典型的调查解决方案将您的调查模型存储在哪里吗？可惜是反问句。你可能只是不知道。SurveyJS 在任何方面都是完全透明的，包括这一点。SurveyJS 库使用 JSON 加载调查模型。您可以使用 [WYSIWYG SurveyJS Builder](https://surveyjs.io/Survey/Builder/) 来创建调查，然后将创建的 JSON 直接复制到您的网页中，以将其提供给 SurveyJS 库。当然，您可以在 [SurveyJS 服务](https://surveyjs.io/Service/MySurveys/)或您自己的存储器中存储并加载 SurveyJS JSON 模型。Survey JSON 是可读的，您可以很容易地自己修改它或简单地将其复制到 [SurveyJS Builder](https://surveyjs.io/Survey/Builder/) 中，并随时进行所需的更改。最后，没有什么可以阻止您在运行时在代码中创建或修改 SurveyJS 模型。最后，它只是 JavaScript 对象。

# JSON 形式的调查结果

可以想象，SurveyJS 结果也是一个 JSON。您可以在任何时候获得 JSON 结果，例如:调查完成时、页面更改时或值更改时。您可以将先前[输入的结果或预定义的数据](https://surveyjs.io/Examples/Library/?id=survey-data)设置到测量中。当然，您也可以随时获取或设置单个问题的值。

# 调查流程

最复杂的调查需要改变调查流程。您必须更改问题、面板和页面的可见性或跳过一些页面。SurveyJS 为你提供了一个简单的方法来实现这个目标。

flow example

# 动态问题

在许多情况下，调查问题模型必须是动态的。数据应该来自不同的服务，不能是静态的。SurveyJS 让您能够[从 restful API](https://surveyjs.io/Examples/Library/?id=questiontype-dropdownrestfull) 加载数据，而无需编写一行代码。当然，你可以自由地改变代码中的调查模式，这又给了你一个无限的可能性。

choose values from api

# 本地化和多语言支持

我们的母语不是英语，当然，我们完全理解产品本地化的重要性。像许多其他库一样，我们提供 SurveyJS 字符串到许多语言的翻译。它是由社区创建和支持的。然而，在一次调查中多语言支持如何呢？如果你不得不进行同样的调查，例如，对你说英语和西班牙语的客户，会发生什么？您应该创建两个不同的调查吗？SurveyJS 有[对多语言的内置支持](https://surveyjs.io/Examples/Library/?id=survey-multilanguages)。一些你在大多数调查解决方案中不太可能找到的东西。

localization

# 了解更多信息

我们可能会继续讨论 SureyJS 库的功能，如[显示模式](https://surveyjs.io/Examples/Library/?id=survey-displaymode)(用于查看结果)[、答案验证](https://surveyjs.io/Examples/Library/?id=validators-standard)、[文本预处理](https://surveyjs.io/Examples/Library/?id=survey-processtext)、[自定义导航](https://surveyjs.io/Examples/Library/?id=survey-customnavigation)等等。你可以看看[我们的例子](https://surveyjs.io/Examples/Library/)并自己探索。如果出于某种原因，您发现 SurveyJS 库缺少某个必需的功能，那么请在 SurveyJS GitHub repo 中创建一个问题，我们会尽力帮助您。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！