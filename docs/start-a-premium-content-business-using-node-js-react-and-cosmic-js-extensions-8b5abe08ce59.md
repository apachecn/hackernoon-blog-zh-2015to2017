# 使用 Node.js、React 和 Cosmic JS 扩展开始优质内容业务

> 原文：<https://medium.com/hackernoon/start-a-premium-content-business-using-node-js-react-and-cosmic-js-extensions-8b5abe08ce59>

![](img/de2bfa72d960a909e60e761bc4a3cf86.png)

# TL；速度三角形定位法(dead reckoning)

[查看演示](https://cosmicjs.com/apps/premium-content-blog/demo)
[查看源代码](https://github.com/cosmicjs/premium-content-blog)
[安装优质内容博客](https://cosmicjs.com/apps/premium-content-blog)

你有很棒的想法。你的头脑中充满了你知道人们会付费阅读的内容。那么你从哪里开始呢？你可能倾向于选择一个像 Wordpress 这样的可信平台，但是因为你想为你的用户提供付费内容，你现在面临的问题是如何使一个麻烦的解决方案更加麻烦。

我们想要一个简单、直接的解决方案，功能不超过我们的需要，而不是调整一个平台来为我们工作。当然，这意味着我们要自己建造它。

本着简单、直接的解决方案的精神，我们将使用 [Cosmic JS](https://cosmicjs.com) 来托管我们的博客，管理它的用户，并存储它的内容。

我们的项目将分为两部分。首先，我们将使用 Express 和 Cosmic JS 构建我们的博客，并使用 Stripe 处理博客的支付和订阅。然后，我们将利用 Cosmic JS 的扩展特性来构建一个仪表板，让我们了解我们业务的后端。

# 第 1 部分:构建博客

# 1.样板设置

为了节省样板文件的时间，我们将使用 Yeoman 和 Express Generator(构建在 Express 的“官方生成器”上)来开始。如果你没有安装 Yeoman，运行`npm i -g yo`。然后用`npm i -g generator-express`安装发电机，用`yo express`运行发电机。按照说明在一个新目录下建立你的项目(比如说`CosmicUserBlog`，安装*基础*版本，并为你的视图引擎使用手柄。

您的目录结构现在如下:

```
CosmicUserBlog
| 
|--bin
|   |--www
|--(node_modules)
|--public
|--routes
|    |--users.js
     |--index.js
|--views
     |--layouts
     |--partials
     |--error.handlebars
     |--index.handlebars
|--.bowerrc
|--.gitignore
|--app.js
|--bower.json
|--gruntfile.js
|--package.json
```

# 2.装置

我们将使用以下软件包:

*   async——一个强大的异步实用程序库
*   axios——简单、基于承诺的 http 请求
*   Cors——标准 CORS 中间件
*   brcypt —用于密码哈希。(如果你在 Windows 上，请阅读这些注释
*   CosmicJs —官方客户
*   快速会话—以便我们的用户可以登录
*   date format——我们将在文章中使用的直观的日期格式化程序
*   Stripe —官方客户端
*   TruncateHTML —用于帖子简介

你可以用 npm 安装这些，但我主张用纱线。它明显更快，我们没有时间浪费。所以安装 Yarn(在 macOS 上我们会做一个`brew install yarn`)然后运行`yarn add async axios cors bcrypt cosmicjs expres-session dateformat stripe truncate-html`。我们差不多准备好开始建造了。

# 3.设置宇宙 JS

在我们开始构建之前，我们需要为我们的宇宙桶设计出模式。我们想要存储`Posts`、`Users`和`Configs`(以动态编辑站点配置)。

这三种对象类型将具有以下元数据字段(都是类型*文本*，由它们的*标题*给出):

## 帖子:

MetafieldValuePremium **真**或**假**

## 用户:

MetafieldValueFirst name string last name string password hashed string mailstring stripe idstring subscription Typestring

## 配置:

**对象:订阅**:| Metafield | Value | |————————————————:| |月价格|字符串|季度价格|字符串| |年价格|字符串| |取消|字符串|

**对象:站点**:| Metafield | Value | |—|——|——: | |站点标题|字符串| |域|字符串|

一旦您添加了您的`Post`、`User`和`Config`对象类型，并创建了您的`Subscriptions`和`Site`配置对象，我们就可以用 Stripe 进行设置了。

# 4.配置条带

由于我们将向用户收取额外费用，我们需要一个支付处理器。凭借强大的 API、公平的现收现付价格和经过验证的安全性，使用 Stripe 是显而易见的。接下来，我们将需要 Stripe API 的“可发布”和“秘密”密钥，并且我们需要为每月、每年和每季度订阅设置订阅计划。按照 Stripe 的说明创建这些订阅，并相应地为他们提供 ID 的*订阅-每月*、*订阅-每季度*和*订阅-每年*。

# 5.配置快速应用程序

我们已经安装了我们的软件包，制定了我们的数据模式，并且我们已经建立了一个 Stripe 帐户。现在我们需要配置我们的 Express 后端。

样板 Express 代码是 pre-ES5，所以为了保持一致的风格，我们将`require`我们需要的包。

在快速应用程序的顶部添加:

```
// app.js
var session = require('express-session')
var dateFormat = require('date-format')
var truncate = require('truncate-html')
var cors = require('cors')
```

当我们部署我们的应用程序时，Cosmic JS 将提供我们的桶密钥以及我们通过`process.env`提供的任何自定义密钥。在 require 语句下面，继续将它们存储在`app.locals`中，使它们在整个应用程序中都可以访问

```
//app.jsvar config = {
    bucket: {
        slug: process.env.COSMIC_BUCKET,
     read_key: process.env.COSMIC_READ_KEY,
      write_key: process.env.COSMIC_WRITE_KEY
    }
}
app.locals.config = config
app.locals.stripeKeyPublishable = process.env.STRIPE_PUBLISHABLE_KEY
app.locals.stripeKeySecret = process.env.STRIPE_SECRET_KEY
```

最后一步是连接`cors`和`session`中间件，如下所示:

```
//app.jsapp.locals.stripeKeySecret = process.env.STRIPE_SECRET_KEYapp.use(session({
  secret: 'sjcimsoc',
  resave: false,
  saveUninitialized: true,
  cookie: { secure: false }
}))
app.use(cors())
```

在这一点上，我们有一个坚实的基础来建立我们的应用程序，并可以开始起草它的观点。

# 6.雕刻出景色

有一个我们希望我们的博客看起来和感觉的模型将帮助我们思考如何连接它的路线。我们将从主要布局开始。

**主要布局:**

在 handlebars 中，每个页面都会在默认布局的`body`标签中呈现。在我们的样板文件中，这是`/views/layouts/main.handlebars`。

我们需要做三处修改。

1.  在`title`标签中，用`{{title}}`替换`{{config.site_title}}`，稍后我们将通过`res.locals`传递它。
2.  在`head`标记的末尾之前加上`<script src="https://js.stripe.com/v3/"></script>`。这是 Stripe 的浏览器客户端。我们只在结帐表单中需要它，但是 Stripe 建议在每一页都包含它，以帮助欺诈检测。
3.  包括自举。在`head`标签中的某处添加`<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" />`并在`body`标签结束之前添加

```
<script src="https://code.jquery.com/jquery-3.2.1.min.js"
 integrity="sha256-hwg4gsxgFZhOsEEamdOYGBf13FyQuiTwlAQgxVSNgt4="
 crossorigin="anonymous"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
```

主布局现在看起来像这样:

```
<!-- views/layouts/main.handlebars -->
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width">
    <title>{{config.site_title}}</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" />
    {{#if ENV_DEVELOPMENT}}
      <script src="http://localhost:35729/livereload.js"></script>
    {{/if}}
    <script src="https://js.stripe.com/v3/"></script> </head>
  <body> {{{body}}} <script
    src="https://code.jquery.com/jquery-3.2.1.min.js"
    integrity="sha256-hwg4gsxgFZhOsEEamdOYGBf13FyQuiTwlAQgxVSNgt4="
    crossorigin="anonymous"></script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
  </body>
</html>
```

**表头部分:**

Handlebars 期望在默认情况下在`/views/partials`目录中找到 partials，所以我们将在那里创建`header.handlebars`。它看起来会像这样:

```
<header>
  <div class="container">
    <div class="row">
      <div class="col-xs-12 text-center">
        <h1>{{config.site_title}}</h1>
      </div>
    </div>
  </div>
</header><nav class="navbar navbar-default">
  <div class="container-fluid">
    <div class="navbar-header">
      <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar-collapse-header" aria-expanded="false">
        <span class="sr-only">Toggle navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
    </div> <div class="collapse navbar-collapse" id="navbar-collapse-header">
      <ul class="nav navbar-nav">
        <li {{#if route_posts}}class="active"{{/if}}><a href="/posts">Posts<span class="sr-only">(current)</span></a></li>
        <li><a href="/premium">Premium Content</a></li>
      </ul>
      <ul class="nav navbar-nav navbar-right">
        {{#if logged_in}}
          <li class="navbar-text text-center">Welcome Back, {{user.first_name}}!</li>
          <li><a href="/logout">Logout</a></li>
        {{/if}} {{#unless logged_in}}
          <li {{#if route_login}}class="active"{{/if}}><a href="/login">Login</a></li>
          <li {{#if route_signup}}class="active"{{/if}}><a href="/plans">Sign Up</a></li>
        {{/unless}}
      </ul>
    </div>
  </div>
</nav>
```

**注意:**在 header partial 中，我们利用手柄的内置`if`和`unless`助手来使我们的代码部分特定于路径。我们稍后将在路由处理程序中传递相对布尔值。

**帖子视图**

在帖子页面上，我们希望:

1.  显示标题
2.  如果用户试图在没有帐户的情况下访问高级帖子，则显示错误消息
3.  将 post 显示逻辑抽象成它自己的部分，使我们的代码更加整洁和模块化。

对于错误消息，我们将像以前一样依赖 Handlebars 的`if`助手。为了显示帖子的摘要，我们将把帖子作为 post 对象的数组传递给视图。这让我们可以使用 Handlebars `each` block 助手来迭代数组(每个 post 都可以通过`this`访问)。我们的帖子视图将如下所示:

```
{{> header}}
<div class="container">
  <div class="row">
    {{#if error}}
      <div class="alert alert-danger" role="alert">
        {{error}}
      </div>
    {{/if}}
    {{#each posts}}
      {{> post-container this}}
    {{/each}}
  </div>
</div>
```

**邮政集装箱局部:**

下一个明显的步骤是构建容器，在 Posts 页面上显示每篇文章的摘要。除了显示文章的标题和内容(很容易通过`this.title`访问)等。)我们希望向用户显示帖子是否是 premium，以及帖子正文及其创建日期的截断。

如果`this.metadata.premium`返回`true`，我们将再次求助于`if`助手在柱子旁边显示一颗星星，这很简单。对于导语和创建日期，我们需要修改 Post 对象的`content`和`created_at`属性；首先，要缩短它，在后者，因为宇宙存储的日期在 ISO 日期时间格式。为了让显示逻辑远离我们的视图，Handlebars 为我们提供了编写自己的助手的功能。

首先，准备好视图代码:

```
<!-- /views/partials/post-container.handlebars --><div style="border-bottom: 3px solid #337ab7" class="col-xs-12 col-md-8 col-md-offset-2">
  <h2>
    {{#if this.metadata.premium}}
      <span style="font-size: 0.5em" class="glyphicon glyphicon-star"></span>
    {{/if}}
    <a href="/post/{{this.slug}}">{{this.title}}</a>
  </h2>
  <p style="margin: 35px 40px">
    {{truncateText this.content 20}} <a href="/post/{{this.slug}}">Read more</a>
  </p>
  <em style="margin: 20px 0" class="pull-right">
    {{date this.created_at}}
  </em>
</div>
```

(提示:我们的车把助手将被命名为 *truncateText* 和 *date* 。)

打开`app.js`并找到将手柄设置为视图引擎的代码片段。`exphbs`是对`express-handlebars`的引用，传递给它的对象包含用于实例化引擎的参数。我们需要向该对象添加`helpers`属性。`helpers`属性将指向`date`和`truncateText`方法，如下所示:

```
// app.js// etc...
app.engine('handlebars', exphbs({
  defaultLayout: 'main',
  partialsDir: ['views/partials/'],
  helpers: {
    date: function(date) {
      return dateFormat(new Date(date), "dddd, mmmm dS, yyyy")
    },
    truncateText: function(text, length) {
      return truncate(text, length, { stripTags: true, byWords: true })
    }
  }
}));
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'handlebars');
// etc...
```

举例来说，`{{truncateText this.content 20}}`告诉 Handlebars 渲染`truncate(this.content, 20, {...} )`的结果。

**帖子查看:**

我们的用户需要能够阅读单个帖子，所以我们将构建一个简单的视图，get 传递一个一个帖子长的数组。(在本指南范围之外的将来，我们可能会发现使用视图连续显示多个完整的帖子很有用。)

如果找不到帖子，我们还会显示一个错误。像这样创建你的帖子视图:

```
<!-- /views/post.handlebars -->{{> header}}
{{#if not_found}}
  <div class="container">
    <div class="row text-center">
      <div class="col-xs12 col-md-6 col-md-offset-3">
        <div class="alert alert-danger">
          Post Not Found!
        </div>
        <a href="/posts">See All Posts</a>
      </div>
    </div>
  </div>
{{/if}}{{#each post}}
  <article class="container">
    <div class="row">
      <div class="col-xs-12 col-sm-8 col-sm-offset-2">
        <h1>{{this.title}}</h1>
        <div class="lead" style="font-size: 1.5em">
          {{{this.content}}}
        </div>
        <em class="pull-right">
          <time>{{date this.created_at}}</time>
        </em>
      </div>
    </div>
  </article>
{{/each}}
```

**登录页面:**

登录页面将是最简单的——一个发布到登录路径的基本表单。

```
<!-- /views/login.handlebars -->{{> header}}<div class="container">
  <div class="row">
    <div class="col-xs-12 col-sm-4 col-sm-offset-4">
      <h1>Log In</h1>
      <p class="text-muted">
        Log in to view premium content.
      </p> <form method="post">
        <input class="form-control" type="email" name="email" placeholder="Email" required/>
        <input type="password" class="form-control" name="password" placeholder="Password" required />
        <button class="btn btn-lg btn-primary btn-block submit-btn" type="submit">Log in</button>
      </form>
    </div>
  </div>
</div><style>
  form > input, button {
    margin-top: 12px;
  }
</style>
```

**计划页面:**

显然，我们的用户需要在登录前注册，但由于我们给他们提供了从三个订阅计划中选择一个的选项，所以我们将构建一个视图，在结账前向他们显示他们的选项。稍后，我们需要将 Subscriptions 对象传递给这个视图，这样我们就可以从 Cosmic 中设置计划价格，而不是硬编码它们。该视图将如下所示:

```
<!-- /views/plans.handlebars -->{{> header}}<div class="container">
  <div class="row text-center lead">
    <h1>Choose a Plan to Read Premium Content</h1>
    <p>
      Sign up to view Premium Content on {{config.site_title}}
    </p>
  </div> <div class="row">
    <div class="col-sm-4">
      <h3 class="text-center text-muted">
        Monthly
      </h3>
      <p class="text-center">
        <strong>Billed every month</strong>
      </p>
      <h1 class="text-center text-success">{{subscriptions.monthly_price}}</h1 class="text-center text-success">
      <ul class="list-unstyled lead" style="padding: 0 20px">
        <li>
          <span class="glyphicon glyphicon-ok text-success"></span>Here's a good benefit
        </li>
        <li>
          <span class="glyphicon glyphicon-ok text-success"></span>A reason to buy
        </li>
        <li>
          <span class="glyphicon glyphicon-ok text-success"></span>Why you have to have it
        </li>
        <li>
          <span class="glyphicon glyphicon-ok text-success"></span>Why you shouldn't miss out
        </li>
        <li>
          <span class="glyphicon glyphicon-ok text-success"></span>Believe it.
        </li>
      </ul>
      <a href="/signup?plan=monthly"><button class="btn btn-block btn-default btn-lg">Sign Up</button></a>
    </div>
    <div class="col-sm-4" style="border: 2px solid #3c763d">
      <h3 style="background: #3c763d;color: white;padding: 7px 0" class="text-center text-success">
        Yearly
      </h3>
      <p class="text-center">
        <strong>Billed every 12 months</strong>
      </p>
      <h1 class="text-center text-success">{{subscriptions.yearly_price}}</h1>
        <ul class="list-unstyled lead" style="padding: 0 20px">
          <li>
            <span class="glyphicon glyphicon-ok text-success"></span>Here's a good benefit
          </li>
          <li>
            <span class="glyphicon glyphicon-ok text-success"></span>A reason to buy
          </li>
          <li>
            <span class="glyphicon glyphicon-ok text-success"></span>Why you have to have it
          </li>
          <li>
            <span class="glyphicon glyphicon-ok text-success"></span>Why you shouldn't miss out
          </li>
          <li>
            <span class="glyphicon glyphicon-ok text-success"></span>Believe it.
          </li>
        </ul>
        <a href="/signup?plan=yearly"><button class="btn btn-block btn-default btn-lg">Sign Up</button></a>
    </div>
    <div class="col-sm-4">
      <h3 class="text-center text-muted">
        Quarterly
      </h3>
      <p class="text-center">
        <strong>Billed every 3 months</strong>
      </p>
      <h1 class="text-center text-success">{{subscriptions.quarterly_price}}</h1 class="text-center text-success">
        <ul class="list-unstyled lead" style="padding: 0 30px">
          <li>
            <span class="glyphicon glyphicon-ok text-success"></span>Here's a good benefit
          </li>
          <li>
            <span class="glyphicon glyphicon-ok text-success"></span>A reason to buy
          </li>
          <li>
            <span class="glyphicon glyphicon-ok text-success"></span>Why you have to have it
          </li>
          <li>
            <span class="glyphicon glyphicon-ok text-success"></span>Why you shouldn't miss out
          </li>
          <li>
            <span class="glyphicon glyphicon-ok text-success"></span>Believe it.
          </li>
        </ul>
        <a href="/signup?plan=quarterly"><button class="btn btn-block btn-default btn-lg">Sign Up</button></a>
    </div>
  </div>
</div><style>
  form > input, button {
    margin-top: 12px;
  }
  ul.lead {
    margin-top: 40px
  }
  .lead > li {
    margin: 8px 0
  }
  .glyphicon {
    margin-right: 12px
  }
  .btn {
    margin: 35px 0px;
  }
</style>
```

*注意，每个注册按钮都链接到* `*signup*` *路线，传递一个与所选计划相关的查询参数。即* `*/signup?plan={:plan}*`

**报名页面:**

最后，但肯定不是最不重要的——赚钱机器。我们把最复杂的视图留到了最后。这些是我们的要求:

1.  我们需要通过之前在`planName`页面上选择的计划。(稍后我们将在 URL 查询字符串中执行此操作)
2.  我们需要使用 Stripe Elements API 来收集信用卡信息。这是我们早前在主布局中包含的`Stripe.js`。在我们结帐表格的最后，我们需要两个`div`;一个 ID 为`card-element`,另一个 ID 为`card-errors`,用于在初始 DOM 渲染后注入`Stripe.js`。
3.  我们需要传递我们的可发布的条形密钥来使它工作。

从 HTML 开始，我们有:

```
<!-- /views/signup.handlebars -->
{{>header}}<div class="container">
  <div class="row">
    <form method="post" id="payment-form">
      <div class="col-xs-12 text-center">
        <h4 class="lead"><em>You're one step away from a <u>{{planName}}</u> subscription to {{config.site_title}}!</em></h4>
      </div>
    </div>
    <div class="row"  style="margin-top: 30px">
      <div class="col-md-8 col-md-offset-2">
        <h4>Enter your account details and payment information</h4> <label for="first_name">First name:</label>
        <input type="text" name="first_name" class="form-control" placeholder="First name" required />
        <label for="last_name">Last name:</label>
        <input type="text" name="last_name" class="form-control" placeholder="Last name" required />
        <label for="email">Email:</label>
        <input type="email" name="email" class="form-control" placeholder="Email" required />
        <label for="password">Password</label>
        <input type="password" name="password" class="form-control" placeholder="Password" required />
        <label for="card-element">Credit or debit card</label>
        <div class="form-control" id="card-element"></div>
        <div id="card-errors" role="alert"></div>
        <button id="submit-button" class="btn-success btn btn-lg btn-block btn-default" >Submit Payment</button> </div>
    </div>
    </form>
</div>
```

然后，为简单起见，我们将使用一个集成了 Stripe 的内联脚本:

```
<!-- views/signup.handlebars --><script>
  var stripe = Stripe('{{stripeKeyPublishable}}')
  var elements = stripe.elements()
  var card = elements.create('card')
  var style = {
      base: {
        color: '#32325d',
        lineHeight: '24px',
        fontFamily: '"Helvetica Neue", Helvetica, sans-serif',
        fontSmoothing: 'antialiased',
        fontSize: '16px',
        '::placeholder': {
          color: '#aab7c4'
        }
      },
      invalid: {
        color: '#fa755a',
        iconColor: '#fa755a'
      }
    }; card.mount('#card-element', {style: style}) // Handle real-time validation errors from the card Element.
card.addEventListener('change', function(event) {
  var displayError = document.getElementById('card-errors');
  if (event.error) {
    displayError.textContent = event.error.message;
  } else {
    displayError.textContent = '';
  }
});// Handle form submission
var form = document.getElementById('payment-form');
var submitButton = document.getElementById('submit-button');
form.addEventListener('submit', function(event) {
  event.preventDefault();
  submitButton.disabled=true
  stripe.createToken(card).then(function(result) {
    if (result.error) {
      // Inform the user if there was an error
      var errorElement = document.getElementById('card-errors');
      errorElement.textContent = result.error.message;
      submitButton.disabled=false
    } else {
      // Send the token to your server
      stripeTokenHandler(result.token);
    }
  });
});function stripeTokenHandler(token) {
  // Insert the token ID into the form so it gets submitted to the server
  var form = document.getElementById('payment-form');
  var hiddenInput = document.createElement('input');
  hiddenInput.setAttribute('type', 'hidden');
  hiddenInput.setAttribute('name', 'stripeToken');
  hiddenInput.setAttribute('value', token.id);
  form.appendChild(hiddenInput);
  // Submit the form
  form.submit();
}
</script><style>
  label,button {
    margin-top: 22px;
  }
</style>
```

事情是这样的:

1.  我们用传递的可发布键实例化 Stripe，将它的`elements`库赋给它自己的变量，用它来创建一个`card`元素，最后将它挂载到我们之前创建的`<div id="card-element">`中。卡对象处理卡验证，打包了良好的 UX 特性，并实时向用户报告错误。
2.  我们将一个事件侦听器附加到 card 对象，该对象响应用户输入的卡号、CCV 或到期日期的任何变化。它将错误记录在`<div id="card-errors">`上
3.  我们手动处理表单提交。首先，我们阻止默认操作，并禁用多次提交。然后，如果没有错误，我们将一个隐藏字段附加到包含 Stripe 的验证令牌的表单，由`Stripe.js`提供，*然后*将表单提交给`Signup`路由。

# 7.建造路线

随着视图的构建，我们确切地知道我们的应用程序需要什么样的路由。即:

*   邮件
*   邮政
*   溢价
*   注册
*   注销
*   注册
*   计划

默认情况下，您的应用程序有一条`Users`路线和一条`Index`路线。删除`Users`并使`Index`重定向到`Posts`如下:

```
// /routes/index.jsvar express = require('express')
var router = express.Router()router.get('/', function(req, res) {
  res.redirect('/posts')
});module.exports = router
```

**发帖路线:**

post 将使用`async`将一系列异步函数串在一起:一个使用 Cosmic JS 客户端获取我们的帖子，下一个使用 Cosmic 获取站点配置并呈现`posts`视图，传递相关的局部变量。如果用户不在认证会话中，我们将使用`lodash`过滤掉从 Cosmic 返回的*没有*标记为高级(因此可以免费阅读)的帖子。我们将通过`res.locals`传递帖子、配置和特定于路由的视图数据。

```
var express = require('express');
var router = express.Router();
var cosmic = require('cosmicjs');
var async = require('async');
var _ = require('lodash')router.get('/', function(req, res) {
  async.series([
    function(cb) {
      cosmic.getObjectType(req.app.locals.config, { type_slug: 'posts' }, function(err, response) {
        (req.session.user) ? res.locals.posts = response.objects.all : res.locals.posts = _.filter(response.objects.all, function(post) {
          return !post.metadata.premium
        })
        cb()
      })
    },
    function(cb) {
      cosmic.getObject(req.app.locals.config, { slug: 'site' }, function(err, response) {
        res.locals.config = response.object.metadata
        res.locals.user = req.session.user
        res.locals.route_posts = true
        if (req.session.user) res.locals.logged_in = true
        return res.render('posts.handlebars')
      })=
    }
  ])
});module.exports = router;
```

**发布路线:**

有了一个针对所有帖子的路由，我们需要一个针对单个帖子的伴随路由，它将帖子段作为 URL 参数，如果找到了就返回该帖子，使用与`Posts`路由类似的逻辑。

```
// routes/post.jsvar express = require('express');
var router = express.Router();
var cosmic = require('cosmicjs');
var async = require('async');
var _ = require('lodash')router.get('/:slug', function(req, res) {
  async.series([
    function(cb) {
      cosmic.getObjectType(req.app.locals.config, { type_slug: 'posts' }, function(err, response) {
        res.locals.post = _.filter(response.objects.all, function(post) {
          return post.slug === req.params.slug
        })
        if (!res.locals.post) res.locals.not_found = true
        cb()
      })
    },
    function(cb) {
      cosmic.getObject(req.app.locals.config, { slug: 'site' }, function(err, response) {
        res.locals.config = response.object.metadata
        res.locals.user = req.session.user
        return res.render('post.handlebars')
      })
    }
  ])
});module.exports = router
```

**登录路径:**

为了让用户能够访问优质帖子，我们需要收集他们的登录信息，用 bcrypt 散列他们的密码，并对照与存储在 Cosmic 中的电子邮件地址相关联的密码进行检查。

GET 请求将呈现登录表单。然后，我们处理表单的 POST 请求，从 Cosmic 检索所有用户，并使用两个异步函数迭代所有用户:一个使用 bcrypt 比较密码散列，下一个将用户的数据保存到会话中(如果找到的话)。

```
// routes/login.jsvar express = require('express');
var router = express.Router();
var cosmic = require('cosmicjs');
var async = require('async');
var _ = require('lodash')
var bcrypt = require('bcrypt')router.get('/', function(req, res) {
  async.series([
    function(cb) {
      cosmic.getObject(req.app.locals.config, { slug: 'site' }, function(err, response) {
        res.locals.config = response.object.metadata
        res.locals.route_login = true
        return res.render('login.handlebars')
      })
    }
  ])
});router.post('/', function(req, res) {
  cosmic.getObjectType(req.app.locals.config, { type_slug: 'users' }, function (err, response) {
    if (err) res.status(500).json({ status: 'error', data: response })
    else {
      async.eachSeries(response.objects.all, function (user, eachCb) {
        if (!_.find(user.metafields, { key: 'email', value: req.body.email.trim().toLowerCase() }))
          return eachCb()
        const stored_password = _.find(user.metafields, { key: 'password' }).value
        bcrypt.compare(req.body.password, stored_password, function (err, correct) {
          if (correct) res.locals.user_found = user
          eachCb()
        })
      }, function () {
        if (res.locals.user_found) {
          req.session.user = {
            first_name: res.locals.user_found.metafield.first_name.value,
            last_name: res.locals.user_found.metafield.last_name.value,
            email: res.locals.user_found.metafield.email.value
          }
          req.session.save()
          return res.redirect('/posts')
        }
        return res.status(404).json({ status: 'error', message: 'User not found' })
      })
    }
  })
})module.exports = router
```

**计划路线:**

在用户登录之前，我们需要一个注册表单，这样我们就可以首先拥有用户。如前所述，我们将在注册表单前向他们展示订阅选项。这只是在呈现视图之前传递站点和订阅配置:

```
var express = require('express');
var router = express.Router();
var cosmic = require('cosmicjs');
var async = require('async')router.get('/', function(req, res) {
  async.series([
    function(cb) {
      cosmic.getObject(req.app.locals.config, { slug: 'site' }, function(err, response) {
        res.locals.config = response.object.metadata
        res.locals.route_signup = true
        cb()
      })
    },
    function(cb) {
      cosmic.getObject(req.app.locals.config, { slug: 'subscriptions' }, function(err, response) {
        res.locals.subscriptions = response.object.metadata
        res.render('plans.handlebars')
      })
    }
  ])
});module.exports = router;
```

**报名路线:**

正如你可能已经猜到的，注册路线是所有路线中进行得最多的。以下是我们需要实现的内容:

1.  在 GET 请求中，呈现注册表单并通过`res.locals`传递我们的可发布 Stripe 密钥，以便 Stripe.js 工作。
2.  根据 Post 请求:
3.  用我们的密钥实例化 Stripe 服务器端
4.  如果用户已经登录，重定向他们。
5.  运行一系列两个名为的*异步函数(这样我们可以在两个函数都完成后使用它们的返回值)来从 Cosmic 获取我们的订阅数据并散列密码。*
6.  完成第 3 步后，我们使用 Stripe API 创建一个新客户，通过注册表单中传递的 Stripe 令牌关联他们的支付方式。
7.  然后，我们根据选择的计划向该客户收费，并创建新的套餐(同样通过 Stripe ),以便自动处理重复支付。
8.  我们基于我们的宇宙模式创建一个新的用户对象，将其添加到我们的 bucket 中，一旦成功，我们为用户创建一个新的会话，并将他们重定向到`Posts`路由，在那里他们现在可以查看优质内容。

全部完成后，它将看起来像这样:

```
// routes/signup.jsvar express = require('express');
var router = express.Router();
var cosmic = require('cosmicjs');
var async = require('async');
var bcrypt = require('bcrypt')router.get('/', function(req, res) {
  if (req.session.user) res.redirect('/')
  async.series([
    function(cb) {
      cosmic.getObject(req.app.locals.config, { slug: 'site' }, function(err, response) {
        res.locals.config = response.object.metadata
        res.locals.route_signup = true
        cb()
      })
    },
    function(cb) {
      cosmic.getObject(req.app.locals.config, { slug: 'subscriptions' }, function(err, response) {
        res.locals.subscriptions = response.object.metadata
        res.locals.stripeKeyPublishable = req.app.locals.stripeKeyPublishable
        res.locals.planName = req.query.plan
        res.render('signup.handlebars')
      })
    }
  ])
});router.post('/', function(req, res) {
  var stripe = require('stripe')(req.app.locals.stripeKeySecret)
  if (req.session.user) res.redirect('/') async.series({
    subscriptions: function(callback) {
      cosmic.getObject(req.app.locals.config, { slug: 'subscriptions' }, function(err, response) {
        callback(null, response.object.metadata)
      })
    },
    hash: function (callback) {
      bcrypt.hash(req.body.password, 10, function (err, hash) {
        callback(null, hash)
      })
    }
  }, function (err, results) { stripe.customers.create({
      email: req.body.email,
      source: req.body.stripeToken
    }).then(function (customer) {
      return stripe.charges.create({
        amount: results.subscriptions[req.query.plan + "_price"].replace(/[$]/,'') + '00',
        currency: "usd",
        customer: customer.id
      })
    }).then(function (charge) {
      stripe.subscriptions.create({
        customer: charge.customer,
        items: [
          {
            plan: 'subscription-' + req.query.plan
          }
        ]
      })
      var object = {
        type_slug: 'users',
        title: req.body.first_name + ' ' + req.body.last_name,
        metafields: [
          {
            title: 'First name',
            key: 'first_name',
            type: 'text',
            value: req.body.first_name
          },
          {
            title: 'Last name',
            key: 'last_name',
            type: 'text',
            value: req.body.last_name
          },
          {
            title: 'Password',
            key: 'password',
            type: 'text',
            value: results.hash
          },
          {
            title: 'Email',
            key: 'email',
            type: 'text',
            value: req.body.email
          },
          {
            title: 'Stripe Id',
            key: 'stripe_id',
            type: 'text',
            value: charge.customer
          },
          {
            title: 'Subscription Type',
            key: 'subscription_type',
            type: 'text',
            value: req.query.plan
          }
        ]
      }
      if (req.app.locals.config.bucket.write_key) object.write_key = req.app.locals.config.bucket.write_key
      cosmic.addObject(req.app.locals.config, object, function (err, reponse) {
        if (err)
          res.status(500).json({ data: reponse })
        else {
          req.session.user = {
            first_name: req.body.first_name,
            last_name: req.body.last_name,
            email: req.body.email
          }
          req.session.save()
          res.redirect('/posts')
        }
      })
    })
  })
})module.exports = router
```

**注销路径:**

为了方便用户，我们需要给用户一个退出的机会。所有这一切只需要给`/logout`一个 POST 请求和一个快速的`session.destroy()`呼叫。

```
var express = require('express')
var router = express.Router()/* GET home page. */router.get('/', function(req, res) {
  req.session.destroy()
  return res.redirect('/')
});module.exports = router
```

**将它们连接在一起:**

我们已经建立了所有的路线，并准备好按照我们需要的方式工作，我们将在我们的应用程序中`require`它们，并通过`app.use()`将它们关联的端点指向它们

```
// app.jsvar routes = require('./routes/index');
var posts = require('./routes/posts');
var post = require('./routes/post')
var login = require('./routes/login')
var logout = require('./routes/logout')
var signup = require('./routes/signup')
var plans = require('./routes/plans')
var premium = require('./routes/premium')
var api = require('./routes/api')app.use('/', routes);
app.use('/post', post)
app.use('/posts', posts)
app.use('/login', login)
app.use('/logout', logout)
app.use('/signup', signup)
app.use('/plans', plans)
app.use('/premium', premium)
app.use('/api', api)
```

**继续扩展:**

如果到目前为止你已经做了所有的事情，你的博客现在完全按照你的预期运行。为了测试，继续运行`npm start`，在 Cosmic 中创建几个帖子并验证它们是否被获取。然后创建一个虚拟帐户，并确保它存储在宇宙和注册的条纹。然后，我们将为 Cosmic 构建我们的仪表板扩展。

# 第 2 部分:构建扩展

Stripe 为我们提供了令人印象深刻的大量分析，但是我们希望有一个中心位置来快速浏览我们所有用户的列表、他们的订阅计划以及关于我们博客的三个关键指标:迄今为止的收入、活跃订阅和迄今为止的取消。

Cosmic JS 通过利用它的扩展特性让我们能够做到这一点，这让我们可以上传一个以`index.html`为入口点的 SPA，该 SPA 被加载到我们的 Cosmic 仪表盘的一个框架中。然后通过 URL 查询字符串向它提供桶键。

我们将使用 React 构建扩展，因为我们的扩展只需要一个视图层。

# 设置

为了保持有序，我们将应用程序存储在`CosmicUserBlog`目录下。我们的树看起来会像这样:

```
CosmicUserBlog
|
|--extensions
| |--subscription-management
| | |--client
| | | |--components
| | | |--index.js
| | | |--index.html
| | |--dist
```

一旦你有了合适的目录结构，运行`yarn init`，我们将继续安装。

# 装置

我们需要这些包:

*   异步ˌ非同步(asynchronous)
*   axios
*   巴别塔-预设-2015
*   巴别塔-预设-2016
*   cosmicjs
*   html-webpack-plugin——用 web pack 生成我们的 html
*   洛达什
*   查询字符串——一种解析桶键的简单方法
*   小路
*   反应
*   反应范围
*   反作用载荷
*   网络包
*   巴别塔核心
*   巴别塔装载机
*   巴别塔-预设-反应

运行`yarn add async axios babel-preset-2015 babel-preset-2016 cosmicjs html-webpack-plugin lodash query-string path react react-dom react-loading webpack babel-core babel-loader-babel-preset-react`，然后我们就开始潜水。

# 配置 Webpack 和 Babel

首先，在根文件夹中创建`.babelrc`,并告诉它如何传输我们的代码:

```
// .babelrc{
  "presets": [
    "es2016", "es2015", "react"
  ]
}
```

然后，再次在`CosmicUserBlog/extensions/subscription-management`下，make `webpack.config.js`以便我们可以告诉 Webpack 如何打包我们的模块。

```
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')module.exports = {
  entry: './client/index.js',
  output: {
    path: path.resolve('dist'),
    filename: 'index_bundle.js'
  },
  module: {
    loaders: [
      { test: /\.js$/, loader: 'babel-loader', exclude: /node_modules/ }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './client/index.html',
      filename: 'index.html',
      inject: 'body'
    })
  ]
}
```

`dist`将包含我们所有的输出文件(那些是`index_bundle.js`和`index.html`)，我们最终将压缩`dist`作为我们的扩展上传。html-webpack-plugin 将从`client/index.html`获取我们的 html 模板，并在构建时链接到`dist/index.html`中我们编译的 javascript。

# 创建一个条目文件

我们想使用 Boostrap，我们需要一个`div`(我们将标识为`root`)来装载 React 应用程序。`client/index.html`应该是这样的:

```
<!DOCTYPE html>
<html>
  <head>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" />
  </head>
  <body>
    <div id="root">
      <script
        src="https://code.jquery.com/jquery-3.2.1.min.js"
        integrity="sha256-hwg4gsxgFZhOsEEamdOYGBf13FyQuiTwlAQgxVSNgt4="
        crossorigin="anonymous"></script>
      <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
  </body>
</html>
```

# 构建 React 应用程序

**1。创建一个入口点**

我们已经准备好开始动手了。我们将使用`client/index.js`作为 React 应用程序的入口点。我们将导入 React，将其设置为一个全局变量，在它的 props 中将我们的宇宙键传递给它，并将 App 组件(接下来我们将制作)挂载到`<div id="root">`。

```
import React from 'react'
import ReactDom from 'react-dom'
import App from './components/App'
import QueryString from 'query-string'window.React = React
const url = QueryString.parse(location.search)const cosmic = { bucket: {
    slug: url.bucket_slug,
    write_key: url.write_key,
    read_key: url.read_key
  }
}ReactDom.render(
  <App cosmic={cosmic}/>,
  document.getElementById('root')
)
```

**2。构建应用组件**

为了让我们的应用保持模块化，我们将采用分层组件结构，如下所示:

```
components
|
|--App.js
|--Header.js
|--SubscriberData
| |
| |--SubscriberContainer.js
| |--Loader.js
| |--StatsContainer.js
| |--StatTicker.js
| |--UserList.js
```

`Header`和`SubscriberContainer`将是`App`的直接子节点。`Loader`、`UserList`、`StatsContainer`都是`SubcriberContainer`的直接子节点。最后，`StatsContainer`将由`StatTicker`组成

除了保持一个有组织的项目之外，这种结构允许我们最大化我们的无状态功能组件的数量，这些组件不是 React 类，而且也很快。

从层次结构的顶端开始，我们将构建一个应用程序组件，它将我们的宇宙键存储在其状态中，并呈现一个`Header`和一个`SubscriberContainer`。

```
// components/App.jsimport { Component } from 'react'
import Header from './Header'
import SubscriberContainer from './SubscriberData/SubscriberContainer'export default class App extends Component { constructor(props) {
    super(props)
    this.state = {
      cosmic: this.props.cosmic
    }
  } render() {
    return (
      <div>
        <Header
          bucket={this.state.cosmic.bucket.slug} />
        <SubscriberContainer
          cosmic={this.state.cosmic} />
      </div>
    )
  }
}
```

下一个明显的步骤是构建头部组件。

**构建标题:**

Header 将是我们的第一个无状态功能组件，仅将我们的 bucket slug 作为道具:

```
const Header = ({ bucket }) =>
  <nav className="navbar navbar-default">
    <div className="container-fluid">
      <ul className="nav navbar-nav">
        <li className="navbar-text"><strong>Managing Subscriptions for: </strong><em>{bucket}</em></li>
      </ul>
    </div>
  </nav>export default Header
```

**构建订户容器:**

`SubscriberContainer`将处理与我们的用户数据相关的所有逻辑，并呈现`StatsContainer`和`UserList`来显示它处理的数据。

`SubscriberContainer`将是一个包含以下内容的有状态 React 类:

1.  一个构造函数，初始化`SubscriberContainer`的状态以包含我们的宇宙键(我们作为道具传递的)并反映我们的订户数据。我们将收入、用户和取消统计初始化为`'Loading…'`，将它们的加载状态初始化为`true`。
2.  一个对`componentDidMount()`的覆盖，让我们的组件在装载到 DOM 后获取我们需要的数据，然后每分钟刷新一次数据。
3.  一个`getRevenue()`方法，通过迭代来自 Cosmic 的活跃用户的订阅类型，并在状态中存储计算的收入，来获取(以公认的黑客方式)我们的收入。
4.  一个`getUsers()`方法从 Cosmic 中获取我们所有的用户，并将他们存储在 state 中的一个数组中，以及他们的总数。
5.  一个`getCancellations()`方法从我们的`Subscriptions`配置对象中获取取消订阅的数量。(稍后，我们将使用 Stripe 的 webhook 更新该数字。)
6.  一个`render()`方法来呈现我们的`Loader`组件(仅当我们获取数据时)、`StatsContainer`和`UserList`。

综上所述，我们有了这个:

```
import { Component } from 'react'
import Cosmic from 'cosmicjs'
import async from 'async'
import _ from 'lodash'
import StatsContainer from './StatsContainer'
import Loader from './Loader'
import UserList from './UserList'const formatter = new Intl.NumberFormat('en-US', {
  style: 'currency',
  currency: 'USD',
  minimumFractionDigits: 2
})export default class App extends Component {

  constructor(props) {
    super(props)
    this.state = {
      cosmic: this.props.cosmic,
      stats: {
        revenue: 'Loading...',
        users: 'Loading...',
        cancellations: 'Loading...'
      },
      users: [],
      fetchingRevenue: true,
      fetchingUsers: true,
    }
  } fetchData() {
    this.getRevenue();this.getUsers();this.getCancellations()
  } componentDidMount() {
    this.fetchData()
    setInterval(() => {
      this.fetchData()
    }, 60000)
  } getRevenue(cosmic) {
    this.setState({ fetchingRevenue: true})
    async.series([
      callback => {
        Cosmic.getObject(this.state.cosmic, { slug: 'subscriptions' }, (err, response) => {
          callback(null, response.object)
        })
      },
      callback => {
        Cosmic.getObjectType(this.state.cosmic, { type_slug: 'users' }, (err, response) => {
          callback(null, response.objects.all)
        })
      }
    ], (err, results) => {
      let subscriptions = results[0], users = results[1];
      let currentStats = this.state.stats
      currentStats.revenue = formatter.format(users.map(user =>
        parseInt(subscriptions.metadata[`${user.metadata.subscription_type}_price`].replace('$', ''))
      )
      .reduce((sum, val) => sum + val))
      this.setState({ stats: currentStats })
      this.setState({ fetchingRevenue: false })
    })
  } getUsers(cosmic) {
    this.setState({ fetchingUsers: true })
    Cosmic.getObjectType(this.state.cosmic, { type_slug: 'users' }, (err, response) => {
      if (err) {
        currentStats = this.state.stats
        currentStats.users = 'Error'
        this.setState({ stats: currentStats })
      } else {
        let currentStats = this.state.stats
        currentStats.users = isNaN(response.total) ? 0 : response.total
        this.setState({ stats: currentStats })
        this.setState({ users: response.objects.all })
        this.setState({ fetchingUsers: false })
      }
    })
  } getCancellations(cosmic) {
    this.setState({ fetchingCancellations: true})
    Cosmic.getObject(this.state.cosmic, { slug: 'subscriptions' }, (err, response) => {
      if (err) {
        currentStats = this.state.stats
        currentStats.users = 'Error'
        this.setState({ stats: currentStats })
      } else {
        let currentStats = this.state.stats
        currentStats.cancellations = isNaN(response.object.metadata.cancellations) ? 0: response.object.metadata.cancellations
        this.setState({ stats: currentStats })
        this.setState({ fetchingCancellations: false })
      }
    })
  } render() {
    return (
      <div className="container">
        <Loader loadingState={this.state.fetchingUsers || this.state.fetchingRevenue || this.state.fetchingCancellations} />
        <StatsContainer stats={this.state.stats} />
        <UserList users={this.state.users}/>
      </div>
    )
  }
}
```

我们现在只剩下四个无状态的功能组件需要构建。这些是:

**1。加载器:**

```
import ReactLoading from 'react-loading'const Loader = ({ loadingState }) =>
  <div className="row" style={{display: loadingState ? 'block' : 'none' }}>
    <div className="col-xs-12">
      <div className="pull-right">
        <ReactLoading height='20px' width='20px' type="spin" color="#444" />
      </div>
    </div>
  </div>export default Loader
```

这利用了便利的`react-loading`包。

**2。StatsContainer:**

```
import StatTicker from './StatTicker'const StatsContainer = ({ stats }) =>
  <div className="row">{Object.keys(stats).map((key, index) =>
      <div key={index} className="col-md-4 text-center"><StatTicker name={key} value={stats[key]} /></div>
    )}
  </div> export default StatsContainer
```

**3。施塔特克尔:**

```
const StatTicker = ({ name, value }) =>
  <div><h3 className="lead text-muted">{name}</h3><h1 className="text-primary">{value}</h1></div>export default StatTicker
```

最后…

**4。用户列表:**

```
const UserList = ({ users, deleteUser }) =>
  <div style={{marginTop: 50 + 'px'}} className="row">
    <div className="col-xs-12">
      <h4 className="pull-left lead">All Users:</h4>
      <table className="table table-responsive table-hover">
        <thead>
          <tr>
            <th>Stripe ID</th>
            <th>First Name</th>
            <th>Last Name</th>
            <th>Email</th>
          </tr>
        </thead>
        <tbody>
          {users.map((user, index) =>
            <tr key={index}>
              <td>{user.metadata.stripe_id}</td>
              <td>{user.metadata.first_name}</td>
              <td>{user.metadata.last_name}</td>
              <td>{user.metadata.email}</td>
            </tr>
          )}
        </tbody>
      </table>
    </div>
  </div> export default UserList
```

# 集成条纹网钩

为了获得我们取消的订户数，我们将在我们的 Cosmic `Subscriptions`对象中更新一个`cancellations`元字段。为此，每次我们在 Stripe 上删除订阅时，都会通过我们的 Express 应用程序从 Stripe 收到一个 webhook。

1.  在 Stripe 中设置 webhooks，并将它们指向您的 Express 应用程序部署到的域。
2.  在`CosmicUserBlog/routes/api.js`处创建一条`api`路线，并在`App.js`处`require`它。
3.  用作用于`req.body`的`switch`语句处理 POST 请求。当 Stripe 向我们发送订阅取消 webhook 时，`req.body.type`将会是`customer.subscription.deleted`。
4.  从 Cosmic 中删除用户对象，从 Cosmic 中获取订阅对象，浅拷贝对象，递增`metadata.cancellations`，然后使用 Cosmic 的 REST API 将更改推送到对象。
5.  回复一个`200`代码，这样 Stripe 就可以确认收到了 webhook。

这是成品:

```
var express = require('express')
var router = express.Router()
var cosmic = require('cosmicjs')
var axios = require('axios')router.post('/', function(req, res) {
  event = req.body
  switch (event.type) {
    case 'customer.subscription.deleted':
      cosmic.deleteObject(req.app.locals.config, { slug: 'user', write_key: req.app.locals.config.bucket.slug }, function (err, response) {
        cosmic.getObject(req.app.locals.config, { slug: 'subscriptions' }, function (err, response) {
          var currentObject = response.object
          currentObject.metadata.cancellations = currentObject.metadata.cancellations + 1
          currentObject.metafield.cancellations.value = currentObject.metadata.cancellations + 1
          currentObject.write_key = req.app.locals.config.bucket.write_key
          axios({
            method: 'put',
            url: `https://api.cosmicjs.com/v1/${req.app.locals.config.bucket.slug}/edit-object`,
            data: currentObject
          }).then(function (axRes) {
            console.log('Success')
          }).catch(function (axError) {
            console.log('Error')
          })
        })
      })
      return res.json({ received: true})
      break;
    default:
      return res.json({ received: false })
  }
});module.exports = router
```

# 部署

要告诉 Cosmic 你的扩展名是什么，你需要将`extension.json`添加到你的`dist`文件夹中。我们将这样配置我们的扩展:

```
// dist/extension.json{
  "title": "Subscription Management",
  "font_awesome_class": "fa-gears",
  "image_url": ""
}
```

# 结论

使用 Cosmic JS、Express、Stripe 和 React，我们建立了一个可盈利的博客，让我们的读者订阅阅读优质内容，以及一个方便的仪表板来查看我们博客的数据。我们已经为安全支付集成了 Stripe，并且我们已经开发了一个应用程序，它可以做我们想做的事情，并有增长空间。

鉴于我们能够如此快速地构建我们的应用程序，以及部署和维护它的简单性，很明显，Cosmic JS 在其 API 优先的内容管理方法中是独一无二的。很明显，CosmisJS 是一个赚钱机器。

*本文原载于* [*宇宙 JS 博客*](https://cosmicjs.com/blog/start-a-premium-content-business-using-nodejs-react-and-cosmic-js-extensions) *。*

Matt Cain 构建了智能 web 应用程序，并介绍了构建这些应用程序所使用的技术。你可以在他的 [*作品集*](http://mattcain.io/) 上了解更多关于他的信息。