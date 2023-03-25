# 三个字节和一个空格

> 原文：<https://medium.com/hackernoon/three-bytes-and-a-space-8f9fbd1c669b>

## 或者说，铁锈 bug，不合规，还有我是怎么学会爱上 IRC 的。

在过去的几周里，我一直在用 Rust 做实验，经常在我工作的间隙学习。在尝试了一段时间并熟悉了这种语言之后，我拼命地寻找一个项目。在我的搜索中，我想到了一个我们在工作场所使用的脚本，为了这个故事的目的，我将它称为`snap.py`。

`snap.py`是一个使用非常简单的 [Python](https://hackernoon.com/tagged/python) 脚本:访问几个带有表格的网页，检查其中的表格，并从每行的列中提取数据。它收集它们，记录它们，然后输出到终端。这似乎很简单。然而，它有几个问题让我不太满意:

*   由于 Python 的 [GIL](https://wiki.python.org/moin/GlobalInterpreterLock) ，没有真正的多线程
*   由于为每一行创建对象，导致内存占用
*   低内存系统上的颠簸
*   由于其工作负载，运行相当缓慢

坐在办公桌前，我心想 Rust 也许能解决许多问题，如果不是所有问题的话。如果它足够好，它甚至会吸引我开发团队中的前辈的目光。

因此，在我漫长而平静的工作转变中:

```
cargo new --bin snaprs
```

# 旅程开始了

我想我应该从分别使用`reqwest`和`kuchiki`下载数据和解析数据开始。我的脚本开始时相当简单:

```
extern crate kuchiki;
extern crate reqwest;use std::io::Read;
use kuchiki::parse_html;
use kuchiki::traits::*;
use reqwest::get;static URL: &’static str = “[http://internal.company.url/table1](http://internal.company.url/table1)";fn main() {

 let mut response = get(URL).unwrap();
 let mut body = String::new();
 response.read_to_string(&mut body).unwrap();}
```

我认为这是一个停下来编译的好地方，因为如果没有别的，我不可能把这个搞砸。

```
thread ‘main’ panicked at ‘called `Result::unwrap()` on an `Err` value: Http(Status)’, src/libcore/result.rs:799
```

![](img/d38911f54cdad88ef80f495af79ca237.png)

# 掉进兔子洞

我注释掉了`body`的行，删除了`response`上的`unwrap()`，并编写了一个快速的小`match`函数来输出我得到的错误消息。忽略警告消息:

```
$ cargo run
   Compiling snaprs v0.1.0 (file:///path/to/snaprs)
   Finished debug [unoptimized + debuginfo] target(s) in 2.27 secs
   Running `target/debug/snaprs`
 Invalid Status provided
```

根据`reqwest`的文档，通过扩展`hyper`，当 HTTP 客户端接收到非标准的 HTTP 状态代码时，会返回此错误消息。即使通过使用`reqwest::Client::new()`创建一个合适的客户端，甚至求助于使用`hyper`，我仍然会得到同样的错误。

作为健全性检查，我下降到`ipython`，导入`requests`，测试相同的 URL，它接收到一个 200，并且下载数据没有问题。打开开发者工具，在我的网络浏览器中加载 URL，我可以看到页面正在下载和渲染，同样是 200。最后，使用`curl`，我能够确认同一个页面的响应标题显示我收到了一个 200。只有生锈是我失败的原因。

沮丧和慌乱中，我上了 IRC，请求# rust-初学者的帮助。有一些基本的故障排除和解释我的情况，我不会让每个人都阅读。大多数故障诊断都是由我通过 chat 的指导完成的，因为它访问了我公司内部的一个 web 服务器。最终，我们开始让我使用`strace`测试，看看`snaprs`实际接收到了什么。借助一点帮助:

```
JoshTriplett - bushidoboy: So, the response you got seems completely reasonable; it has a valid status.JoshTriplett - bushidoboy: “HTTP/1.1 200\r\n”[Truncated]JoshTriplett - bushidoboy: So, the error from hyper means that it thought it got an invalid status code.JoshTriplett - bushidoboy: That error in turn gets set from httparse.bushidoboy - Yeah, I just don’t know why it looks at a 200 and thinks it’s an errorbushidoboy - Like, that’s the literal HTTP textbook definition of success
```

至少，当我知道我得到了 200 分时，我确实感觉好了一点。现在，剩下的就是找出*为什么*对客户来说不够好。发生了什么事？怎么回事？

```
JoshTriplett - httparse’s parse_code just reads three literal bytes and expects something in the range 000–999.JoshTriplett - Wait, no…JoshTriplett - Oh. JoshTriplett - Hang on, I think I might have figured it out.
```

我在座位的边缘。我已经下班很久了，一直在等着找出这一切的潜在问题。

我们终于来了。

就是这个。

```
JoshTriplett - bushidoboy: “200” is not the textbook definition of success. “200 OK” is. :)
```

…就这样？

```
JoshTriplett - bushidoboy: httparse chokes on the lack of a space after 200.JoshTriplett - bushidoboy: curl, on the other hand, puts up with it.JoshTriplett - bushidoboy: You’re not *required* to use the literal string “OK”, but you have to have a space and then a “reason”.JoshTriplett - bushidoboy: [https://www.w3.org/Protocols/rfc2616/rfc2616-sec6.html#sec6.1](https://www.w3.org/Protocols/rfc2616/rfc2616-sec6.html#sec6.1)JoshTriplett - bushidoboy: No provision for leaving out the last SP.* misdreavus *slow clap*
```

读着这些文字，我沉思着，这个简单的失败实际上代表了我到目前为止的生活。

![](img/3a50cc620df71f122a5ff699d906e992.png)

# 但是等等！还有呢！

虽然我仍然很沮丧，但我很高兴知道这不是我自己的错。但对我来说很奇怪的是，在我使用过的`reqwest`中，这是我第一次遇到非标准的错误代码。我测试过的开放互联网上的其他网站都没有这个问题。

然后我突然想到:

```
bushidoboy - Oh goodness.bushidoboy - You just reminded mebushidoboy - We run a custom server we forked.wyvern - yikes.
```

没错。我们内部开发的服务器返回不兼容的 HTTP 状态行。一天两个虫子。一个在`httparse`中，一个在我公司的定制网络服务器上。我不觉得自己很特别吗？

# rust-初学者的居民也非常同情我的困境。

```
misdreavus - this just keeps getting better and better* misdreavus reaches for the popcornJoshTriplett - httpopcorn* cmyr : refills popcornXion - HTTP POP /corn
```

# 结论

这就是我第一次真正报告 Rust 错误的故事的结尾。我已经提交了一个关于`httparse`回购的问题，所以希望这个问题能尽快解决。

*2016 年 12 月 4 日更新:* Sean 引入了一个 [commit](https://github.com/seanmonstar/httparse/pull/30) 来允许省略原因短语的 HTTP 状态——万岁！

![](img/3d180242e5a08ace3a2b8c56290cfef7.png)

我想向# rust-初学者中的人们致以最深切的感谢——特别是 JoshTriplett 和 misdreavus。在我使用 Rust 的整个过程中，这个社区给了我很大的帮助。

虽然这很令人沮丧，但我真的从中学到了很多，而且我觉得经历这些比没有经历更开心。要做的事情很多，要学的东西也很多。

```
bushidoboy - Well, I hope I gave everyone a good laugh and a read. I’m gonna go cry in a corner and mourn the death of this project
```

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)