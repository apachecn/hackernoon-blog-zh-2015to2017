# 如何使用 Node.js 构建一个简单的博客

> 原文：<https://medium.com/hackernoon/how-to-build-a-simple-blog-using-node-js-4ccdce39e78f>

![](img/0e4aae366d243e9d74096ace76944543.png)

Blog Card Preview

在本教程中，我将向你展示如何使用 Node.js 和 [Cosmic JS](https://cosmicjs.com) 创建一个简单的博客。这将是你创建的最快、最轻量级的博客。让我们开始吧。

# TL；速度三角形定位法(dead reckoning)

[查看演示](https://cosmicjs.com/apps/simple-blog/demo)
[在 Cosmic JS 上安装简单博客 App](https://cosmicjs.com/apps/simple-blog)
[在 GitHub 上查看代码库](https://github.com/cosmicjs/simple-blog)

# 入门指南

确保您的机器上安装了 [Node.js](https://cosmicjs.com/blog/Node.js) 和 NPM，如果没有，[请访问 Node.js 网站](https://nodejs.org/en/)安装最新版本。

确保您已经在全球范围内安装了[纱线](https://yarnpkg.com/en/):

```
npm install yarn -g
```

让我们首先为我们的应用程序创建一个文件夹。在您喜欢的终端中运行以下命令:

```
mkdir simple-blog
cd simple-blog
```

现在，让我们添加一个 package.json 文件来导入应用程序的所有依赖项:

```
vim package.json
```

将以下内容添加到我们的 package.json 文件中:

```
{
  "dependencies": {
    "cosmicjs": "^2.39.0",
    "express": "^4.15.2",
    "hogan-express": "^0.5.2",
    "nodemon": "^1.11.0"
  },
  "scripts": {
    "start": "node app.js",
    "development": "nodemon app.js"
  }
}
```

对于一个非常轻的应用程序来说，这是一个非常轻的依赖列表。因此，我们将安装的是:

1.宇宙 JS 节点模块从我们的宇宙 JS 桶中获取我们的内容。
2。我们的网络应用框架的快速表达。Hogan 为我们的模板视图
4。用于开发的节点

我们的脚本是在生产和开发中启动我们的应用程序所必需的。

运行以下命令来安装我们的依赖项:

```
yarn
```

# 建立我们的博客

接下来，让我们开始构建我们的博客页面。创建名为 app.js 的文件:

```
vim app.js
```

并将以下内容添加到 app.js 中:

```
const express = require('express')
const app = express()
const hogan = require('hogan-express')
const http_module = require('http')
const http = http_module.Server(app)
app.engine('html', hogan)
app.set('port', (process.env.PORT || 3000))
app.use('/', express.static(__dirname + '/public/'))
const Cosmic = require('cosmicjs')
const helpers = require('./helpers')
const bucket_slug = process.env.COSMIC_BUCKET || 'simple-blog-website'
const read_key = process.env.COSMIC_READ_KEY
const partials = {
  header: 'partials/header',
  footer: 'partials/footer'
}
app.use('/', (req, res, next) => {
  res.locals.year = new Date().getFullYear()
  next()
})
// Home
app.get('/', (req, res) => {
  Cosmic.getObjects({ bucket: { slug: bucket_slug, read_key: read_key } }, (err, response) => {
    const cosmic = response
    if (cosmic.objects.type.posts) {
      cosmic.objects.type.posts.forEach(post => {
        const friendly_date = helpers.friendlyDate(new Date(post.created_at))
        post.friendly_date = friendly_date.month + ' ' + friendly_date.date
      })
    } else {
      cosmic.no_posts = true
    }
    res.locals.cosmic = cosmic
    res.render('index.html', { partials })
  })
})
http.listen(app.get('port'), () => {
  console.info('==> 🌎  Go to http://localhost:%s', app.get('port'));
})
```

这里发生了一些事情:

1.我们正在导入我们的基本模块:Express，Cosmic JS，动态设置我们的端口，等等。
2。我们指出了一些部分:页眉和页脚，你可以从 GitHub 的代码库中参考这些部分。
3。我们查看我们的应用程序主页('/')并查询我们的宇宙 JS 桶中的 Post 对象，设置友好日期，然后返回 index.html 模板。
4。我们还以全球数据存储的形式将我们的数据添加到这个页面:cosmic。这种数据结构使得我们的模板实现非常直观。

# 添加我们的主页模板变量

这个过程的这一部分是最有趣的，因为它向您展示了 Cosmic JS 与声明性的、无逻辑的模板系统(如 [Mustache](http://mustache.github.io/) )相结合的威力。让我们创建一个名为 views 的文件夹，并添加我们的 index.html 文件:

```
mkdir views
cd views
vim index.html
```

将以下内容添加到 index.html 中:

```
{{> header }}
  <main class="container">
    {{# cosmic.objects.type.posts }}
      <div class="card" data-href="/{{ slug }}">
        {{# metadata.hero.imgix_url }}
          <div class="blog-post-hero blog-post-hero--short" style="background-image: url({{ metadata.hero.imgix_url }})"></div>
        {{/ metadata.hero.imgix_url }}
        <div class="card-padding">
          <h2 class="blog__title blog__title--small">
            <a href="/{{ slug }}">{{ title }}</a>
          </h2>
          <div class="blog__author">
            <div class="blog__author-image" style="background-image: url({{ metadata.author.metadata.image.imgix_url }}?w=100)"></div>
            <div class="blog__author-title">by <a href="/author/{{ metadata.author.slug }}">{{ metadata.author.title }}</a> on {{ friendly_date }}</div>
            <div class="clearfix"></div>
          </div>
          <div class="blog__teaser droid">{{{ metadata.teaser }}}</div>
          <div class="blog__read-more">
            <a href="/{{ slug }}">Read more...</a>
          </div>
        </div>
      </div>  
    {{/ cosmic.objects.type.posts }}
  </main>
{{> footer }}
```

这里发生了什么事？
1。我们用 Mustache 模板变量拉进头文件。
2。我们使用 Mustache 变量{{ cosmic }}获取宇宙数据，这个变量包含了我们布局页面动态数据所需的一切。
3。我们正在循环我们的{{ cosmic.objects.type.posts }}，并发布我们的博客文章。

我喜欢这种方法的一点是，除了一些布尔查询和数组循环之外，该模板完全没有逻辑。它确实很好地将我们的逻辑从我们的演示中分离出来(感谢[克里斯·万斯特拉斯](https://twitter.com/defunkt)！)

如果您想构建应用程序的其余部分，[克隆 GitHub repo](https://github.com/cosmicjs/simple-blog) 并按照自述文件的说明进行操作。

# 结论

这是[简单博客应用](https://cosmicjs.com/apps/simple-blog)的删节版，可以在[宇宙 JS 应用页面](https://cosmicjs.com/apps)下载。完整的代码库包括一个帖子页面视图，以及一个专门用于每个作者帖子的页面。[查看 GitHub 上的完整代码库](https://github.com/cosmicjs/simple-blog)并通过[将应用](https://cosmicjs.com/apps/simple-blog)安装到您的 Cosmic JS 桶中，在您的 Cosmic JS 仪表板上点击几下即可部署该应用。

我希望你喜欢这个教程，如果你有任何问题[在 Twitter 上联系我们](https://twitter.com/cosmic_js)和[加入我们的 Slack 社区](https://cosmicjs.com/community)。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)