# 编写优秀 iOS 框架的技巧

> 原文：<https://medium.com/hackernoon/tips-for-writing-a-great-ios-framework-8cf3452f6c5d>

*本帖最初发表于*[*【SamJarman.co.nz/blog】*](http://www.samjarman.co.nz/blog/)

随着 Cocoapods 和即将到来的 Swift Package Manager 的兴起，许多开发人员正在尝试编写一个框架/库供其他开发人员使用(这很好，您应该这样做！).但是，写框架和写正规 app 代码的心态是不一样的，有几个小窍门和小技巧值得了解。

我从事[框架](https://github.com/carnivalmobile/carnival-ios-sdk)已经有一年多了，在这个过程中我学到了一些东西。

## 代码的命名空间

命名空间，即在我们的类名前面加几个字母的行为，是防止与其他代码冲突的一大进步。这通常是用作者或苹果的名字来完成的。例如，我可以给我的代码加上前缀 *SAJ* ，这样我就有 *SAJAppDelegate、SAJMyModel、SAJMyViewController* 作为类。对于框架，3 个字母的前缀是最好的(感谢那些在 twitter 上向我指出这一点的人！).

另一个人们经常忘记的地方是在你的分类方法中。我建议在所有的分类方法前加一个 3 个字母的前缀。

因此，如果您要通过返回最近的公共假日的类别向 NSDate 添加一个方法，您应该将其命名为类似于 *saj_nearestBankHoliday，*调用它类似于*ns date * easterFriday =[ns date saj _ nearestbank holiday]；*

## 使用可空性注释

对于使用 Objective-C 的开发人员来说，你可以做的一件事就是使用可空性注释，这样才能真正给 Swift 用户一些爱。这将允许 Swift 用户正确地知道属性、返回值和参数是否可以为空。

*@property(非原子、强、可空)NSString * firstName*

*-(void)greet person:(nonnull ns string *)person name {…}*

## 使用泛型

增强 Swift 开发人员体验的另一个好方法是使用泛型。这允许你指定一种类型的对象，这种类型的对象通常接受 NSObject 的任何 ol '子类。Swift 用户将获得一个真实的物体，而不是*【any object】*，他们不必施展才能正确使用。耶。

例如，您现在可以指定一个数组只包含字符串

*@property(非原子、强、非空)ns array<ns string *>* names；*

这将变成

Swift 中的*【ns string】*。

## 详细记录

我现在听起来可能像一张破唱片，但这是许多开发人员根本不会花时间去做的事情。写一些该死的评论。至少，您应该在所有的公共头文件中编写文档，如果所有的源代码都是公开的，那么应该在每个方法上编写文档。

要包含的内容:参数、返回值、警告、讨论、相关方法

额外提示:使用 AppleDoc 语法，CocoaPods 会自动把你放到 [CocoaDocs](http://cocoadocs.org/docsets/DogeKit/0.0.3/Classes/DogeKit.html) 上。

[NSHipster](http://nshipster.com/documentation/) 对此有一篇很好的文章。

## 永远不要假设你正在运行什么线程

长话短说，当你是一个框架时，你不能控制你的类或代码的实例化。很多开发者可能会选择在发布后在后台启动你的框架——那么这是否意味着你的应用会崩溃？假设它在主线程上，它会接触用户界面吗？一般来说，如果你的框架不接触 UI，它应该在后台线程上运行，只有在必要的时候才返回主线程，这样才不会降低主机应用程序的速度。做一个好公民。

[更多的帮助。](http://amattn.com/p/grand_central_dispatch_gcd_summary_syntax_best_practices.html)

## 考虑 Xcode 版本

当你是一个框架时，你有两个用户——应用程序的用户和代码的用户。他们的工具、设置和堆栈可能会影响框架的设计——所以要注意这一点。如果你依赖于新特性(比如 Xcode 新版本中的特性)，你可能会不太走运，因为很多开发人员并没有使用最新的特性。

不久前，我做了一个调查，写了关于 Xcode 版本的文章[，所以请阅读一下](http://www.samjarman.co.nz/blog/what-version-of-xcode-are-you-using/)。

## 启用位代码

启用位代码！！！说真的。**只有当所有包含的框架都支持位代码时，一个应用程序才能支持位代码。所以打开它，修复你需要的东西。你不想成为那种意味着应用程序不支持位代码的框架。这是一种很快被取代的方式。**

## 好的发行说明

似乎很明显，但这是许多开发人员忘记做的事情。保留一个公共的变更日志。如果你们都喜欢使用 Github，只需结合 git 标签使用发布页面。一个好的发行说明日志将帮助开发人员跟踪 bug 的进展，找到稳定的版本并保持更新。如果他们“关注”你的库，他们也会收到一封包含发布说明的电子邮件！

[示例](https://github.com/carnivalmobile/carnival-ios-sdk/releases)

原来如此。快速总结一些建议，打造更好的框架。一般来说，你需要更努力地思考，而不仅仅是用代码解决问题。**想想开发者体验(DX)。**

你有什么建议？在 twitter 上 Ping 我——我是带着你的想法的 [@samjarman](http://twitter.com/samjarman) ！我很想听听。

PPS: [迈克尔](https://twitter.com/MichaelCiurus)写过类似的文章。[看看吧！](http://yourcodesucksexception.blogspot.co.nz/2015/11/things-ive-learned-while-building-ios.html)

【SamJarman.co.nz/blog】本帖最初发表于[](http://www.samjarman.co.nz/blog/)

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *要了解更多信息，请阅读我们的“关于”页面、[在脸书](http://bit.ly/HackernoonFB)上给我们点赞/发消息，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*