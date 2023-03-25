# 幂等性、API 和重试——天哪！

> 原文：<https://medium.com/hackernoon/idempotency-apis-and-retries-34b161f64cb4>

![](img/b52db113632a0dc757b35a83c889ab82.png)

在当今世界，应用程序分布在许多网络组件上是很常见的。无论这些组件是您自己的堆栈中的微服务还是第三方 SaaS API，依赖它们的组件都需要能够与它们对话。这通常是通过 API 客户机来完成的，通常只是一个简单的类，提供易于使用的方法来包装 HTTP 请求。

# 一个示例客户端

ButterCMS 是一个 SaaS API 与相关客户端的很好的例子。ButterCMS 是一个“内容[管理](https://hackernoon.com/tagged/management)系统即服务”——CMS 的[数据库](https://hackernoon.com/tagged/database)、逻辑和管理仪表板作为托管服务提供，其[内容通过 web API](https://buttercms.com/cms/) 提供。有了 Butter 的 [API-first CMS 和 content API](https://buttercms.com/cms) ，你可以通过它的 [API 客户端](https://github.com/buttercms)检索内容，并将其插入你的网站。在 C#中，API 方法可以通过一个[单个类](https://github.com/ButterCMS/buttercms-csharp/blob/master/ButterCMS/ButterCMSClient.cs)来调用。

让我们来看看这个类的结构。它有许多公共方法，通过私有的`Execute(string queryString)`和`ExecuteAsync(string queryString)`方法发送 API 请求。为了简单起见，我们只处理`Execute`方法和它的同步调用者。下面是一个公共方法，用于检索博客文章列表:

```
private string authToken; // Authorization token set in the ButterCMSClient constructor
private const string retrievePostsEndpoint = "v2/posts/{0}"; // Base URL for blog posts on the API

// ... Code excluded for brevity ...

public PostResponse RetrievePost(string postSlug)
{
    var queryString = new StringBuilder();
    queryString.Append(string.Format(retrievePostEndpoint, postSlug));
    queryString.Append("?");
    queryString.Append(authTokenParam);
    var postResponse = JsonConvert.DeserializeObject<PostResponse>(Execute(queryString.ToString()), serializerSettings);
    return postResponse;
}
```

简单明了。正如您所看到的，它接受一个`postSlug`参数(这只是标识我们想要加载的博客文章的唯一 URL 段)，将它组装到 ButterCMS 服务器上文章的 URL 中，并将其传递给`Execute(string queryString)`方法，该方法获得一个 JSON 响应并将其返回，以便封送到我们的`PostResponse`类中。然后，我们可以获取这些数据，并将其呈现在我们公共网站的页面模板中。

让我们更深入地了解一下`Execute`方法内部发生了什么:

```
private HttpClient httpClient; // System.Net.Http.HttpClient instance, set in the ButterCMSClient constructor

// ... Code excluded for brevity ...

private string Execute(string queryString)
{
    try
    {
        var response = httpClient.GetAsync(queryString).Result;
        if (response.IsSuccessStatusCode)
        {
            return response.Content.ReadAsStringAsync().Result;
        }
        if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
        {
            throw new InvalidKeyException("No valid API key provided.");
        }
        if (response.StatusCode >= System.Net.HttpStatusCode.InternalServerError)
        {
            throw new Exception("There is a problem with the ButterCMS service");
        }
    }
    catch (TaskCanceledException taskException)
    {
        if (!taskException.CancellationToken.IsCancellationReques‌​ted)
        {
            throw new Exception("Timeout expired trying to reach the ButterCMS service.");
        }
        throw taskException;
    }
    catch (HttpRequestException httpException)
    {
        throw httpException;
    }
    catch (Exception ex)
    {
        throw ex;
    }
    return string.Empty;
}
```

这个方法简单地向给定的 URL 发出一个 HTTP `GET`请求，并以字符串的形式返回响应体，调用者可以将其解析为 JSON、XML 等。它有一些内置的错误检查，用于在错误响应的情况下抛出异常。这可以防止调用方意外地试图将它们解析为合法数据。

这个 API 客户端完成了工作，但是您知道最好拥有什么吗？自动重试失败请求的能力。由于间歇性的连接问题，请求可能偶尔会失败。假设在我们发出请求时连接中断了，或者服务器收到了请求，但是在它完成发送响应之前连接被断开了。这些问题可能是间歇性的，只需重新发送请求就可以解决。当我们可以再次尝试并向用户显示他们想要的内容时，向用户显示错误页面是一种耻辱。

# 幂等性和安全性

让我们尝试在这个 API 客户机中实现自动重试功能。这里需要注意的是，对于像这样的客户端，这是相对简单的*——我们所要做的就是捕捉由`Execute`方法抛出的任何异常，并使用相同的参数再次调用它，直到有限的尝试次数(如果有持续的问题，我们不希望重试永远继续下去)。这是因为这个客户端只发出`GET`请求。`GET`请求，如果正确实现和使用，有一个重要的属性叫做*幂等性*。*

## 幂等性和安全性

幂等性听起来像是一个花哨的词，但它是一个简单的概念——多次执行相同的操作，而只在服务器上产生一次“副作用”的能力。副作用被定义为对服务器上持久数据的更改。正确实现的`GET`请求仅用于从服务器检索数据，从不修改数据，因此它们自然是幂等的。这是一个叫做*安全*的特例。安全方法是等幂的方法，因为它们不会产生任何副作用。HTTP `OPTIONS`和`HEAD`动词也有这个属性。

## 幂等性和不安全性

有两个 HTTP 动词是幂等的但不安全，即`PUT`和`DELETE`方法。也就是说，它们在第一次成功时会产生副作用，但在随后的请求中什么也不做。例如，如果我在`myrestapi.com/resources/{id}`调用资源上的`DELETE`，该 URL 上的资源将被删除。如果我再次调用它，什么也不会发生，因为那个资源已经不存在了。对于`PUT`也是一样——调用一次用一些新数据替换资源，然后再调用一次，什么都不会发生，因为现在你正在把它“更新”到已经存在的相同数据。

既然我们理解了幂等性，就很容易理解为什么简单的重试机制对所有类型的请求都不安全。每当我们在服务器上发出成功的非幂等请求，但是响应没有到达我们这里，一个“哑”重试机制将再次发送该请求。如果它不是等幂的，那可能是灾难性的(或者至少会导致一些愤怒的客户— *“我的信用卡被双重收费了，嗯？!"*)。

由于我们的示例 API 客户机实际上是只读的(只发出`GET`请求)，我们可以使用一个“哑”重试机制，简单地重新发送请求，直到一个请求成功或者我们超过了最大允许重试次数。为非幂等请求构造重试机制需要服务器的配合。也就是说，客户端为每个请求附加一个惟一的 ID(GUID/UUID 就足够了)。当服务器成功处理一个请求时，它保存 ID 和它想要发送回的响应的副本。如果该响应没有返回到客户端，客户端将再次发送请求，使用相同的 ID。服务器将识别 ID，跳过请求的实际处理，只发送回存储的响应。从客户端的角度来看，这使得所有请求都是有效的幂等的。虽然在客户机或服务器上实现这种机制并不特别复杂，但本文只是对幂等性和重试的介绍，因此我们将坚持使用更简单的情况，即我们的示例中的`GET`请求和“哑”重试。

# 实现自动重试

让我们回到代码上。我们需要“观察”`Execute`方法，这样如果它抛出异常，我们就可以重新执行它。这可以通过捕捉异常的简单包装方法来完成。首先，让我们将旧的`Execute`方法重命名为`ExecuteSingle`，以便更准确地表达其目的。

```
- private string Execute(string queryString)
+ private string ExecuteSingle(string queryString)
```

现在让我们构建我们的包装方法。我们将其命名为`Execute`，这样我们现有的公共方法将调用它，而不是我们刚刚重命名的函数。现在，我们只做一个简单的包装器，不添加任何功能:

```
private string Execute(string queryString)
{
    return ExecuteSingle(queryString);
}
```

API 客户端现在应该完全像以前一样工作，所以我们实际上还没有完成任何事情。让我们从编写一个简单的循环开始，在一定的次数内重试请求。为了在`ExecuteSingle`抛出异常的情况下“继续循环”,我们需要在循环中捕获这些异常。

```
private string Execute(string queryString)
{
    // maxRequestTries is a private class member set to 3 by default, 
    // optionally set via a constructor parameter (not shown)
    var remainingTries = maxRequestTries;  

    do 
    {
        --remainingTries;
        try 
        {
            return ExecuteSingle(queryString);
        }
        catch (Exception) 
        {

        }
    }
    while (remainingTries > 0)
}
```

如果请求成功，这段代码将通过`return`语句退出循环。如果`ExecuteSingle`抛出一个异常，它将被吞掉，循环将继续到`maxRequestTries`次。`do { ... } while ()`语法确保请求总是至少执行一次，即使`maxRequestTries`被错误配置并设置为类似于`0`或`-10`的值。

当然，这段代码有一个明显的问题——它包含了所有的异常。如果所有的请求都失败了，它只会返回一个`null`字符串。但是我们该如何处理呢？我们不能从`catch (Exception) { }`块内部抛出异常，否则执行将会脱离循环，这违背了整个方法的目的。我们应该在所有请求失败后抛出异常，并且只有当所有请求失败时才抛出异常。我们可以通过将它们聚集在一个`List<Exception>`中并在方法末尾抛出一个`AggregateException`来实现这一点。

```
private string Execute(string queryString)
{
    var remainingTries = maxRequestTries;  
    var exceptions = new List<Exception>();

    do 
    {
        --remainingTries;
        try 
        {
            return ExecuteSingle(queryString);
        }
        catch (Exception e) 
        {
            exceptions.Add(e);
        }
    }
    while (remainingTries > 0)

    throw new AggregateException(exceptions)
}
```

如果所有的请求都失败了，这个方法将抛出一个`AggregateException`,包含每个请求抛出的所有异常的列表。如果任何请求成功，将不会抛出异常，我们将只得到我们的响应字符串。这绝对够用。但是让我们把它变得更好一点——大多数重复的失败都是由一个持久的问题引起的，所以每个请求都会抛出完全相同的异常。如果我们所有的请求都抛出一个`InvalidKeyException`(当我们的 API auth 令牌无效时就会发生这种情况)，我们真的想要返回一个`AggregateException`，比如说，有 3 个相同的`InvalidKeyException`吗？只扔一个`InvalidKeyException`不是更符合人体工程学吗？为此，我们需要将异常列表中的任何重复项“折叠”成一个“代表性”异常。我们可以使用 Linq 的`Distinct`方法来做到这一点，但是默认情况下它不会折叠异常，因为它们是...良好的...*不同的*对象和`Distinct`会参照比较。我们可以使用它的重载，它接受一个自定义的`IEqualityComparer<T>`，我们可以用它来识别异常，这些异常可以被认为是重复的。下面是我们的实现:

```
private class ExceptionEqualityComparer : IEqualityComparer<Exception>
{
    public bool Equals(Exception e1, Exception e2)
    {
        if (e2 == null && e1 == null)
            return true;
        else if (e1 == null | e2 == null)
            return false;
        else if (e1.GetType().Name.Equals(e2.GetType().Name) && e1.Message.Equals(e2.Message))
            return true;
        else
            return false;
    }

    public int GetHashCode(Exception e)
    {
        return (e.GetType().Name + e.Message).GetHashCode();
    }
}
```

如果两个异常共享相同的类型和`Message`属性，这个相等比较器认为它们相等。就我们的目的而言，这是“重复”的一个足够好的定义。

现在，我们可以折叠由请求尝试引发的重复异常:

```
private string Execute(string queryString)
{
    var remainingTries = maxRequestTries;  
    var exceptions = new List<Exception>();

    do 
    {
        --remainingTries;
        try 
        {
            return ExecuteSingle(queryString);
        }
        catch (Exception e) 
        {
            exceptions.Add(e);
        }
    }
    while (remainingTries > 0)

    var uniqueExceptions = exceptions.Distinct(new ExceptionEqualityComparer());

    if (uniqueExceptions.Count()) == 1)
        throw uniqueExceptions.First();

    return new AggregateException("Could not process request", uniqueExceptions);
}
```

这更符合人体工程学。简而言之，我们只抛出由请求尝试产生的不同异常。如果只有一个，要么是因为我们只做了一次尝试，要么是因为多次尝试都因为同样的原因失败了，我们抛出这个异常。如果有多个异常，我们抛出一个`AggregateException`,每个类型/消息组合一个。

# 包扎

在 API 客户机上实现幂等请求的重试功能就是这么简单。即使对于非幂等请求，我们也可以在循环之前创建一个新的`Guid`,并在每次请求尝试中包含它。服务器将负责跟踪请求 id 和响应。

一定要看看 [ButterCMS](https://buttercms.com/) ，这是一个[托管的 API 优先 CMS 和内容 API](https://buttercms.com/cms/) ，让你使用任何编程语言构建 CMS 驱动的应用，包括 [Ruby](https://buttercms.com/ruby-cms) 、 [Rails](https://buttercms.com/rails-cms) 、 [Node.js](https://buttercms.com/nodejs-cms) 、[。网](https://buttercms.com/asp-net-cms)、[巨蟒](https://buttercms.com/python-cms)、[凤凰](https://buttercms.com/phoenix-cms)、[姜戈](https://buttercms.com/django-cms)、[烧瓶](https://buttercms.com/flask-cms)、[反应](https://buttercms.com/react-cms)、[有角](https://buttercms.com/angular-cms)、[围棋](https://buttercms.com/golang-cms)、 [PHP](https://buttercms.com/php-cms) 、[拉拉夫尔](https://buttercms.com/laravel-cms)、[仙丹](https://buttercms.com/elixir-cms)、[流星](https://buttercms.com/meteor-cms)。

我希望这篇教程对你有所帮助。愿你的 API 永远符合人体工程学，愿你的网站永远可靠。愿你永远不会向顾客重复收费。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)