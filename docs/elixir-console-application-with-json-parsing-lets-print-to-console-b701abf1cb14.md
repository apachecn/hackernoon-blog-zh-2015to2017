# 带有 JSON 解析的 Elixir 控制台应用程序。让我们打印到控制台！

> 原文：<https://medium.com/hackernoon/elixir-console-application-with-json-parsing-lets-print-to-console-b701abf1cb14>

## 使用 HTTPoison、Poison 和 Escript 进行维基百科搜索

朋友们好。我一直在玩 JSON 解析，并在 Elixir 中从收到的地图中提取信息。我想我应该写一下，我也认为我们可以一起写一个非常简单的基于控制台的维基百科搜索应用程序。我们将在这个应用程序中使用几个十六进制软件包:

## http poison

*“HTTP 客户端为仙丹，基于*[*HTTP 药水*](https://github.com/myfreeweb/httpotion)*”* 我们需要一个 HTTP 客户端来连接维基百科的 web API。

## 毒药

“一个令人难以置信的快速、纯粹的 JSON 库”
一旦我们从维基百科的 web API 收集了 JSON 数据，我们就需要解析它。

## 描述

"生成可从命令行调用的可执行文件。escript 可以在任何安装了 Erlang 的机器上运行，默认情况下不需要安装 Elixir，因为 Elixir 是作为 escript 的一部分嵌入的。
这样我们就可以像运行常规命令行应用程序一样运行我们的应用程序。

好吧，让我们开始吧！

像往常一样，我们将从创建一个新项目开始。我将把我的应用程序称为“维基梦”:

```
**~/**$ mix new wiki_dream
* creating README.md
* creating .gitignore
* creating mix.exs
```

我们将 cd 放入应用程序，并将依赖项添加到 mix.exs 文件中。因为 HTTPoison 运行在不同的进程上，所以我们需要将它添加到我们的应用程序函数中，以便它在我们启动应用程序时启动:

```
# mix.exs
......def application do
  [applications: [:logger, :httpoison]]
enddefp deps do
  [{:httpoison, “~> 0.9.0”},
  {:poison, “~> 2.0”}]
end
```

我们将获取并安装我们添加的依赖项:

```
**~/wiki_dream**$ mix deps.get
Running dependency resolution
Dependency resolution completed
certifi: 0.4.0
hackney: 1.6.1
......
```

现在，让我们也像这样设置我们的文件夹结构:

```
wiki_dream/ 
- lib/
  — wiki_dream/
    - cli.ex
  - wiki_dream.ex
```

如果你看上面的结构，你还会注意到我添加了一个新文件，cli.ex. Perfect，都设置好了。我们开始工作吧。

## 我们的应用程序将如何运行:

*   从我们的控制台处理给定的参数
*   从维基百科获取数据
*   解析提取的数据
*   从数据中提取我们想要的信息
*   把数据打印到我们的控制台

# 从我们的控制台处理给定的参数

我们的 cli.ex 文件将处理来自控制台的输入。如果您还不知道，CLI 代表“命令行界面”，是基于控制台的应用程序的前端。因为我们正在用 escript 构建一个可执行的应用程序，所以我们的第一个函数将是 main/1。这是 escript 所要求的:

```
# lib/wiki_dream/cli.exdefmodule WikiDream.CLI do
  def main(args) do
    parse_args(args)
    |> process
  end
end
```

我们还添加了一个函数 parse_args/1，这个函数我们还没有定义。该函数将调用内置的 [OptionParser 模块](http://elixir-lang.org/docs/stable/elixir/OptionParser.html)，该模块包含解析命令行选项的函数。process/1 函数将包含我们的应用程序的不同步骤:

```
# lib/wiki_dream/cli.ex 
......def parse_args(args) do
  parse = OptionParser.parse(args, switches: [help: :boolean], 
                             aliases: [h: :help]) case parse do
    {[help: true], _, _}
      -> :help
    {_, [search_term], _}
      -> {search_term}
  end
end
```

OptionParser 将接受我们给它的任何参数，并将其放入我们的 parse 变量。它还会寻找帮助或-h 标志。我们将添加一个 case 结构，如果给出了一个帮助标志，它将返回 atom :help，或者以一个元素元组的形式返回我们的搜索词。让我们看看是否能让我们的控制台打印一些东西。我们将编写一个非常简单的流程函数，看看它是否有效:

```
# lib/wiki_dream/cli.ex
......def process({search_term}) do
  IO.inspect search_term
end
```

现在，要将我们的应用程序作为可执行文件运行，我们需要用 escript 构建它。为此，我们需要将它添加到我们的 mix.exs 文件中，并赋予它我们的主模块(main/1 函数所在的模块):

```
# mix.exs
......def project do
  [app: :wiki_dream,
  version: “0.1.0”,
  elixir: “~> 1.3”,
  build_embedded: Mix.env == :prod,
  start_permanent: Mix.env == :prod,
  escript: [main_module: WikiDream.CLI], #Added escript
  deps: deps()]
end......
```

在我们的控制台中，我们将使用 mix 来构建它:

```
**~/wiki_dream**$ mix escript.build
===> Compiling idna
===> Compiling mimerl
......
Generated httpoison app
==> wiki_dream
Compiling 2 files (.ex)
Generated wiki_dream app
Generated escript wiki_dream with MIX_ENV=dev
```

让我们试着运行它，给它一个参数:

```
**~/wiki_dream**$ ./wiki_dream hello
“hello”
```

太棒了。有用！现在，如果我们给它一个帮助标志会发生什么？

```
**~/wiki_dream**$ ./wiki_dream --help
** (FunctionClauseError) no function clause matching in WikiDream.CLI.process/1
(wiki_dream) lib/wiki_dream/cli.ex:18: WikiDream.CLI.process(:help)
(elixir) lib/kernel/cli.ex:76: anonymous fn/3 in Kernel.CLI.exec_fun/2
```

它爆炸了。不出所料，因为我们还没有编写任何函数来处理它。让我们接下来这样做:

```
# lib/wiki_dream/cli.ex
......def process(:help) do
  IO.puts “””
  Wiki Dream
  — — — — — 
  usage: wiki_dream <search_term>
  example: wiki_dream lion
  “””
end
```

我们将再次构建我们的可执行文件，看看它是否有效:

```
**~/wiki_dream**$ ./wiki_dream --help
Wiki Dream
— — — — — 
usage: wiki_dream <search_term>
example: wiki_dream lion
```

厉害！我们已经得到了控制台参数解析工作！

# 从维基百科获取数据

现在我们将使用 HTTPoison。我们需要访问维基百科的 API 并获取一些 JSON 数据。如果我们给出“长生不老药”作为参数，我们将从 Wikipedia 的长生不老药文章中接收 JSON 数据。现在，维基百科不允许从他们的网页上抓取文章，所以从[https://en.wikipedia.org/wiki/E](https://en.wikipedia.org/wiki/Lion)lixir 那里获取信息是行不通的。但是，如果浏览他们的 API，

[https://en.wikipedia.org/w/api.php?format=json&action = query&prop = extracts&exin tro =&explain text =&titles =仙丹](https://en.wikipedia.org/w/api.php?format=json&action=query&prop=extracts&exintro=&explaintext=&titles=elixir)

维基百科会给我们一个 JSON 文件，里面有我们需要的所有东西。我们将创建一个新的文件和模块 json_fetch.ex，它将处理获取和解析接收到的 json。首先，我们将从 process/1 中移除 IO.inspect，并让它调用我们尚未编写的 fetch/1 函数:

```
# lib/wiki_dream/cli.ex
......def process({search_term}) do
  WikiDream.JSONFetch.fetch(search_term)
end
......
```

在我们新的 fetch_json.ex 文件中:

```
# lib/wiki_dream/fetch_json.exdefmodule WikiDream.JSONFetch do
  def fetch(search_term) do
    wiki_url(search_term)
    |> HTTPoison.get
    |> IO.inspect
  end defp wiki_url(search_term) do
    “[https://en.wikipedia.org/w/api.php? format=json&action=query&prop=extracts&exintro=&explaintext=&titles= #{search_term](https://en.wikipedia.org/w/api.php?format=json&action=query&prop=extracts&exintro=&explaintext=&titles=#{search_term)}"
  end
end
```

为了减少 fetch/1 函数的噪声，我添加了一个带有 Wikipedia API URL 的私有函数。请注意 URL 中的字符串插值。

然后，我们通过 HTTPoison 用管道将我们的 URL 与搜索词连接起来。我添加了一个 IO.inspect，这样我们可以在终端中看到输出。构建新的脚本并启动我们的应用程序:

```
**~/wiki_dream**$ ./wiki_dream elixir
{:ok, %HTTPoison.Response{body: “{\”batchcomplete\”:\”\”,\”query\”:{\”normalized\”:[{\”from\”:\”elixir\”,\”to\”:\”Elixir\”}],\”pages\”:{\”457424\”:{\”pageid\”:457424,\”ns\”:0,\”title\”:\”Elixir\”,\”extract\”:\”An elixir (from Arabic: \\u0627\\u0644\\u0625\\u06................
```

很好，我们收到噪音了！如果您查看输出，您会发现一个元组，它返回:ok(这很好！)，还有一大堆其他数据。(正文和页眉)。如果我们观察身体，我们可以看到维基百科关于长生不老药(一种液体，不是我们的语言)的文章中的字符串。

# 解析提取的数据

我们将使用我们安装的第二个依赖项 Poison 来处理和提取我们获取的数据体:

```
# lib/wiki_dream/json_fetch.ex
......def handle_json({:ok, %{status_code: 200, body: body}}) do
  {:ok, Poison.Parser.parse!(body)}
end
def handle_json({_, %{status_code: _, body: body}}) do
  IO.puts “Something went wrong. Please check your internet 
           connection”
end......
```

因此，我们有两个 handle_json/1 函数，一个用 atom :ok 处理一个元组，用 status_code: 200 处理一个 map，另一个处理所有其他的事情。我们的第一个 handle_json/1 函数将返回一个带有:ok 的 tuple 和一个带有主体的 map。另一个将返回我们的错误。

让我们将 handle_json/1 添加到 fetch/1 函数中:

```
# lib/wiki_dream/json_fetch.exdef fetch(search_term) do
  wiki_url(search_term)
  |> HTTPoison.get
  |> handle_json
  |> IO.inspect
end......
```

构建脚本，并运行应用程序:

```
**~/wiki_dream**$ ./wiki_dream elixir
{:ok,
%{“batchcomplete” => “”,
“query” => %{“normalized” => [%{“from” => “elixir”, “to” => “Elixir”}],
“pages” => %{“457424” => %{“extract” => “An elixir (from Arabic: الإكسير — al-’iksīr) is a clear, sweet-flavored liquid used for medicinal purposes, to be taken orally and intended to cure one’s illness. When used as a pharmaceutical preparation, an elixir contains at least one active ingredient designed to be taken orally.”,“ns” => 0, “pageid” => 457424, “title” => “Elixir”}}}}}
```

太好了。一切都如预期的那样。我们收到了一个带有:ok 的元组和一个带有主体的地图。现在，继续删除 fetch/1，IO.inspect 中的行，因为我们不再需要它了。

# 从数据中提取我们想要的信息

现在，如果您看一看接收到的数据，您会注意到我们正在寻找的信息是键“extract”的值。我们需要在这张地图中找出路，并获取“extract”的值。

我们将从创建一个新文件 extract_map.ex 开始，该文件将包含我们将用作挖掘工具的函数:

```
# lib/wiki_dream/extract_map.exdefmodule WikiDream.ExtractMap do
  def extract_from_body(map) do
    {:ok, body} = map
    IO.inspect body
  end
end
```

在这里，我们很好地利用了模式匹配。如前所述，我们的数据现在是一个带有:ok 原子和映射的元组。我们对元组和主体进行模式匹配，并将主体存储在名为 body 的变量中。我们将添加一个 IO.inspect 来查看数据输出。
我们还需要将这个函数添加到我们的 process/1 函数中:

```
# lib/wiki_dream/cli.ex
......def process({search_term}) do
  WikiDream.JSONFetch.fetch(search_term)
  |> WikiDream.ExtractMap.extract_from_body
end......
```

我们将构建并运行我们的应用程序:

```
**~/wiki_dream**$ ./wiki_dream elixir
%{"batchcomplete" => "",
"query" => %{"normalized" => [%{"from" => "elixir", "to" => "Elixir"}],
"pages" => %{"457424" => %{"extract" => "An elixir (from Arabic: الإكسير - al-'iksīr) is a clear, sweet-flavored liquid used for medicinal purposes, to be taken orally and intended to cure one's illness. When used as a pharmaceutical preparation, an elixir contains at least one active ingredient designed to be taken orally.", "ns" => 0, "pageid" => 457424, "title" => "Elixir"}}}}
```

酷毙了。元组不见了，因为我们要求只检查其中的映射。现在，如果你用不同的参数运行应用程序，你会注意到除了页码(整数串)之外的每个键都是静态的，固定的，不会改变。我们可以使用一个内置的 Elixir 函数(get_in/2)来为我们做一些繁重的工作。

> [**get_in(data，keys)**](http://elixir-lang.org/docs/master/elixir/Kernel.html#get_in/2)*从嵌套结构中获取值
> / Elixir documentation*

我们需要深入挖掘“查询”、“页数”、“页码”和“摘录”。前两个键应该很容易，因为它们不会改变。
让我们试一试:

```
# lib/wiki_dream/extract_map.ex
......def extract_from_body(map) do
  {:ok, body} = map extract_article = get_in(body, ["query"])
  |> get_in(["pages"])
  IO.inspect extract_article
end......
```

因此，我们从“查询”键获得值，这使我们能够访问“页面”键。让我们看看“pages”键里面有什么:

```
**~/wiki_dream**$ ./wiki_dream elixir
%{“457424” => %{“extract” => “An elixir (from Arabic: الإكسير — al-’iksīr) is a clear, sweet-flavored liquid used for medicinal purposes, to be taken orally and intended to cure one’s illness. When used as a pharmaceutical preparation, an elixir contains at least one active ingredient designed to be taken orally.”,
“ns” => 0, “pageid” => 457424, “title” => “Elixir”}}
```

干得好。我们挖到了页码。我们离“提取”键越来越近了。现在，我们的下一个目标比之前的稍微复杂一点。关键字名称将根据文章的页码而变化。然而，键名总是一个整数，所以我们可以为此写一个函数。

> **枚举。** [**find(enumerable，default \\ nil，fun)**](http://elixir-lang.org/docs/stable/elixir/Enum.html#find/3)*返回 fun 返回真值的第一个项目。如果没有找到这样的项目，则返回默认的
> /酏剂文档*

我们将使用 Enum.find 返回第一个整数键。因为只有一个键，而且这个键是一个整数，我们应该可以更进一步:

```
# lib/wiki_dream/extract_map.exdef extract_from_body(map) do
  {:ok, body} = map extract_article = get_in(body, [“query”])
  |> get_in([“pages”])
  |> Enum.find(fn {key, _value} ->
       case Integer.parse(key) do
         :error -> false
         _ -> key
       end
     end)
  |> IO.inspect
end
```

> **整数。** [**parse(binary，base \ \ 10)**](http://elixir-lang.org/docs/stable/elixir/Integer.html#parse/2)*解析一个整数的文本表示
> / Elixir 文档*

所以我们使用 Integer.parse 来查找一个整数的文本表示的键。如果存在，我们返回密钥，如果不存在，我们返回 false。

如果我们运行我们的程序，我们将收到一个元组，其中页码作为第一个元素，其余的映射作为第二个元素。我们不再关心页码，所以我们将做更多的模式匹配来获得“extract”键。然后我们将使用 Map.fetch！/2 从“extract”键获取值:

> **地图。** [**取回来！(map，key)**](http://elixir-lang.org/docs/stable/elixir/Map.html#fetch!/2)*获取特定键
> /药剂文档*的值

```
# lib/wiki_dream/extract_map.exdef extract_from_body(map) do
  {:ok, body} = map extract_article = get_in(body, [“query”])
  |> get_in([“pages”])
  |> Enum.find(fn {key, _value} ->
       case Integer.parse(key) do
         :error -> false
         _ -> key
       end
     end) {_, extract_article_content} = extract_article
  Map.fetch!(extract_article_content, “extract”)
end
```

# 把数据打印到我们的控制台

我的朋友，我们已经走了很长一段路了。我们现在搜索数据，接收数据，解析数据，从数据中提取信息。现在，我们需要将数据打印到控制台。让我们从 IO.inspect 开始，看看我们得到了什么:

```
# lib/wiki_dream/cli.ex
......def process({search_term}) do
  WikiDream.JSONFetch.fetch(search_term)
  |> WikiDream.ExtractMap.extract_from_body
  |> IO.inspect
end......
```

构建并运行:

```
**~/wiki_dream**$ ./wiki_dream elixir
“An elixir (from Arabic: الإكسير — al-’iksīr) is a clear, sweet-flavored liquid used for medicinal purposes, to be taken orally and intended to cure one’s illness. When used as a pharmaceutical preparation, an elixir contains at least one active ingredient designed to be taken orally.”
```

我们输出文章内容的字符串。这正是我们想要的！既然是字符串，我们就真的不需要再用 IO.inspect 了。我们可以使用 IO.puts 来输出字符串。让我们也使用一些非常简单的字符串格式，在每个句点处换行:

> **弦。** [**replace(subject，pattern，replacement，options \ \[])**](http://elixir-lang.org/docs/stable/elixir/String.html#replace/4)*返回用 replacement
> /Elixir documentation*替换主题中出现的模式所创建的新字符串

```
# lib/wiki_dream/cli.ex
......def process({search_term}) do
  WikiDream.JSONFetch.fetch(search_term)
  |> WikiDream.ExtractMap.extract_from_body
  |> string_format
end......def string_format(string) do
  String.replace(string, “. “, “. \n”)
  |> IO.puts
end......
```

构建并运行:

```
**~/wiki_dream**$ ./wiki_dream elixir
An elixir (from Arabic: الإكسير — al-’iksīr) is a clear, sweet-flavored liquid used for medicinal purposes, to be taken orally and intended to cure one’s illness.
When used as a pharmaceutical preparation, an elixir contains at least one active ingredient designed to be taken orally.
```

仅此而已。一个非常无用的 JSON 解析、地图挖掘 CLI 应用程序！

当然，还可以添加其他字符串格式来使应用程序看起来更好，但是这超出了本文的范围。

可能有更简单或不同的方法来做到这一点。然而，我发现这种方法对我很有效。我还认为它清楚地展示了函数式编程是如何工作的。其中不同的函数一个接一个地修改数据，直到得到想要的结果。我们没有改变我们开始时的原始数据，我们只是拿走它，复制它，沿途修改它，最后得到完全不同的东西。

Escript 可执行文件的酷之处在于，整个应用程序及其所有依赖项和代码都在这个文件中。如果您将它移到应用程序文件夹之外并运行它，它仍然可以工作。您甚至可以在不同的计算机上运行它，只要该计算机安装了 Erlang。

目前就这些。

下次再见斯蒂芬·巴克伦德·瓦卢瓦

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！