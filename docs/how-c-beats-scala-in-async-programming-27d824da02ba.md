# C#如何在异步编程中击败 Scala

> 原文：<https://medium.com/hackernoon/how-c-beats-scala-in-async-programming-27d824da02ba>

在我之前的[帖子](/@anicolaspp/implicit-conversions-in-scala-for-c-developers-92ea6c7902fa)中，我们解释了 Scala 编程语言的一些特性是如何与我们在 C#编程语言中发现的特性重叠的。但是*隐含*和*类型扩展*并不是两种语言共享的唯一特征。

## 演员模型

正如维基百科呈现的那样

> *计算机科学中的 actor 模型是一个并发计算的数学模型，它将“actor”视为并发计算的通用原语:为了响应它接收到的一条消息，一个 actor 可以做出本地决策，创建更多 actor，发送更多消息，并确定如何响应接收到的下一条消息。它既被用作理论上理解计算的框架，又被用作并发系统的几个实际实现的理论基础。*

在 Scala 世界中，演员是一个大问题，C#默认情况下确实缺乏这种模式，但是有了 Akka.NET 来扭转局面。因为 Actors 只是一种可以在任何语言中实现的模式，所以我们将跳过它，直接跳到每种语言中多线程的核心。

## 未来与任务

Scala 提出了未来的概念，它作为一个占位符，代表将来某个时刻可用的值。期货通常被定义为持有这些价值的通用占位符。让我们看看如何使用它们。

```
val aFuture = Future {
    1 to 1000000
}
```

这里， **aFuture** 保存序列的值，一旦它变得可用。它也是强类型的，所以我们可以用不同的语法来定义它，只是为了显示它的定义。它看起来像这样

```
val aFuture: Future[Seq[Int]] = Future {
    1 to 1000000
}
```

未来不会阻止当前线程上的执行，因此它将在不同的线程中异步执行。

假设我们有一个长任务要异步执行，比如从 web 下载一个文件，我们可以用 Futures 异步做这个操作。事情是这样的

```
val aFuture = Future {
    val file = downloadFileFromTheWeb()
    file
}
```

在这种情况下，我们只是开始下载文件，在此期间，我们仍然可以在未来的定义后做一些其他有趣的操作。但是当我们需要异步操作的结果时会发生什么呢？期货提供了一种机制来做到这一点，在我看来这是过去的事情，但让我们回顾一下。

```
case class File(content: String)object File{
  def apply(content: String) = new File(content)
}val aFuture = Future {
    val file = downloadFileFromTheWeb()
    file
}aFuture onSuccess  {
    case File(content) => println(content)
}aFuture onFailure {
      case error => println("Error Downloading from the web: " + error.printStackTrace())
    }
```

定义了 *onSuccess* 和 *onFailure* 函数，以便根据长时间计算的结果执行它们，然后我们可以相应地采取行动。然而，如果我们需要等待长时间操作的结果，事情会变得更奇怪。

```
val aFuture = Future {
    val file = downloadFileFromTheWeb()
    file
}val file = Await.result(aFuture, 0 nanos)
```

这里我们只是等待计算完成，然而，Future 对象对这个调用没有控制权，因为它是在执行上下文中调用的，而不是在操作本身中。

C#提供了一个更好的 API 来解决这些问题，尽管它以与 Scala 相同的方式对这些想法建模。然而。NET API 比较好用。

同样的问题看起来像这样

```
public class File
{
    public string Content { get; set; }
} var task = Task<File>.Factory.StartNew(() =>
                {
                    var file = downloadFileFromTheWeb(); return file;
                });
```

如果我们想等待这个任务的结果，我们只需要说

```
var file = task.Result;
Console.WriteLine(file.Content)
```

这将一直阻塞，直到任务以与 **Await.result** 相同的方式结束。然而，控制动作是由任务本身执行的，而不是由需要导入到我们的执行环境中的全局执行器上下文执行的。

C#对*回调*也有更好的支持。它们不再被称为*回调*，而是*任务继续*。让我们使用 C#延续实现我们在 Scala 中所做的。

```
var taks = Task<File>.Factory.StartNew(() =>
                {
                    var file = downloadFileFromTheWeb(); return file;
                })
                .ContinueWith(t =>
                    {
                        Console.WriteLine(t.Result);
                    }, TaskContinuationOptions.OnlyOnRanToCompletion);
```

这将与期货的 *onSuccess* 相同，但是， *ContinueWith* 方法也返回一个任务，因此它可以链接到其他操作。*任务延续选项*决定我们在什么情况下执行延续，因此不需要不同的回调。

正如我们所看到的，C#任务是执行长时间运行任务的一种更直观的方式，更好地支持解决任务完成后的问题。然而，还有另一个与链接任务相关的问题。

## C#适用模型

引入 async/await 模型是为了解决延续的问题，我真的相信 C#在这个领域比其他编程语言要先进得多。

让我们回顾一下下面的伪代码:

```
var taks = Task<File>.Factory.StartNew(() =>
                {
                    var file = downloadFileFromTheWeb();
                        // <-- here want to do some other async operation such as writing the file content to a database
                        // <-- then here we want to log to a file the db result
                    return file;
                })
```

我们可以通过这样做来实现这一目标

```
var taks = Task<File>.Factory.StartNew(() =>
    {
        var file = downloadFileFromTheWeb(); return file;
    })
     .ContinueWith(t =>
     {
            Task<string>.Factory.StartNew(() =>
            {
                string fileContent = t.Result.Content;
                 return saveToDb(fileContent);
              }).ContinueWith(x =>
                {
                    logToFile(x.Result);
                 }) ;
      }, TaskContinuationOptions.OnlyOnRanToCompletion);
```

注意延续的链接会变得多么复杂，在 Scala 中也会发生同样的事情，但是 C#有一个特性在这方面杀死了 Scala。最后一段代码可以使用 C#中的 async/await 模式以下面的方式重写

```
void main(...){
    var task = Do();

    RefreshUIOrDoSomethingElse();

    //if we need to do something with the file we do
    File file = task.Result; // or File file = await task;
} private static async Task<File> Do()
        {
            var file = await Task<File>.Factory.StartNew(downloadFileFromTheWeb); var messageFromDb = await saveToDbAsync(file.Content); await logToFileAsync(messageFromDb); return file;
        } static Task logToFileAsync(string s)
        {
            return Task.Factory.StartNew(()=>Console.WriteLine(s));   
        } private static List<string> db = new List<string>();        static Task<string> saveToDbAsync(string fileContent)
        {
            return Task<string>.Factory.StartNew(() =>
                {
                    db.Add(fileContent);
                    return fileContent;
                });
        }
```

现在，我们正在使用 C# async/await API，以便让代码尽可能的干净。让我们分析一下，以确保我们都在同一页上。

首先，我们调用方法 **Do()** ，它在 Scala 中基本上返回一个任务或未来，但有趣的部分是它内部发生了什么。最重要的部分是 **Do()** 不遮挡。

第二，在里面 **Do()** 我们正在异步下载文件，但是那是*可获得的*所以此刻我们调用

```
var file = await Task<File>.Factory.StartNew(downloadFileFromTheWeb);
```

控制流程返回到**主**功能。一旦文件下载完毕，系统选择一个可用的线程，并从原来的位置继续执行任务。是啊！所以该任务中下一个要执行的将是

```
var messageFromDb = await saveToDbAsync(file.Content);
```

但是那句话也是可接受的，所以控制权再次返回到 **main** 直到 DB 完成它的工作。当 DB 完成时，选择另一个线程(不必是以前的那个线程),任务恢复。同样的事情发生在

```
await logToFileAsync(messageFromDb);
```

当它完成时，任务再次恢复，然后文件被返回。

请注意， *await* 是强类型的，因此它的执行结果将是可唤醒任务的一般类型。举个例子，

```
static Task<string> saveToDbAsync(string fileContent){...}
```

返回一个**任务<字符串>所以如果我们*等待*这个任务，结果将是一个*字符串***

公平地说，Scala 可以使用 *map* 实现类似 async/await 的功能。让我们看一个例子。

```
aFuture onSuccess  {
      case File(content) => println(content)
}
```

前面的代码可以实现如下

```
aFuture.map(println)
```

让我们看一个我在网上找到的更复杂的例子

```
val firstLove = future {
      Thread.sleep(500)
      "i love you"
    } val thenBetray = firstLove map {
      case loveLetter => {
        Console.println(loveLetter)
        Thread.sleep(500)
        "not really"
      }
    } thenBetray onSuccess {
      case partingWords => Console.println(partingWords)
    }
```

我们可以看到 *map* 如何帮助链接未来，但是代码仍然会变得相当混乱。还有其他的 Scala 构造可以和 map*一起工作，但是它们不能和 C#解决这个问题的方式相提并论。*

## 结局

我们看到了 Scala 如何在不阻塞调用线程的情况下实现长时间运行的操作。我们还看到了如何通过使用 class **Task** 在 C#中实现同样的功能。我们了解了如何在 Scala 中使用回调实现 continuationss，以及如何在 C#中通过链接**任务**来实现 continuation。最终，我们看到了 C#如何通过使用 async/await 模式解决链接问题，以及它对开发人员来说是多么自然。

当谈到异步编程时，我希望这种比较有助于同步两个世界的开发人员。当有人谈论**未来**时，你可以*将*映射到**任务**或者反过来。

玩得开心，两种语言都很棒，我都喜欢。

*接下来阅读:*

[*面向 C#开发者的 Scala 中的隐式转换*](/@anicolaspp/implicit-conversions-in-scala-for-c-developers-92ea6c7902fa#.qyjnst1d5)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)、[赞/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！