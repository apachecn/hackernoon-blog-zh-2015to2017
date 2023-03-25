# "空接口有代码气味吗？"

> 原文：<https://medium.com/hackernoon/are-interfaces-code-smell-bd19abc266d3>

我是 StackOverflow 的[早期用户](https://stackoverflow.com/users/54937/sedat-kapanoglu)之一。回到 2011 年，我在那里问了一个[编程](https://hackernoon.com/tagged/programming)相关的问题。五年后，53 张赞成票和 9 颗星之后，今天，我注意到它因为“主要基于观点”而被关闭，并被提名删除。我从未被告知我创建的内容发生了什么变化。我相信它会永远存在于网络上。当然，我太天真了。

我非常喜欢《T4 》,我觉得它非常有价值。我也喜欢为它做贡献。我不喜欢那里的中庸文化，但我接受它的本来面目。不过，如果能提前通知我就好了。免费赠送我的作品并不意味着我不赋予它任何价值。如果它不遵守 StackOverflow 规则，也不意味着它没有价值。总之。

不希望那个问题不了了之。尽管它将被视为“主要基于观点”而被抛弃，但我认为这些观点是有价值的。我把问题挪到这里，以防丢了。我不会移动答案，因为我不知道他们的作者是否会同意。他们可能已经获得了 CC 许可，但这并不意味着他们对他们的作品发生的任何事情都没有意见。相反，我会提到一些想法。

问题是，正如你所猜测的那样，“[是空之介的代码气味吗？](https://stackoverflow.com/questions/7552677/are-empty-interfaces-code-smell)”。以下是全文:

> 我有一个函数，它返回相同类型的对象(查询结果)，但是没有共同的属性或方法。为了拥有一个通用类型，我使用了一个空接口作为返回类型，并在两者上“实现”了它。
> 
> 这听起来当然不对。我只能安慰自己，希望有一天这些类会有一些共同之处，我会把这种共同的逻辑转移到我的空界面上。然而我并不满意，我在考虑是否应该有两个不同的方法，有条件地调用 next。那会是更好的方法吗？
> 
> 我也听说了。NET Framework 使用空接口进行标记。
> 
> 我的问题是:一个空的界面是一个设计问题的强烈信号还是被广泛使用？
> 
> 对于那些感兴趣的人来说，我后来发现函数式语言中有区别的联合是我试图实现的完美解决方案。C#似乎对这个概念还不太友好。

至少可以说，这个问题的措辞不恰当。很暧昧。我需要回答几个后续问题来澄清我的问题。我收到了有价值的评论和回答。我也接受了一个提到“[标记接口](https://en.wikipedia.org/wiki/Marker_interface_pattern)的答案，因为这是关于这个主题的最详尽的观点，引用了文章和链接。

我已经接受了*的那个*空界面很久了，因为“标记界面”的人，是的，直到几个星期前。我灵光一现，在欣喜若狂的时候，我发现了我的代码的问题。是的，事实证明那个界面实际上是一种代码味道，我最终为它找到了正确的设计。

我先把问题说清楚。我试着举一个非常简单的例子:Twitter。我们正在用 C#从头开始写 Twitter！(我喜欢 C#)。我们在不同的项目中有 Web 和 DB 层。我们在数据库层有一个搜索查询功能:

```
IQueryResult Query(string input);
```

根据用户在搜索框中键入的内容，查询函数返回不同的 IQueryResult 实例。如果以“@”开头，则返回 UserProfileResult。如果是常规文本，则返回 SearchQueryResult。如果为空，则返回 ValidationErrorResult。

根据结果对象类型，我们在 Web 层的控制器做不同的事情。如果是 ValidationErrorResult，它只呈现一个标准的验证错误，不做任何其他事情。如果是 UserProfileResult，它会重定向到用户的个人资料。如果是 SearchQueryResult，它会重定向到搜索页面。

问题是这两种类型都没有共同的属性或功能。ValidationErrorResult 有 ErrorMessage，UserProfileResult 有 UserId，SearchQueryResult 有关键字。所以我以这个结尾:

```
interface IQueryResult
{
}
```

就是这样。这只是我想到的一个充满罪恶感的方法，因为我不敢拥有这个令人厌恶的东西:

```
object Query(string keyword);
```

我有一点点类型安全。我可以肯定，除了实现了 IQueryResult 的对象之外，查询不会返回任何东西。但最终 web 层代码变成了这样:

```
var result = db.Query(input);
if (result is ValidationErrorResult)
{
    var validationResult = (ValidationErrorResult)result;
    return RenderError();
}
if (result is SearchQueryResult)
{
    var searchResult = (SearchQueryResult)result;
    return Redirect("Search", new { q = searchResult.Keyword });
}
if (result is UserProfileResult)
{
    var profileResult = (UserProfileResult)result;
    return Redirect("Profile", new { user = profileResult.UserId });
}
```

但是你看，它们都是谎言。当然，查询方法可以开始返回一个 *new* IQueryResult 类型，而我不会处理它。我没有任何类型安全，只有对它的幻想。

我也被那些连续的类型检查和类型转换所困扰。你可以用一个“as”操作符和一个空检查来稍微优化它们，但是不管怎样，这是一个顺序检查。每次 Twitter 用户请求时，你都要做不必要的额外工作。这不是正确的面向对象编程，效率不高，速度不快，不够灵活，还容易出错。怎么了？

当时我想有这种错觉，因为数据库和 Web 层是相互隔离的。我不可能在 DB 层的 ValidationErrorResult 中实现“RenderError”。它无法访问 Web 所拥有的任何东西。我也不可能实现重定向。DB 不知道那些事。我不得不坚持那些丑陋的“是”检查。糟透了。

面向对象编程主要是关于虚拟调度。我们不应该通过查找东西来找到正确的代码。这就是为什么我们有像继承和多态这样漂亮的特性。否则何苦呢，对不对？

界面就像一个插座。只要它支持插座的标准，它就能运行你插入的任何东西。事实上，我需要的是 DB 层中的一个插座，以后我可以插入任何我想要的功能。大概是这样的:

```
interface IQueryResult
{
    void Visit(IQueryResultVisitor visitor);
}interface IQueryResultVisitor
{
    void VisitValidationError(ValidationErrorResult result);
    void VisitUserProfile(UserProfileResult result);
    void VisitSearchQuery(SearchQueryResult result);
}class UserProfileResult : IQueryResult
{
     void Visit(IQueryResultVisitor visitor)
     {
          visitor.VisitUserProfile(this);
     }
}class ValidationErrorResult : IQueryResult
{
    void Visit(IQueryResultVisitor visitor)
    {
        visitor.VisitValidationError(this);
    }
}class SearchQueryResult : IQueryResult
{
    void Visit(IQueryResultVisitor visitor)
    {
        visitor.VisitSearchQuery(this);
    }
}
```

最后我重新发明了“[访客模式](https://en.wikipedia.org/wiki/Visitor_pattern)”。找到了。

我当然知道访客模式。我在其他地方用过。但是我从来没有下意识地把它和我在这里遇到的问题联系起来。一个原因是我不知道我可以从这些空函数中得到一个返回值。然后我想起了对象也包含状态。所以我的控制器代码应该是这样的:

```
var result = db.Query(input);
var visitor = new QueryResultVisitor(this.Context);
result.Visit(visitor);
return visitor.Result;
```

我不需要接口签名中的结果。我只是在实现中需要它:

```
class QueryResultVisitor: IQueryResultVisitor
{
    ActionResult **Result** { get; private set; } private IControllerContext context; QueryResultVisitor(IControllerContext context)
    {
        this.context = context;
    } void VisitValidationError(ValidationErrorResult result)
    {
         this.Result = context.RenderError(result.Message);
    } void VisitUserProfile(UserProfileResult result)
    {
         this.Result = context.Redirect("Profile", new { user = result.UserId });
    } void VisitSearchQuery(SearchQueryResult result)
    {
         this.Result = context.Redirect("Search", new { q = result.Keyword });
    }
}
```

它远没有看上去那么笨重。很明显，它和看起来一样多。但这不是一个扼杀情绪的平凡工作。尤其是当您考虑实现时的好处时:您的 DB 类突然开始支持具有不同行为模式的不同客户端。您可以获得编译时检查的好处，当您添加新的结果类型时，编译时检查会强制您实现缺少的部分。您的控制器代码被简化为几行。这是一种快速、可扩展的映射操作。

更重要的是，你笨拙的空界面突然变得有功能了。耶！房间里的大象永远消失了。

为了完整起见，我们可以用函数式编程语言(如 Haskell 或 F#)解决有区别的联合的相同问题。我对语法很生疏，但它可以归结为这个看起来像 F#的代码:

```
type QueryResult = 
| ValidationError of string 
| SearchQuery of string 
| UserProfile of intlet Query text : QueryResult = ... query code ...
```

我们的控制器代码变成这样:

```
let result = db.Query input
match result with
| ValidationError v -> RenderError v
| SearchQuery s -> Redirect "Search" dict["q", s]
| UserProfile p -> Redirect "Profile" dict["user", p]
```

它包含的样板文件少得多，易于理解，并且提供了更好的编译时检查。与命令式语言中的“switch”语句不同，如果添加新的结果类型，编译将会因为未处理的情况而失败。不知道它是否会一样快，但肯定会涉及更少的布线。

我还不能让自己在生产中使用函数式语言，除了 XSLT 不算。这样的转变是一个相当大的飞跃，有很多问题。我至少很高兴我现在在命令式语言领域有了一个令人满意的解决方案来处理那些空接口。我终于可以结束我的问题，让它远离中庸之锤。如果你也喜欢，那就更好了！

TL；DR:如果你最终得到一个空的接口或者通用的返回类型，看看这是否是一个使用访问者模式的机会。

另外，虽然我这次是被版主触发的，但我正在考虑把我写的其他关于编程的材料从其他平台转移到 Medium，这些平台有类似的扩展评论。如果这听起来是个好主意，请告诉我！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！