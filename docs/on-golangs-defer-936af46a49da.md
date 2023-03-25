# 论戈朗的“推迟”关键词

> 原文：<https://medium.com/hackernoon/on-golangs-defer-936af46a49da>

[Golang](https://golang.org/) 有一个 [*defer* 关键字](https://blog.golang.org/defer-panic-and-recover)，它“推迟”一个函数的执行，直到周围的[函数](https://hackernoon.com/tagged/function)返回。多个*延迟*被堆叠[后进先出](https://en.wikipedia.org/wiki/Stack_%28abstract_data_type%29)以便最近的*延迟*红色功能首先运行。

让我们来看一个实践中的例子。

```
func writeLogs(c chan []string) {
  defer stats.Duration("write_logs_fn", time.Now()) // order: 4

  file, err := os.Create("request.log", filename)
  if err != nil {
    panic(err)
  }
  defer file.Close() // order: 3

  writer := csv.NewWriter(file)
  defer writer.Flush() // order: 2

  for line := range c {
    err := writer.Write(line)
    if err != nil {
      panic(err)
    }
    defer stats.Increment("wrote_line") // order: 1
  }
}
```

乍一看，你可能会问—

> 这与其他语言中的 [**finally** 关键字](https://docs.oracle.com/javase/tutorial/essential/exceptions/finally.html)有何不同？

从 [Java 文档](https://docs.oracle.com/javase/tutorial/essential/exceptions/finally.html)，

> 最后，不仅仅在异常处理上有用——它允许程序员避免清理代码被返回、继续或中断意外绕过。将清理代码放在 finally 块中始终是一个好的做法，即使没有预料到异常。

好了，让我们用 *finally* 来比较 *defer* 和下面的 [Java](https://hackernoon.com/tagged/java) 例子。

```
static void writeLogs(List<List<String>> lines) throws Exception {
  Date start = new Date();
  FileWriter writer = null;
  int lineCount = 0;

  try {
    writer = new FileWriter("request.log");
    // non-buffered input
    for (int i = 0; i < lines.size(); i++) {
      // naive CSV writer
      for (value : lines[i]) {
        writer.append(value);
        writer.append(",");
      }
      writer.append("\n");
      lineCount++;
    }
  } **finally {
    if (writer != null) {
      writer.flush();
      writer.close();
    }** **stats.increment("wrote_line", lineCount);
    stats.duration(start);
  }**
}
```

很快，你就能发现一些“问题”:

*   **相似关注点的分离。** *终于*把相似的顾虑分开。因为当你打开一个文件时，你不能声明你想要关闭它，你不能有效地提前计划；相反，你必须记住让它适应周围的环境。我数不清我见过多少由于分离相似的关注点而导致的程序员错误。
*   **中断。** *最后*需要立即的、排他的、中断的注意力，而*延迟*允许你提前计划，而不中断你通常的控制流程。在 Go 示例中，我们可以在读取每一行时很容易地推迟增加度量，而在 Java 中，推迟多个调用将需要更高级的控制流，比如队列，所以相反，我们必须通过聚合来重新组织我们的流。
*   **可读性。** *finally* 强制嵌套，这会损害整体可读性，而 *defer* 可以内嵌。注意到了*！= null* 检查、计数器，并将定义与声明分开。总的来说，这些特征对读者来说都没有意义，但是由于嵌套结构，他们不得不解析它。
*   命令式与陈述式。这可能不是你所期望的流向 Go 方面的一个点，但是总的来说， *defer* 更具有宣示性。你告诉计算机你想让*做* X，Y，Z 而不是*如何做*。

和大多数技术债务一样，我发现随着功能复杂性的增加，这些“陷阱”会变得更加明显。

## 结论

虽然不合常规，Go 的*延期*却是纯粹的聪明。它用一个简单、通用的控制流伪装了一个通常复杂的控制流，既有助于可读性，又保持了事物的声明性([两个互不相容的因素](http://www.sandimetz.com/blog/2016/1/20/the-wrong-abstraction))。这比传统的通过*最终*拆卸要好得多，你应该总是更喜欢它，而不是推出你自己的控制流。

如果你觉得这很有趣，可以考虑在 Twitter 上关注我，在那里我分享了我的围棋经验和其他经验。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿美族家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！