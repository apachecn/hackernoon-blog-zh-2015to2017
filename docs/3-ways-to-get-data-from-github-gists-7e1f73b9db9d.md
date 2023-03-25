# 从 GitHub Gists 获取数据的 3 种方法

> 原文：<https://medium.com/hackernoon/3-ways-to-get-data-from-github-gists-7e1f73b9db9d>

*原载于* [*我的博客*](https://miguelpiedrafita.com/github-gists/)

![](img/da961351e641941bd37f88db93fa7184.png)

GitHub Gist Logo ([https://github.com/logos](https://github.com/logos))

在做我的副业项目( [UnMarkDocs](https://unmarkdocs.co/) 和 [Webslides](https://slides.zone/) )时，我必须从 GitHub Gists 获取数据。这里有三种方法:

# 1.JS 方式

这是官方的方法，也就是 GitHub 文档中提到的方法。它包括添加`.js`并将其包含在脚本标签中:

# 2.JSON 方式

将`.json`添加到 Gist URL 的末尾会返回如下内容:

这在使用 Gists 服务器端时非常有用

# 3.GitHub API

当然，您可以使用 GitHub API 来获取这些内容。根据 [API 文档](https://developer.github.com/v3/gists/#get-a-single-gist)，你必须向`https://api.github.com/gists/$GIST_ID`发出`GET`请求，其中 Gist ID 是出现在你的用户名之后的 URL 的一部分。

下面是一个回答示例:

# 关于作者

👋嗨！我是 Miguel Piedrafita，一个 15 岁的 PHP 工匠和开源爱好者。在推特上连接[怎么样？](https://twitter.com/m1guelpf)

*原载于*[*dev . to*](https://dev.to/m1guelpf/3-ways-to-get-data-from-github-gists-9bg)*。*