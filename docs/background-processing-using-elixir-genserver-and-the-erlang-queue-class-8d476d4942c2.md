# 使用 Elixir、GenServer 和 Erlang 队列模块进行后台处理

> 原文：<https://medium.com/hackernoon/background-processing-using-elixir-genserver-and-the-erlang-queue-class-8d476d4942c2>

我写 Ruby 代码已经很多年了，和我一样，你可能会使用 Redis 或 Sidekiq 等熟悉的工具来处理后台作业和数据。我想告诉你为什么有时候，纯粹的灵丹妙药足以完成任务。

这是我如何用 pure Elixir 和 Erlang :queue 类替换 Redis/Exq 的故事。

> 非常感谢[omgnering](https://www.youtube.com/channel/UCp01DFl8kp-239gW289C0ew)在 GenServer 上的视频。如果你理解 GenServer 有困难，这将是一个巨大的帮助。

我构建了[magnetismo](http://github.com/sergiotapia/magnetissimo)作为一个学习练习，以真正理解仙丹，并了解如何发布生产就绪代码。虽然 Github 上的版本现在可以工作，但它在我最初目标中列出的非常重要的方面有所欠缺。

*目标:*

*   *抓取种子和磁铁链接的多个索引网站。——***正在工作！**
*   *不拘泥于仪式的奔跑。不需要无意义的配置。—* **嗯……挺？**
*   *高性能，利用 Elixir 的 GenServer 和 Erlang 的 BEAM VM。—* **正在工作！**
*   *测试单元的正确性。—* **正在工作！**

人们运行这个项目并不容易，甚至开发人员都有疑问，而我想要零摩擦。

运行[核磁共振](https://github.com/sergiotapia/magnetissimo)的步骤越少，采用率越高。

我在 Erlang 的队列类和 Elixir 的 GenServer 中找到了我的解决方案。

![](img/4a73bfb114f67e9fe9572b2339173914.png)

这是迈向最终目标第一步。

我做的第一件事是制作爬虫，并为每个爬虫创建一个工人。全部由我的主管监督。

```
children = [
  # Start the Ecto repository
  supervisor(Magnetissimo.Repo, []),
  # Start the endpoint when the application starts
  supervisor(Magnetissimo.Endpoint, []),
  worker(Magnetissimo.Crawler.ThePirateBay, []),
  worker(Magnetissimo.Crawler.EZTV, []),
  worker(Magnetissimo.Crawler.LimeTorrents, []),
  worker(Magnetissimo.Crawler.Leetx, []),
  worker(Magnetissimo.Crawler.Demonoid, []),
]
```

每个爬虫实际上是一个 GenServer 实现。例如，这是爬虫的 PirateBay 版本。

```
defmodule Magnetissimo.Crawler.ThePirateBay do
  use GenServer
  alias Magnetissimo.Torrent
  alias Magnetissimo.Crawler.Helper

  def start_link do
    queue = initial_queue
    GenServer.start_link(__MODULE__, queue)
  end

  def init(queue) do
    schedule_work()
    {:ok, queue}
  end

  defp schedule_work do
    Process.send_after(self(), :work, 1 * 1 * 300) # 5 seconds
  end

  # Callbacks

  def handle_info(:work, queue) do
    case :queue.out(queue) do
      {{_value, item}, queue_2} ->
        queue = queue_2
        queue = process(item, queue)
      _ ->
        IO.puts "Queue is empty - restarting queue."
        queue = initial_queue
    end
    schedule_work()
    {:noreply, queue}
  end

  def process({:page_link, url}, queue) do
    IO.puts "Downloading page: #{url}"
    html_body = Helper.download(url)
    if html_body != nil do
      torrents = torrent_links(html_body)
      queue = Enum.reduce(torrents, queue, fn torrent, queue ->
        :queue.in({:torrent_link, torrent}, queue)
      end)
    end
    queue
  end

  def process({:torrent_link, url}, queue) do
    IO.puts "Downloading torrent: #{url}"
    html_body = Helper.download(url)
    if html_body != nil do
      torrent_struct = torrent_information(html_body)
      Torrent.save_torrent(torrent_struct)
    end
    queue
  end

  # Parser functions

  def initial_queue do
    urls = for i <- 1..6, j <- 1..50 do
      {:page_link, "https://thepiratebay.org/browse/#{i}00/#{j}/3"}
    end
    :queue.from_list(urls)
  end

  def torrent_links(html_body) do
    html_body
    |> Floki.find(".detName a")
    |> Floki.attribute("href")
    |> Enum.map(fn(url) -> "https://thepiratebay.org" <> url end)
  end

  def torrent_information(html_body) do
    name = html_body
      |> Floki.find("#title")
      |> Floki.text
      |> String.trim
      |> HtmlEntities.decode

    magnet = html_body
      |> Floki.find(".download a")
      |> Floki.attribute("href")
      |> Enum.filter(fn(url) -> String.starts_with?(url, "magnet:") end)
      |> Enum.at(0)

    size = html_body
      |> Floki.find("#detailsframe #details .col1 dd")
      |> Enum.at(2)
      |> Floki.text
      |> String.split(<<194, 160>>)
      |> Enum.at(2)
      |> String.replace("(", "")

    {seeders, _} = html_body
      |> Floki.find("#detailsframe #details .col2 dd")
      |> Enum.at(2)
      |> Floki.text
      |> Integer.parse

    {leechers, _} = html_body
      |> Floki.find("#detailsframe #details .col2 dd")
      |> Enum.at(3)
      |> Floki.text
      |> Integer.parse

    %{
      name: name,
      magnet: magnet,
      size: size,
      website_source: "thepiratebay",
      seeders: seeders,
      leechers: leechers
    }
  end
end
```

Initial_queue 是一个创建 Erlang :queue 对象的函数，该对象包含扩展的初始 URL，并链接到其他分页页面或单个 torrent 链接。

my :queue 中的每个元素都是一个元组，由两部分组成:

```
{:torrent_link, “some_url”}
{:page_link, "some_url"}
```

在上面的过程方法中使用函数模式匹配，我可以很容易地解析一个分页链接或者解析一个单独的 torrent 页面。

然后，schedule_work 函数调度下一个要处理的项目。

最终结果是代码更加内聚，间接性更低。现在向项目中添加一个新的爬虫要容易得多。也更容易知道到底在运行什么。更少的错误机会，更可预测的增长行为。这种方法的一个缺点是波动性。如果我的应用程序关闭，我将失去处理队列。但是对于这个特别的项目，我觉得这样很好。

一个潜在的升级将是从 handle_info 更改为异步 handle_call。

我的下一步是使用 [Distillery](https://github.com/bitwalker/distillery) 构建一个可部署的可执行文件，这样终端用户就可以运行它，并让[magnetismo](https://github.com/sergiotapia/magnetissimo)在本地主机上启动服务。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)