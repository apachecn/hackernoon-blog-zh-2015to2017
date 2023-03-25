# Rails 5 Imgur 克隆教程…第 2 部分

> 原文：<https://medium.com/hackernoon/rails-5-imgur-clone-tutorial-part-2-29b02fadb254>

![](img/10f0204380183dc76e829c3a0391737f.png)

Rails 5 Imgur Clone

欢迎来到 Rails 5 Imgur 克隆的第二个也是最后一个部分，在这一部分中，我们将构建映像上传、管理配置，然后部署到 Heroku。

> 如果你还没有看过第一部分 [**这里**](/@priom/rails-5-imgur-clone-tutorial-part-1-8fa6e08d5d) 一定要看看。
> 
> 首先，注册并验证一个新的 cloudinary 帐户，方法是点击[这里](http://cloudinary.com/invites/lpov9zyyucivvxsnalc5/vvf3cm3nlh4z8lk5ndii)，这是我到 cloudinary 的推荐链接，会给我一点额外的空间。

让我们从在***config/initializer/cloudinary . Rb***向我们的 cloud inary 配置添加几个变量开始

记住，***cloudinary . Rb***必须在那个文件夹中，如果你打开***config/application . Rb***你会看到在注释中写着配置必须在那里，这就是 rails 配置的工作方式。

确保你的 ***cloudinary.rb* 看起来像这样。**

```
**Cloudinary**.**config do |*config*|
    *config***.**cloud_name = ENV**[**'CLOUDINARY_CLOUD_NAME'**]
    ***config***.**api_key = ENV**[**'CLOUDINARY_API_KEY'**]
    ***config***.**api_secret = ENV**[**'CLOUDINARY_API_SECRET'**]
    ***config***.**cdn_subdomain = true
    *config***.**enhance_image_tag = true
end**
```

转到 cloudinary dashboard，您将看到我们将使用的 ***云名称、API 密钥、API 秘密*** 变量。包含***cdn _ subdomain***通过获得最佳 cdn 位置来提高图像服务性能，并且***enhance _ image _ tag***为内置 rails***image _ tag***方法添加了更多功能。

> 好了，这里我们动态地为我们的应用程序设置环境变量。它将获得生产和开发环境的变量。你真的不想硬编码，因为公开你的密钥和其他信息是不安全和不明智的，因为这些都是高度敏感的。这种风格被强烈推荐给你的任何 rails 项目，而不仅仅是这个。这真的是一个很好的练习。你可能会认为这只是一个家庭项目，但如果你在一家公司工作，这真的很严重，所以，最好从现在开始练习。

其次，这是最重要的一步，在你的环境中设置变量。我们将使用 ***dotenv*** 宝石。将它添加到您的**gem 文件**中，并在您的终端中运行 ***包*** 来安装 gem，然后重启您的服务器。

```
gem 'dotenv-rails', groups: [:development, :test]
```

> 这是迄今为止最好、最容易和最简单的解决方案，效果非常好。

现在转到项目的根目录，创建一个名为 ***的文件。env***

我们要像这样设置变量。

```
export CLOUDINARY_CLOUD_NAME=
export CLOUDINARY_API_KEY=
export CLOUDINARY_API_SECRET=
```

只需按原样放置变量，不加任何引号。然后加上 ***。婀*** 到贵 ***。gitignore*** 这样它就不会被推送到你的 GitHub repo。我们将添加 Heroku cloudinary 附加组件，并按照他们的方式在生产环境中设置变量。

好了，我们的配置部分已经完成了。如果你想进一步了解 **dotenv** gem，那么去他们的 [github 页面](https://github.com/bkeepers/dotenv)阅读更多。

最后，我们将向控制器添加代码，用于将图像上传和保存到数据库。

到你的控制器***pics _ controller . Rb***并添加到你的 ***新的*** & ***像这样创建*** 方法。

```
**def *new* @pic = Pic**.**new
end

def *create* @pic = Pic**.**new**(**params**.**require**(**:pic**).**permit**(**:image**))
  **@pic**.**save
  redirect_to @pic
end**
```

我们在这里所做的基本上是创建一个名为@ ***pic*** 的**实例变量**，并将 ***新的*** 方法添加到我们的 ***Pic*** 模型中，我们将在整个项目中使用它。稍后，在 create 方法中，我们允许它添加到数据库，然后保存它，并在它保存到我们的数据库后将其带到 pic 页面。

现在，在***app/views/pics/***目录下创建一个***new . html . erb***&***_ form . html . erb***

在你的 ***_form.html.erb* 中添加这段代码。**

```
<%= **form_for @pic do |*f*| %>** <%= ***f*.label :image do %>** <%=  ***f*.hidden_field :image_cache %>** <%=  ***f*.file_field :image %>** <% **end %>** <%= ***f*.submit %>** <% **end %>**
```

因此，我们在这里创建一个表单，将一张图片添加到我们的数据库中，然后我们将在 web 上显示它。

现在，我们要实现显示页面和控制器。

在您的***pics _ controller . Rb***中添加以下代码

```
**def *show* @pic = Pic**.**find**(**params**[**:id**])
**end**
```

这里我们通过他们的 ***id*** 从数据库中提取数据

在***app/views/pics/***目录下创建一个新文件***show . html . erb***并以这简单的一行向公众显示图片

```
<%= **image_tag(@pic.image.url, width: '100%', crop: "scale")  %>**
```

我们还缩放图像，以适应显示器，使它看起来很漂亮。

太好了，您的图像显示出来了，现在我们将把所有的图像显示到我们的索引/主页上。打开***pics _ controller . Rb***将下面的代码添加到 ***index*** 控制器中，从数据库中获取所有的图像

```
**def *index* @pics = Pic**.**all
end**
```

在你的***index . html . erb***中添加这个来循环所有的图片并显示到索引页面。

```
<% **@pics.each do |*pic*| %>** <%=  **image_tag(*pic*.image.url, width: '100%', crop: "scale")  %>** <% **end %>**
```

现在，我们将通过添加简单的引导 css 和导航来美化我们的应用程序。

去 [bootstrap](https://bootswatch.com/paper/bootstrap.min.css) ，这是我非常喜欢的 bootstrap 材料设计风格，你可以用这个或任何你喜欢的东西。抓取链接，将其添加到 csrf meta 标签正下方的***application . html . erb***的 ***头***

```
**<link rel="stylesheet" href="https://bootswatch.com/paper/bootstrap.min.css">**
```

现在，在***app/views/layouts/***目录下创建一个 ***分部*** ，命名为***_ header . html . erb***，并添加导航到我们的页眉

```
**<nav class="navbar navbar-default">
  <div class="container-fluid">
    <div class="navbar-header">** <%= **link_to 'Rimgur', root_path, class: 'navbar-brand' %>** <%= **link_to 'New Post', new_pic_path, class: 'navbar-brand' %>
    </div>
  </div>
</nav>**
```

然后将标题呈现到我们的主应用程序页面。将此添加到您的***application . html . erb***顶部 ***产量***

```
<%= **render 'layouts/header' %>**
```

现在，作为最后一步，我们将把我们的应用程序部署到 heroku。

为了让我们的应用程序投入生产，我们需要对我们的 ***Gemfile 做一些修改。*** 将 ***宝石【SQLite】****移至*开发组并添加 ***pg*** 为 P ***ostgreSQL*** 为 ***生产。*****

```
****group :development do
  gem 'sqlite3'
end

group :production do
  gem 'pg'
end****
```

**然后按照下面的步骤安装 gems**

> **捆**
> 
> **gem 安装捆绑器**

**Git 添加并提交所有文件。**

> **git 添加。；git commit -m '项目已完成'**

**在你的账户和我们的代码库中创建一个新的 git repo。按照屏幕上的指示做 ***git 远程添加和推*** 。**

**现在，我们准备开始直播了。免费创建一个新的 [heroku](https://www.heroku.com/) 账号并验证。然后，在你的终端里新建一个 heroku app，推送。**

> **heroku 创建**
> 
> **饭桶推 heroku 主人**

**为了让我们的应用程序正常工作，我们需要将我们的模式迁移到生产环境中。我们可以这样做。**

> **heroku 运行 rails 数据库:迁移**

**做 ***heroku 打开*** 在浏览器中打开 app。**

**好了，现在你要做最重要的一步，我们的图像上传工作。**

> **heroku 插件:创建云**

**请记住，我们为[开发](https://hackernoon.com/tagged/development)机器的[云二进制](https://hackernoon.com/tagged/cloudinary)设置了环境变量，现在我们要为我们的生产设置环境变量。**

**转到您的 heroku 仪表盘，然后转到您的应用程序，再转到 cloudinary 仪表盘来获取我们的配置。**

**现在，在您的终端中执行以下操作来添加配置变量。**

> **heroku 配置:设置 CLOUDINARY_CLOUD_NAME=**
> 
> **heroku 配置:设置 CLOUDINARY_API_KEY=**
> 
> **heroku 配置:设置 CLOUDINARY_API_SECRET=**

**尝试在你的应用程序中上传一张照片，然后检查已添加的 cloudinary 仪表盘。**

**恭喜您，您已经成功构建了一个功能齐全的 web 应用程序。你可以随意摆弄它，让它变得更棒。**

**这是我的 github [回购](https://github.com/priom/rimgur)的链接。所有代码都托管在这里，供您参考。**

> **感谢你阅读它，希望你喜欢它，并将使用这些知识来建立一些真棒。**
> 
> ****喜欢&分享如果你喜欢我的帖子。请在下面评论任何反馈、建议或请求。也请访问我的** [**网站**](http://priom.tech) **。如果您想在收件箱中收到未来的帖子，请订阅我。****