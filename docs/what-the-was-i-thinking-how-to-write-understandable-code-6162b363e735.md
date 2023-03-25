# “我到底在想什么#@$%啊！?"如何编写可理解的代码

> 原文：<https://medium.com/hackernoon/what-the-was-i-thinking-how-to-write-understandable-code-6162b363e735>

![](img/cbeaf6e9dcdc34986fa10ea3ffe4008e.png)

我无法告诉你这篇文章的标题在我脑海中出现了多少次，因为我翻找了一段多年没碰过的[代码](https://hackernoon.com/tagged/code)。

在我写的时候，我可能认为我的代码很漂亮。优雅的杰作。它应该被打印出来，装上镜框，挂在[节目名人堂](https://hackernoon.com/tagged/programming)的墙上。尽管几年前我认为自己很聪明，但我很少能在不浪费大量时间调试的情况下阅读我的旧代码。

这个问题定期*困扰我*。我尝试了不同的技术，试图让我的代码更容易理解。

首先，我尝试在代码中添加注释。很容易做到，但不是所有的帮助。

当注释不起作用时，我试着编写自文档化的代码:小型的、命名良好的类和方法，描述它们有限的功能。这使得代码可读性更好，但我仍然有疑问。

我还尝试在一个单独的文件中记录我的代码。这有它的好处，但也没有完全解决问题。

最终我明白了我需要做什么:我需要使用以上所有三种技术来编写真正漂亮且易懂的代码。

## 评论

代码中的注释应该记录*为什么，*而不是*如何*。当我第一次开始编程时，我经常会写这样一些非常无益的评论:

```
public class Class1
{
  public List<string> DoWork(List<string> a)
  {
    List<string> numbers = new List<string>();

    // Loop over data
    for (int i = 1; i < a.Count; i++)
    {
      int s = a[i].IndexOf(" ");
      string num = a[i].Substring(0,s);

      // Save data
      numbers.Add(num);
    }

    return numbers;
  }
  ...
}
```

“数据循环”？“保存数据”？这些注释有助于理解代码。我可以很容易地判断出我有一个循环，并且我正在将我的数据添加到一个集合中，为什么我要用无用的注释浪费宝贵的屏幕空间呢？

与其说*什么*或者*如何*，评论应该解释*为什么*。程序员会看到`for`循环，并知道它在某种类型的`Addresses`集合上循环。然而，程序员不会知道为什么我们用`int i = 1`来启动我们的计数器——这就是添加注释可以提高代码理解的地方:

```
// i = 1 because the view will never display the first address
for (int i = 1; i < Addresses.Count; i++) {...
```

现在，我们知道了驱动我们应用程序的一些业务逻辑。我们知道我们不处理第一个地址，因为它永远不会输出到我们的视图中。这个评论回答了跳过第一个地址背后的 *why* ，增加了代码的清晰度。

此外，我们完全删除了`// Save data`注释，因为它没有增加任何有见地的价值。

## 自我记录

然而，单靠注释并不能使代码易于重新解释。让我们用改进后的注释再次看看我们的方法:

```
public class Class1
{
  public List<string> DoWork(List<string> a)
  {
    List<string> numbers = new List<string>();

    // i = 1 because the view will never display the first address
    for (int i = 1; i < a.Count; i++)
    {
      int s = a[i].IndexOf(" ");
      string num = a[i].Substring(0,s);

      numbers.Add(num);
    }

    return numbers;
  }
  ...
}
```

`Class1`到底是什么？`DoWork()`在做什么工作？`int s`的用途呢？我们代码中的对象名称并不能帮助我们理解这段代码在做什么。

这就是自文档化代码的概念的来源:我们不是用任意的、没有信息的名称创建对象(“我发誓我以后会重构它”)，而是构建描述性对象。如果我有一个类，它的名字应该能让我很好地了解它的属性和方法。一个方法的名字应该有足够的描述性，告诉我我应该期待什么样的输出，而不必深究该方法正在做什么的细节。变量应该添加额外的说明，使 *what* 和 *how* 类型注释过时。

在我们的例子中，让我们的代码自文档化。首先，这个类旨在帮助我们清理地址数据。姑且称之为`AddressStandardizer`。通过这个简单的重命名，我们知道这个类的所有属性和方法都应该与处理脏地址数据有关，并使其更干净。

方法名`List<string> DoWork(List<string> a)`怎么样？我可以告诉你，这个方法试图解析出街道地址的数字部分。所以让我们把方法名和签名改成更有信息性的，比如`List<string> ParseHouseNumbers(List<String> addresses)`。现在我们可以做出一个有根据的猜测，这个方法接受一些地址字符串作为输入，它将返回一个经过解析的门牌号列表。

如果我们清除一些变量名，我们的代码会变得更容易阅读，就像这样:

```
public class AddressStandardizer
{
  public List<string> DoWork(List<string> addresses)
  {
    List<string> houseNumbers = new List<string>();

    // i = 1 because the view will never display the first address
    for (int i = 1; i < addresses.Count; i++)
    {
      int firstSpaceIndex = addresses[i].IndexOf(" ");
      string houseNumber = addresses[i].Substring(0,firstSpaceIndex);

      houseNumbers.Add(houseNumber);
    }

    return houseNumbers;
  }
  ...
}
```

## 证明文件

我们的代码终于开始成形了。我们用注释解释了*为什么*我们选择做某事，我们重构了代码，使对象名具有信息性。

此时的代码还可以，但并不完美。如果我们几年不看这段代码，我们现在可能有足够的信息来看这段代码，并相对容易地弄清楚它在做什么。

然而，我们仍然缺少的一大块信息是，首先要知道为什么要写这段代码。

很多时候，我会从经理或分析师那里得到一个问题，关于为什么我们首先决定建立这个项目。或者我会收到一个关于程序中逻辑如何工作的信息请求。如果没有合适的文档文件，我能做的最好的事情就是给业务用户发送一份我的代码。大多数情况下，这并没有多大帮助。

不过，解释一下我们的程序在高层次上做了什么会有所帮助。这就是正式文件的目的。

这部分代码的文档可能如下所示:

> …在从我们的供应商处检索到我们的客户信息后，该程序会处理数据并对其进行清理，以加载到我们的报告仓库中。清理数据意味着将地址解析成多列，包括门牌号、街道名称、街道后缀、城市、州和邮政编码…

现在，当商业用户需要知道你的程序在做什么时，你可以很容易地把上面的文档发给他们。当需要重新阅读代码时，文档也可以作为程序员以及任何未来的项目新手的好帮手。

## 包裹

所有这些技术都是消除代码问题所必需的。从我的经验中吸取教训——不做这三件事可能会在短期内节省一点时间，但在将来的某个时候会有伤害。一旦你养成了写这三种文档的习惯，它将成为你的第二天性，并使你的生活(以及你未来的生活)成为你自己！)容易多了。

代码中的注释应该解释为什么*而不是如何:*

*   应该用命名良好的类和方法来解释*如何*
*   开发人员和非开发人员仍然需要单独的文档。想想那些需要知道您的流程如何工作以及流程中的业务逻辑的业务用户；很高兴有一个高层次的文档来解释您的流程的业务用途，非技术人员也能理解。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)