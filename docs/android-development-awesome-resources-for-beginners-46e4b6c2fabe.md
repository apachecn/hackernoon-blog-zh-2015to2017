# Android 开发:初学者的绝佳资源

> 原文：<https://medium.com/hackernoon/android-development-awesome-resources-for-beginners-46e4b6c2fabe>

![](img/58e5055fa0fa5ccffaac0ac350a8b1c8.png)

## 免费和付费

我最近[发布了一本书](https://gumroad.com/l/sghW)，直接面向想要开始 Android 开发的人。

发布后，我访问了 [r/learnprogramming](https://www.reddit.com/r/learnprogramming/) 并赠送了五本书，很快就有人感谢我的书，但也有人哀叹网上(应该)缺乏适合初学者的好资源。

我完全不同意:我认为网上有很多针对 Android 初学者的很棒的资源(有免费的也有付费的)，所以我答应列出我最喜欢的资源。

开始了。

# 自由的

## Java 语言(一种计算机语言，尤用于创建网站)

在你开始学习 Android SDK 之前，掌握 Java 编程语言的知识会有所帮助。如果你在 YouTube 上搜索“面向初学者的 Java”，你会得到*大量的*结果。

过滤这些可能是一件苦差事，所以让我来帮你:你想看的是

1.  德里克·巴纳斯的 [Java 编程](https://youtu.be/WPvGqX-TXP0)(又名在 30 分钟内学会 Java，这是一个很高的要求)
2.  约翰·珀塞尔的[面向完全初学者的 Java】](https://youtu.be/7WiPGP_0AUA?list=PL9DF6E4B45C36D411)

现在，我并不是建议你两个都要看，尽管如果你想看就看吧。它不会伤害你，除非你在看的时候被一个异形追赶。那样的话，肯定会痛。

如果你已经有了一些其他编程语言的经验，可以看看 Derek Banas 的 30 分钟视频*。该视频是为已经有一些条件语句，循环等经验的人准备的。在其他语言中。*

如果你以前从未做过任何编程，请观看 John Purcell 的系列视频。它会非常详细地解释一切。

还有一门 Java 课程是你在 YouTube 上找不到的。为了这个，你必须去 [Udacity](https://medium.com/u/2929690a28fb?source=post_page-----46e4b6c2fabe--------------------------------) 的网站注册才能观看[Java 编程简介。](https://classroom.udacity.com/courses/cs046/lessons/176475143/concepts/1784228310923)

最后，WikiBooks 上有一个很棒的 Java 指南，我不能推荐更多。

现在，你不需要学习 Java 的所有知识就能创建 Android 应用。我建议你关注以下主题

*   基本语法
*   流程控制(条件语句、for 和 while 循环)
*   面向对象编程的基础(类、对象、方法、继承和多态)
*   Java 类型系统
*   仿制药！仿制药！仿制药！和 Java 集合框架。列表、集合和地图需要成为你的朋友。如此多的应用程序通过向用户呈现数据集来工作，以至于你会经常使用集合
*   接口
*   异常和 try/catch 块
*   线程和可运行程序的基础知识

这些应该够你用了。

[](https://www.buymeacoffee.com/XozUExS) [## 给安东尼斯·查加利斯买杯咖啡——BuyMeACoffee.com

### 我是一名 Android 开发人员和设计师，热爱漂亮的用户界面！

www.buymeacoffee.com](https://www.buymeacoffee.com/XozUExS) 

## 机器人

学习 Java 只是创建 Android 应用的第一步。然后你必须熟悉 Android SDK ( **S** 软件 **D** 开发 **K** it)。

当开发 Android 应用程序时，你将主要使用 Java 和 XML。我说主要是因为 Gradle build 系统用的是 Groovy，NDK(原生开发包)用的是 C/C++，AIDL(安卓界面定义语言)用的是……什么的。

问题是，如果你是一个初学者(或曾经是初学者), NDK 或 AIDL 对你没有任何用处，Groovy 知识也不是必需的，因为你将使用 Gradle 做的事情主要是在一个名为 build.gradle 的文件中复制和粘贴代码行

XML(可扩展标记语言)是一种*标记*语言。这意味着它是用来描述事物，而不是定义它们是如何工作的。如果你曾经做过网络编程(甚至是基本的东西)，你一定用过 HTML。在 HTML 中，当你想在屏幕上显示一个段落时，你可以使用一个段落元素，就像这样

```
<p>This is a paragraph</p>
```

这只是声明性的:你*向*展示了一些东西。如果你想根据一些条件或者从服务器上下载的东西来改变段落的内容，你可能会使用 JavaScript 或者 PHP。

XML 很像 HTML，因为你将使用它来创建用户界面:你将使用它来放置各种元素，如文本视图、图像视图和屏幕上的编辑文本。

XML 是一个庞大的主题，你可以在网上找到很多关于它的信息，包括前面提到的 WikiBooks 站点。然而，你只需要 XML 的一小部分就可以在 Android 应用上工作。

对于 Android 中 XML 的充分介绍，我的书*天才灵长类的 Android 开发*应该足够了。

我知道，我知道:“那是我见过的最无耻的插头。我要离开这个鬼地方！”。但是等等——如果我告诉你，只要在下面输入你的电子邮件地址，你就可以免费得到这本书的一大部分内容，那会怎么样呢？这个免费的例子包含了很多关于 XML 用法的信息，并且是专门针对 Android 开发的。不客气

YouTube 上有几个非常好的免费 Android 教程。再说一次，如果你只是通过搜索来寻找，你会被大量的选择轰炸，所以请允许我推荐几个好的

1.  新波士顿:这太棒了。非常翔实，有趣，解释清楚
2.  [slidenerd](https://youtu.be/hzrGAZnMOMQ?list=PLonJJ3BVjZW6hYgvtkaWvwAVvOFB7fkLa) :我的另一个英雄。一个庞大的教程播放列表，针对初学者。开始时你可能会有口音上的困难，但过一会儿你就会习惯了。我能描述的最好的口音是“阿诺德·施瓦辛格，如果他是印度人的话”。和技术坏蛋，如果那是一件事的话”。

你也可以通过查看他们的 [Android 初学者开发](https://www.udacity.com/course/android-development-for-beginners--ud837)课程和他们的 Android 基础系列来很好地利用 Udacity 帐户(希望你现在已经创建好了)，其中包括

*   [用户输入](https://www.udacity.com/course/android-basics-user-input--ud836)
*   [联网](https://www.udacity.com/course/android-basics-networking--ud843)
*   [数据存储](https://www.udacity.com/course/android-basics-data-storage--ud845)

最后，约翰·珀塞尔(好家伙！)在其网站 caveofprogramming.com 上为初学者提供了免费的 Android 课程，但是你必须创建一个账户才能观看。这是为展出的惊人之举付出的小小代价。

# 有报酬的

## Java 语言(一种计算机语言，尤用于创建网站)

我不会建议任何付费的 Java 资源，因为免费的资源应该已经覆盖了你。但是，如果你想学习更多关于 Java 的知识，Joshua Bloch 的[有效 Java 第二版](https://www.amazon.com/Effective-Java-2nd-Joshua-Bloch/dp/0321356683/ref=sr_1_1?ie=UTF8&qid=1489339176&sr=8-1&keywords=effective+java)是必不可少的。

此外，如果你想尽早养成良好的习惯，[头脑优先设计模式](https://www.amazon.com/Head-First-Design-Patterns-Brain-Friendly/dp/0596007124/ref=sr_1_1?ie=UTF8&qid=1489339681&sr=8-1&keywords=head+first+design+patterns)会教你一些 Java 编程中常用的非常有用的习惯用法。

## 机器人

有两个地方你可以找到物美价廉的内容:Udemy 和 Team Treehouse。

让我们从[团队树屋](https://teamtreehouse.com/home)开始:你必须为基本计划(包括很多)每月支付 25 美元的订阅费，但这绝对是值得的。

Team Treehouse 上的一个很棒的功能是跟踪:有一个**初学者**和一个**中级**跟踪，它将通过建议一个设定的顺序来指导你学习 Android 开发的过程。把它们想象成餐馆里的套餐，只是*没那么好吃，但更有营养。我怀念我作为营销人员的职业。*

> 另一项福利！如果您在 TeamTreehouse 创建了一个帐户，您还可以访问他们的 Java 教程！

与 YouTube 类似，如果你去 Udemy 搜索“android 开发”，你会得到很多建议，你不知道从哪里开始。所以，以真正的“我很棒”的方式，这里是我的建议

*   蒂姆·布查尔卡[精通用 Java 开发 Android N 应用](https://www.udemy.com/master-android-7-nougat-java-app-development-step-by-step/learn/v4/overview)
*   Matthew Penning 的[用 Java Essentials 开发 Android】](https://www.udemy.com/android-development-with-java-essentials/learn/v4/content)

> 小贴士:Udemy 上的一些课程花费很高，但是网站上经常有折扣。你也可以安装一个名为 [Honey](https://chrome.google.com/webstore/detail/honey/bmnlcjabgnpnenekpadlanbbkooimhnj) 的 Chrome 扩展，它会自动应用优惠券代码，给你当时最大的折扣。

当我发现 LearnByTheByte 关于 Android 开发的令人敬畏的四部分教程是从 Udemy(和他们自己的网站)上删除的时候，我很失望，因为那篇教程太棒了。至少我设法[在 YouTube](https://youtu.be/3BEco-vD0Jk?list=PLZon9z32m_Rh_UfPf1J9DnfEl2TGg2AEu) 上找到了这个，这是我猜测的事情(他们教程的第一部分)。

最后，我想推荐一些不会让初学者困惑的 Android 开发书籍

*   Barry Burd 的[面向傻瓜的 Android 应用开发一体机](http://Android Application Development All-in-One For Dummies)。巴里的写作风格友好、平易近人，我非常喜欢。
*   大书呆子牧场的 [Android 编程](https://www.amazon.com/Android-Programming-Ranch-Guide-Guides/dp/0134706056/ref=sr_1_1?ie=UTF8&qid=1489341899&sr=8-1&keywords=big+nerd+ranch+android)是一本非常酷的指南，很容易阅读，但在本书的后面会有相当深入的介绍

稍后，你可能会想看看[忙碌的编码者的 Android 开发指南](https://commonsware.com/Android/)，它庞大而详尽，我认为任何严肃的 Android 开发者都应该拥有它。

[Ian Clifton 的《Android 用户界面设计](https://www.amazon.com/Android-User-Interface-Design-Implementing/dp/0134191404/ref=asap_bc?ie=UTF8)》是另一本书，你以后应该看看。我的另一个最爱是一个 [ndroid 编程:挑战极限](https://www.amazon.com/Android-Programming-Pushing-Erik-Hellman/dp/1118717376/ref=asap_bc?ie=UTF8)，但是，再次声明，一旦你已经创建了几个应用程序，就把它加入书签并检查一下。

就是这样！如果你需要问我什么，请在下面评论。感谢阅读。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)