# 使用 express 创建信使机器人

> 原文：<https://medium.com/hackernoon/creating-a-messenger-bot-with-express-134e7ec8ea07>

*最初发表在 diff 博客上，现在我忘记了之前放在媒体上，但是我希望它仍然有用。*

脸书最近宣布了为他们的信使平台创建[机器人的方案。](http://newsroom.fb.com/news/2016/04/messenger-platform-at-f8/)

> *每个月，全球超过 9 亿人通过 Messenger 与朋友、家人和超过 5000 万家企业交流。这是 iOS 上第二大最受欢迎的应用，也是 2015 年美国增长最快的应用。*

有一个接收和发送消息的应用编程接口，这样您就可以通过编程的方式与用户和客户交流，有几个步骤可以让它工作，所以我们建议从一个简单的应用程序开始。你作为一个页面而不是个人与用户互动，所以在这个过程中你会创建一个脸书应用程序和页面。

有一个[示例应用程序](https://github.com/dvidsilva/messenger-bot)有两个端点，一个用于接收，另一个用于发送消息。在这个应用程序中，发送者将收到它发送的相同消息。我们对两者使用相同的 url，当收到一条消息时，请求是 GET，发送一条新消息时，它将使用 POST 请求。

# 概观

1.  设置 Nodejs 应用程序。
2.  脸书不喜欢自签名证书。使用 LetsEncrypt 中的一个。
3.  按照 Facebook Messenger API 文档中提到的步骤创建所需的应用程序和页面。
4.  生成页面访问令牌。
5.  使用上一步生成的令牌，并将应用程序订阅到页面。
6.  你准备好了，机器人应该用发送的确切字符串来回应。
7.  其他步骤

# 设置 Nodejs 应用程序

最简单的方法是在数字海洋中创建一个液滴，在创建液滴时，您可以选择一个小实例，并单击应用程序列表上的 nodejs。您也可以通过点击[(这里是](https://dashboard.heroku.com/new?button-url=https%3A%2F%2Fgithub.com%2Fdvidsilva%2Fmessenger-bot&template=https%3A%2F%2Fgithub.com%2Fdvidsilva%2Fmessenger-bot))在 Heroku 上部署该应用程序，但是我还没有完全证明这一点，所以它可能需要额外的配置。

运行 nodejs 生产就绪应用程序对您来说应该是最简单的部分。克隆[样本应用程序](https://github.com/dvidsilva/messenger-bot)并使用 [pm2](http://pm2.keymetrics.io/) 运行。

您可以在[数字海洋指南](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-node-js-application-for-production-on-ubuntu-14-04)中找到更多信息。

应用程序需要有两个端点，一个 get 和一个 post。

脸书将在安装过程中使用 Get 来验证您是否拥有此端点。

每次用户向您发送消息时，都会使用 Post，并附带消息的有效负载。

要发送消息，您需要向脸书发送一个包含内容的 Post 请求。

验证端点如下所示:

```
if (req.query['hub.verify_token'] === conf.VERIFY_TOKEN)
  return res.send(req.query['hub.challenge']);
```

它确保脸书发送的令牌与您配置中的令牌相匹配，稍后您将对此进行设置。

# 从 LetsEncrypt 创建证书

[https://letsencrypt.org/](https://github.com/dvidsilva/nodeconnect-post/blob/master/LetsEncrypt)让我们为 ssl 创建一个证书。它是免费的、自动化的、开放的。这个证书对你的机器人来说已经足够好了。

要为此过程做准备，请为要使用的域或子域创建一个 A 记录。

克隆 LetsEncrypt repo

```
sudo git clone https://github.com/letsencrypt/letsencrypt /opt/letsencrypt
```

并生成证书

```
cd /opt/letsencrypt
./letsencrypt-auto certonly -a webroot --webroot-path=/usr/share/nginx/html -d your.domain.com
```

它会要求您输入一个电子邮件地址，以获取有关您的域和证书的信息，并同意服务条款。

使用 Webroot 插件 Webroot 插件通过在/中放置一个特殊的文件来工作。您的文档根目录中众所周知的目录，可以通过 Let's Encrypt 服务打开(通过您的 web 服务器)进行验证。根据您的配置，您可能需要显式允许访问/。知名目录。

通过将它添加到服务器块来修改您的 nginx 配置

```
sudo vi /etc/nginx/sites-available/defaultlisten 443 ssl;server_name  your.domain.com;ssl_certificate /etc/letsencrypt/live/your.domain.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/your.domain.com/privkey.pem;
```

你可以在数字海洋指南中找到更详细的步骤[。](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encryp%0At-on-ubuntu-14-04)

# 遵循 Facebook Messenger API 文档中提到的步骤

完整的脸书指南可以在[这里](https://developers.facebook.com/docs/messenger-platform/implementation)找到。

你需要有一个页面，你的用户将是一个页面，而不是一个人，所以创建一个新的页面。特别是对于您的测试尝试，最好使用不重要的内容，不要冒险使用重要的业务页面。在我的例子中，我创建了[信使回声机器人](https://www.facebook.com/Messenger-Echo-Bot-1747759535458893)

在 app configuration 上，您将生成一个令牌，将这个令牌添加到 [conf.js](https://github.com/dvidsilva/messenger-bot/blob/master/conf.js) 。

要生成令牌，请转到应用程序配置的产品设置，选择添加产品并找到 Messenger，它会让您选择您自己的页面并生成令牌。将此令牌作为配置中的值添加到`PROFILE_TOKEN`。

您可以通过应用程序设置或运行以下命令，使用此令牌将页面与应用程序相关联:

```
curl -ik  -X POST "https://graph.facebook.com/v2.6/me/subscribed_apps?access_token=<PROFILE_TOKEN>"
```

创建一个随机、安全的字符串，并将其添加到`VERIFY_TOKEN`。在应用程序配置中，添加 webhook 时会提示您输入此信息。你的节点应用程序现在应该可以工作了，因为脸书只会让你添加 webhook，如果这样的话

当你的应用处于开发模式时，插件和 API 功能只对应用的管理员、开发者和测试者有效。在你的应用程序被批准并公开后，它将为普通大众服务。

记得在编辑 conf.js 后重启你的应用，最快的方法是`pm2 restart all`，但是如果有更多的应用在那个服务器上运行，要小心。

# 尝试一下

转到您创建的页面并选择消息选项，页面将回复您发送的相同文本。

如果有错误，调试应该很容易，可能出错的地方很少。

跟踪应用程序的日志，看看向页面发送消息时会发生什么。

如果日志中什么都没有，可能是因为:用户没有关联到应用程序，因此没有触发挂钩，或者挂钩没有正确配置。

另一个常见错误是 OAuthException，这意味着`PROFILE_TOKEN`不正确。

如果你遇到其他你不能修复的错误，试着发表评论，开一张票或者联系我，我会试着看看。

# 其他步骤

要为用户准备好你的应用程序，你必须提供隐私政策、应用程序图标和关于你的机器人如何工作的视频。对于测试应用程序来说，工作量相当大，他们似乎很认真地对待这一点，并审查每一个提交的内容，所以请确保在申请前完成所有步骤。

如果这就是你的应用的全部功能，你的应用可能会被拒绝，所以试着添加一些聪明的笑话或者更多的信息。

您可以在脸书应用程序设置的应用程序查看部分找到详细说明。

希望这有助于你开始，让我们知道你有什么很酷的机器人！