# 使用 Dart 在短短一秒钟内完成端到端测试

> 原文：<https://medium.com/hackernoon/end-to-end-testing-in-one-short-second-with-dart-e699c8146fd6>

构建良好的端到端测试很难。拥有良好的端到端测试是非常好的。你如何从前者进入后者？

上周我介绍了一个用 [Dart](https://hackernoon.com/tagged/dart) 编写的简单的[聊天服务器和客户端](/@davidmorgan_14314/building-a-chat-app-in-dart-815fcd0e5a31#.5p6514fje)。在编写代码的中途，我决定停下来添加端到端测试——这样它们可以帮助我取得更快的进展。这很有效，总体上节省了时间。

测试可能会耗费大量时间；但是我确信它会工作，因为我以前在类似的代码中使用过相同的模式。现在我在这里与你分享这种模式。

对于那些在家跟进的人来说，下面是如何获取和运行测试的方法。你需要 [git](https://git-scm.com/downloads) 和 [dart](https://www.dartlang.org/install) ，然后:

```
git clone [https://github.com/google/built_value.dart.git](https://github.com/google/built_value.dart.git)
cd built_value.dart
git checkout tags/v0.4.3-article
cd chat_example
pub get
pub run test
```

如果你运行它，你会发现它快得离谱*。在我的机器上，运行所有 13 个端到端测试总共需要大约一秒钟。此外，如果你检查其中一个测试，你会发现它们非常容易编写。这里有一个[测试](https://hackernoon.com/tagged/test)私有消息是私有的:*

```
group('tell', () {
  test('goes to a single user', () async {
    var alice = environment.newUser()..type('/login Alice letmein');
    var bob = environment.newUser()..type('/login Bob letmein');
    var eve = environment.newUser(); alice.type('/tell Bob Hi there.');
    bob.expectMatch(r'Alice \(private\): Hi there.');
    eve.expectNoMatch('Hi there.');
  });
});
```

它同时测试客户端代码(在浏览器中运行的代码)和服务器。我们创建了三个用户:Alice、Bob 和 Eve。爱丽丝和鲍勃登录，然后爱丽丝发送一个“告诉”，一个直接的消息，给鲍勃。如果 Bob 能看到消息而 Eve 看不到，则测试通过。

# 到处飞镖

如何测试聊天示例的关键是客户机代码和服务器代码都是用 Dart 编写的。这意味着完全有可能在服务器上运行客户机代码，或者在客户机上运行服务器代码——只有像“dart:io”这样的 VM 专用库和像“dart:html”这样的浏览器专用库除外。

所以我做的是*伪造出*客户端专用代码。这意味着“dart:html”支持的类被分成一个接口和单独的实现。例如，以下是 HTML 显示:

```
/// Chat window main text display.
abstract class Display {
  /// Adds [text] to the display, coloured to indicate a local   
  /// command.
  void addLocal(String text);   /// Adds [text] to the display.
  void add(String text);
}
```

然后，为了在 VM 中加载客户机代码，我提供了一个假的实现:

```
/// Fake [Display] that stores added text.
class FakeDisplay implements Display {
 List<String> text = <String>[]; @override
 void add(String text) {
   this.text.add(text);
 } @override
 void addLocal(String text) {}
}
```

现在，我可以在同一个虚拟机中运行服务器和客户端代码。到目前为止一切顺利。但是他们需要能够互相交流。

使用相同的方法；连接类被分成接口:

```
/// Two-way connection between client and server; the client.
abstract class ClientConnection {
  Stream<String> get dataFromServer; void sendToServer(String string);
}
```

然后为了测试，使用假的实现，把所有的东西连在一起。一个[测试环境](https://github.com/google/built_value.dart/blob/v0.4.3-article/chat_example/lib/testing/fake_environment.dart)维护服务器代码的一个实例，并创建连接到它的[测试用户](https://github.com/google/built_value.dart/blob/v0.4.3-article/chat_example/lib/testing/test_user.dart)。

仅此而已。现在，你可能会说这不是真正的端到端测试。为此，我们需要使用类似于[网络驱动](https://github.com/google/webdriver.dart)的东西。这样的测试有它们的位置，特别是在与复杂系统集成时。它们是运输坚如磐石的软件所必需的。但是它们不是提高开发速度的最佳方式。

为此，你需要快速、简单地编写测试，就像这些，只测试你正在处理的代码。对于某些代码，单元测试是理想的；但是要真正理解高层次的东西，你需要作为用户与整个应用程序交互的测试。它们是“端到端”的，但只针对您最关心的部分。

# 更多的测试

在撰写本文时，我注意到聊天代码中的一个 bug:并非所有命令都能在本地得到正确响应。也就是说，有些命令在输入后就“消失”了。这并不奇怪:我对此没有任何测试！所以我决定补充一些。

首先，“FakeDisplay”也需要记录本地回显的文本:

```
class FakeDisplay implements Display {
  List<String> text = <String>[];
  List<String> localText = <String>[]; @override
  void add(String text) {
    this.text.add(text);
  } @override
  void addLocal(String text) {
    this.localText.add(text);
  }
}
```

然后我们需要能够断言它，所以“TestUser”得到了一个新方法:

```
/// Checks local text for this user.
void expectLocalMatch(Pattern pattern) {
  expect(_display.localText, anyElement(matches(pattern)));
}
```

就这样，我准备添加这样的测试:

```
test('away echoes locally', () async {
  environment.newUser()
      ..type('/away Not here.')
      ..expectLocalMatch(r'/away Not here\.');
});
```

捕获并修复了错误。这是完整的[拉取请求](https://github.com/google/built_value.dart/pull/90/commits/68b8593d9a7e554e10aa7382d6de0c401ab0c61f)。我添加了七个新的端到端测试，使总数达到 20 个，并修复了两个 bug。

现在所有的测试都在 1.1 秒内完成。我认为这是可以接受的。

# 节日快乐

我现在已经深入地写了我在 Dart 峰会上的[演讲(视频)](https://www.youtube.com/watch?v=TMeJxWltoVo)中的所有内容。幸运的是，现在是休息的好时机。你可以期待我在 2017 年发表更多文章——只要我有新的东西可以写。同时，如果你有什么特别想让我报道的，请告诉我。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！