# 我是如何和为什么爱上仙丹的

> 原文：<https://medium.com/hackernoon/how-and-why-i-fell-in-love-with-elixir-3c547237cd4e>

## 我们短暂的爱情故事。

![](img/d0bd3c48b4e831777702685c4592cf28.png)

Elixir and the Phoenix Framework

*免责声明:这只是从我的角度讲故事，不应作为教育参考。另外，请记住，这是我第一次深入函数式编程的世界。我也不打算在这篇文章中讲得太专业，而只是提供一些简要的概述。*

大学毕业一个月后，我找到了第一份工作，成为一名初级后端开发人员。当时我唯一熟练掌握的编程语言是 PHP，也许还有一些 Java(哦，我多么讨厌它——个人言论，请不要介意)。在我第一天上班的前一天晚上，我真的在祈祷这家公司使用 PHP，这样我就可以很快适应了。你猜怎么着？我的祈祷没有得到回应。**他们在用仙丹！**

> 什么是[仙丹](https://hackernoon.com/tagged/elixir)？？— (Urag，2017 年)

# 接受

起初，我有点疯狂，因为我不能应用我在大学里学到的大部分东西，但我逐渐把它视为一个成长的机会。我意识到，如果我努力学习语言，我不会失去任何东西(*，但如果我不学，我会失去一些东西——这是我的工作*，所以我去设置我的机器。

# 恐怖

“我应该从哪里开始？”我去找了 Elixir 的[入门指南](https://elixir-lang.org/getting-started/introduction.html)，把它隐盖完成了。这是长生不老药第一次引起我的注意。他们的[入门指南](https://elixir-lang.org/getting-started/introduction.html)真的不友好。没有令人困惑的术语/术语。尽管《入门指南》足够好，可以帮助你入门，但我仍然建议你寻找其他参考资料。我上了这门**非常全面的**在线课程，名为《 [***仙丹与凤凰训练营***](https://www.udemy.com/the-complete-elixir-and-phoenix-bootcamp-and-tutorial/) 》，从未后悔过一毛钱。该课程涵盖了 Elixir 的所有基础部分，从用[Phoenix](https://hackernoon.com/tagged/phoenix)(Elixir 的 web 框架)到 Websockets 创建一个 CRUD 应用程序。我从作者本人那里得到了一张折扣券与你分享。链接将在这篇文章的结尾。我真的建议选那门课。

# 我喜欢什么

## 句法

Elixir 使用 def-do-end 来定义代码块，而不是 Java 和 PHP 中的花括号。寻找丢失的`end`有时比难以捉摸的`}`右括号更容易找到。你们中的一些人可能会惊讶于它看起来与 Ruby 的语法惊人地相似。别担心。灵药语言的创造者何塞·瓦里姆是一位著名的卢布主义者。

## 模式匹配

```
iex(1)> { a, b } = { "hello", "world" }
{ "hello", "world" }
iex(2)> a
"hello"
iex(3)> b
"world"
```

上面是一个在交互式 elixir shell 中执行的演示，可以通过在终端中键入`iex`来调用。这一开始非常令人困惑，因为它违反了你的一些*编程基础*。你在说什么，文斯？我说的是赋值运算符`=`。在 Elixir 中，赋值操作符被称为*匹配操作符*。让我通过提供一些片段来解释。

让我们以这段代码为例:

```
<?php$my_number = 1;
1 = $my_number;**FATAL ERROR** syntax error, unexpected '=' on line number 3
```

太荒谬了。为什么要把一个整数赋给一个变量？事情不是这样的！

如果在 Elixir 中执行相同的代码，将会发生以下情况:

```
iex(1)> my_number = 1
1
iex(2)> 1 = my_number
1
```

哇，成功了。嗯，在 Elixir 中，你不需要给变量赋值。你匹配他们。也许我需要抛出另一个片段，让它更清楚。

```
iex(3)> 2 = my_number
** (MatchError) no match of right hand side value: 1
```

那里发生了什么？我们之前已经将*与*我们的`my_number`匹配为 1。然后在第 3 行，我们试图让*将* 2 与已经有值 1 的`my_number`匹配，因此，它产生了一个`MatchError`。

## 管道操作员

```
["hello", "world"]
|> Enum.join(" ")
|> String.capitalize
```

这使得代码看起来更清晰；因此更容易调试。如果没有管道操作符，这将被写成`String.capitalize(Enum.join([“hello”, “world”], “ “))`。令人困惑。

## 凤凰框架

[凤凰框架](http://phoenixframework.org/)，根据他们网站的说法，是一个高效的网络框架，不牺牲速度和可维护性。一个简单的[谷歌搜索](https://www.google.com/search?q=elixir+web+framework)将向我们展示还有其他关于仙丹的网络框架。凤凰是最常用的。这就像 Ruby 的 Rails。它与 Ecto 捆绑在一起。Ecto 是用于与数据库交互的 DSL。Phoenix 还内置了对 websockets 的支持，使您能够轻松构建实时应用程序。如果你是一个 Ruby 迷，并且想开始过渡到 Elixir，Phoenix 在某种程度上类似于 Rails。甚至有人说是 Rails 克隆。顺便说一下，我以前尝试过学习 Ruby on Rails，我只能说 Phoenix 需要的学习曲线更少。感觉就是一边编码一边指导你。为了让您对 Phoenix 和 Ecto 有所了解，我将会转储一段代码，展示一篇具体的博客文章。

```
# router.ex
...
get "/:post_id", PostController, :show
...# post_controller.ex
...
def show(conn, %{"post_id" => post_id}) do
  post = BlogApp.Repo.get!(Post, post_id)
  render conn, "show.html", post: post  
end
...
```

如果仔细观察路由器，您会看到一个通配符。在 Phoenix 中，路由中的`:`表示路由通配符。我们还可以在示例中看到模式匹配的强大功能。它可以用来销毁复杂的数据。我们不是在函数定义中获取完整的参数映射，而是在接收到。

# 结局

我知道你们中一些长期使用 Elixir 和 Phoenix 框架的人会说我错过了 Elixir 最重要的特性，那就是并发性。老实说，我还没有探索过这个问题，正如我在免责声明中提到的，我希望这篇文章尽可能的简洁。

我从这次经历中学到的是永远不要满足。技术在不断发展，作为与技术一起工作的人，我们也必须发展。

## 在线课程折扣(如承诺的)

如前所述，[这里的](https://www.udemy.com/the-complete-elixir-and-phoenix-bootcamp-and-tutorial/?couponCode=4MORE1234)是 Stephen Grider 的 Udemy 课程的优惠券链接，“ [***完全仙丹和凤凰训练营***](https://www.udemy.com/the-complete-elixir-and-phoenix-bootcamp-and-tutorial/?couponCode=4MORE1234) ”。如果你不能点击链接，推广代码是:`4MORE1234`谢谢斯蒂芬，你是一个如此慷慨的小伙子。干杯。

感谢您的阅读。如果有任何问题，可以随时在 Twitter 上跟我说[@ Vince rug](https://twitter.com/VinceUrag)

喜欢你读的东西吗？你可以随时给我买杯咖啡。❤

在 Github 上与我联系:

[](https://github.com/vinceurag/) [## 文斯·乌拉格

### vinceurag 有 11 个可用的存储库。在 GitHub 上关注他们的代码。

github.com](https://github.com/vinceurag/)