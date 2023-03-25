# 如何用 Ember.js 搭建一个房产网站

> 原文：<https://medium.com/hackernoon/how-to-build-a-real-estate-website-using-ember-js-ef4ed7ae3806>

![](img/7701f317276c69a43f589d84532913c1.png)

今天的互联网很复杂。战争是从 React 状态管理开始的，新语言的编写只是为了让 Angular 愉快地工作，而不是从所有对 Node 的憎恨开始。

在围绕新技术的狂热中，简单被遗忘了。像 [Cosmic JS](https://cosmicjs.com) 这样的服务挑战了这一点。因此，本着反其道而行之的精神，我们将构建一个易于使用的时尚的房地产列表应用程序(带有社交风格)。

# TL；速度三角形定位法(dead reckoning)

[查看演示](https://cosmicjs.com/apps/real-estate-website)
[查看 GitHub 上的完整代码库](https://github.com/cosmicjs/ember-real-estate-website)

# 入门指南

我们将使用 Cosmic JS 来管理我们所有的内容和部署网站。我们将在 Ember.js 中构建前端。

我们用 Ember 是因为它固执己见。我们的应用不会对任何现代设备上的性能问题敏感，所以我们可以利用符合我们设计理念的高层次抽象。

# 设置应用程序

假设您已经安装了 Node，打开终端并运行每个步骤中的命令。

0.设置快速服务器

*   创建一个新目录 CosmicRealEstate 作为项目的根目录。
*   在 CosmicRealEstate 中，将目录设为 app(用于存储路由器)和 public(用于存储 [Ember.js](https://cosmicjs.com/blog/Ember.js) 版本)。
*   在 CosmicRealEstate 下创建文件 [server.js](https://cosmicjs.com/blog/server.js) ，内容如下:

```
var express = require('express');
var app = express();
var port = process.env.PORT || 3000;app.use(express.static(__dirname + '/public'));require('./app/routes')(app);app.listen(port);exports = module.exports = app;
```

*   在 app 目录中创建 [routes.js](https://cosmicjs.com/blog/routes.js) 并添加以下代码:

```
module.exports = function(app) {
       app.get('*', function(req, res) {
           res.sendfile('./public/index.html'); // load our public/index.html file
       });
};
```

*   在终端的 CosmicRealEstate 中，运行:

```
npm init
npm install express --save
```

你知道有一个简单的快速应用程序设置，这将让我们在宇宙部署应用程序。稍后，我们将把 Ember 应用程序构建到公共目录中。在 CosmicRealEstate 中运行以下命令:

1.安装 Ember 的命令行界面

```
npm install -g ember-cli
npm install -g phantomjs
```

(Ember 使用 Phantonjs 进行测试)

2.生成 Ember 项目结构

```
ember new cosmic-real-estate && cd cosmic-real-estate
```

4.安装引导程序

从 comsic-real-estate 目录中，安装 Ember Bootstrap 插件以自动将 Bootstrap 包含在您的构建路径中

```
ember install ember-bootstrap
```

5.启动您的开发服务器

```
ember serve
```

打开你的浏览器，你会在 localhost:4200 看到 Ember 欢迎页面。

# 建立你的物品路线

现在您已经有了应用程序的样板代码，让我们开始构建一个页面来显示我们的 Cosmic JS bucket 中的所有列表。

0.去掉欢迎代码

我们感谢 Ember 的热情欢迎，但我们需要去工作了。继续从您的应用程序的主手柄模板中删除以下代码。

```
{{!-- app/templates/application.hbs --}}{{!-- The following component displays Ember's default welcome message. --}}
{{welcome-page}}
{{!-- Feel free to remove this! --}}
```

现在，在剩余的{{outlet}}(这是您的路线将呈现的位置)周围添加一个容器。您的应用程序模板现在将如下所示:

```
{{!-- app/templates/application.hbs --}}<div class="container" style="margin-top:20px">
    {{outlet}}
</div>
```

1.生成列表路线，并给你的模板一个欢迎垫

在 cosmic-real-estate 目录中，运行:

```
ember g route listings
```

然后，在新创建的列表模板中，添加以下 HTML:

```
<div class="jumbotron" style="margin-top: 20px">
  <h1>Welcome to Cosmic Real Estate!</h1>
  <p>
    Check out our awesome real estate listings -
    stored and managed with Cosmic JS and
    rendered right in your browser.
  </p>
</div>
```

如果你去 localhost:4200/listings，你会看到我们的列表页面的开始。

2.生成一个索引路径，使您的列表显示在根 URL 上

我们希望将根域转发到我们的列表页面，所以首先运行:

```
ember g route index
```

然后，打开索引的路由处理程序，并将以下代码添加到路由的导出中:

```
// app/routes/index.jsexport default Ember.Route.extend({
  beforeModel() {
    this.replaceWith('listings');
  }
});
```

Ember 在渲染路线时使用一系列生命周期挂钩进行验证。调用 beforeModel()利用这些钩子中的第一个来告诉路由处理器立即将我们带到清单路由。访问 localhost:4200 将把我们带到 localhost:4200/listings。

# 从 Cosmic JS 中检索数据

我们希望向用户展示我们存储在 Cosmic JS 中的列表。为此，我们将使用 Ember 的内置数据存储 Ember Data 从 Cosmic JS 中检索列表，并存储在相应的模型中。

0.从现在开始设置你的桶，我们假设你已经创建了一个宇宙 JS 桶。(本指南的水桶鼻涕虫将是宇宙-房地产。)稍后您将在您的 ENV 变量中设置。在 cosmic-real-estate 桶中是具有以下结构的“列表”类型的对象:

## 列表:

宇宙 JS 元域
类型
价格
文本输入
地址
文本输入
个人资料图片
图片
风格
单选
街坊
文本输入
床位
文本输入
浴场
文本输入
平方英尺
文本输入
邮政编码
文本输入

正如本指南开头所强调的，Ember 是约定胜于配置。Ember Data 处理所有你不想做的讨厌的 AJAX 和缓存工作。我们所要做的就是告诉 Ember 在哪里寻找我们的数据，如何解释它，以及它如何适合我们的模型。

1.创建列表模型

与任何其他 web 应用程序框架一样，Ember 中的模型只是充当后端数据的容器。我们将使用一个列表模型来匹配我们的 Cosmic JS 桶中的列表对象。运行:

```
ember g model Listing
```

在新创建的清单文件中，添加以下代码:

```
// app/models/listing.jsexport default DS.Model.extend({
  title: DS.attr(),
  price: DS.attr(),
  address: DS.attr(),
  profileImage: DS.attr(),
  style: DS.attr(),
  neighborhood: DS.attr(),
  beds: DS.attr(),
  baths: DS.attr(),
  squareFeet: DS.attr(),
  zipCode: DS.attr()
});
```

我们现在已经指定了一个列表将具有哪些属性，也就是说，当从我们的桶中抓取数据时要查找哪些属性。

2.创建列表适配器和序列化程序

Cosmic JS 运行在一个直观的 REST API 上，所以我们将利用 Ember Data 内置的 REST 适配器和 JSON 序列化器。我们只需要做一些细微的调整来适应宇宙 API 规范。要生成列表适配器，请运行:

```
ember g adapter listing
```

要生成序列化程序，请执行以下操作:

```
ember g serializer listing
```

Ember 现在将自动使用清单序列化程序和 adaptaer 来处理清单模型。

3.调整列表适配器

默认情况下，Ember 的 REST 适配器指向的端点与我们从桶中获取数据所需的端点略有不同。因此，将下面的代码添加到清单适配器中，确保您换出了 DS。JSONAPIAdapter for DS。重新适配:

```
// app/adapters/listing.jsexport default DS.RESTAdapter.extend({
  host: 'https://api.cosmicjs.com/v1/cosmic-real-estate',
  urlForFindAll(modelName, snapshot) {
    let path = this.pathForType(modelName);
    return this.buildURL() + '/object-type/' + path;
  },
  urlForQueryRecord(slug) {
    return this.buildURL() + '/object/' + slug;
  }
});
```

这样做的效果是，每次我们在数据存储中查询清单时，我们告诉 Ember 从响应中获取清单，这些响应位于[https://API . cosmicjs . com/v1/cosmic-real-esate/object-type/listing](https://api.cosmicjs.com/v1/cosmic-real-esate/object-type/listing)或[https://API . cosmicjs . com/v1/cosmic-real-esate \ object \ listing-slug，](https://api.cosmicjs.com/v1/cosmic-real-esate/object/listing-slug,)，这取决于我们是检索单个清单还是多个清单。

4.调整列表序列化程序

Cosmic JS 给我们的数据格式与 Ember 的 REST Serializer 所期望的略有不同。例如，宇宙返回这个

```
{
  "objects": [
    {
      "_id": "59403aefacfbc8d252000563",
      "slug": "example-condo",
      "title": "Example Condo",
      "content": "",
      "bucket": "593f696bacfbc8d2520000df",
      "type_slug": "listings",
      "created_at": "2017-06-13T19:20:15.485Z",
      "created_by": "593f691ea768e28650000334",
      "created": "2017-06-13T19:20:15.485Z",
      "status": "published",
      "metadata": {
        "price": 545000,
        "address": "2100 High Avenue",
        "profile": {
          "url": "https://cosmicjs.com/uploads/4ff36ce0-506d-11e7-9f7d-dbfbf1c66da0-ex-condo.jpeg",
          "imgix_url": "https://cosmicjs.imgix.net/4ff36ce0-506d-11e7-9f7d-dbfbf1c66da0-ex-condo.jpeg"
        },
        "style": "Condo",
        "location": "Uptown"
      }, 
      // etc...
```

Ember 期望:

```
{
    "listings": [
        {
            "id": 1,
            "title": "example-condo",
            "price": 54000,
            // etc...
```

这里的两个主要差异是对象与我们的模型名不匹配，并且我们需要的大多数主要属性都存储在元数据中。要解决这个问题，请将以下代码添加到清单序列化程序中，确保交换 DS。DS.RESTSerializer 的 JSONAPISerializer。

```
// app/serializers/listing.jsimport DS from 'ember-data';function buildNormalizeListing(source) {
  return {
    id: source._id,
    slug: source.slug,
    content: source.content,
    title: source.title,
    price: source.metadata.price,
    address: source.metadata.address,
    profileImage: source.metadata.profile.url,
    style: source.metadata.style,
    neighborhood: source.metadata.neighborhood,
    beds: source.metadata.beds,
    baths: source.metadata.baths,
    squareFeet: source.metadata.square_feet,
    zipCode: source.metadata.zip_code }
}export default DS.RESTSerializer.extend({
  normalizeResponse(store, primaryModelClass, payload, id, requestType) {
    if (payload.objects) {
      let normalizedListings = payload.objects.map(function(listing) {
        return buildNormalizeListing(listing)
      });
      payload = {
        listings: normalizedListings
      };
    } else {
      let normalizedListing = buildNormalizeListing(payload.object);
      payload = {
        listing: normalizedListing
      }
    }
    return this._super(store, primaryModelClass, payload, id, requestType); }
});
```

对于由 Cosmic JS 返回的 objects 数组中的每个列表，我们创建一个新对象，只包含我们想要的属性，并将这些属性映射到一个新数组。然后，我们创建一个新的散列，用 Ember 正在寻找的列表键来指向我们的有效负载引用。通过调用 _super 将它们打包在一起，我们的应用程序就可以和 Cosmic JS 做生意了。(检索单个列表的过程类似，所以我们将其抽象为 buildNormalizedListing())

# 显示列表

我们希望我们的用户能够查看单个列表，以获得关于它们的更详细的信息。为此，我们需要利用 Ember 的嵌套路由。

1.在列表下生成索引路径

列表/索引路径将取代我们原来的列表路径。运行:

```
ember g route listings/index
```

2.移动列表模板代码和路线代码

将以下代码片段从 app/templates/listings.hbs 移动到 app/templates/listings/index . HBS

```
{{!-- app/templates/listings.hbs --}}<div class="row">
  {{#each model as |currentListing|}}
    <div class="col-sm-4" >
       {{real-estate-listing listing=currentListing}}
    </div>
  {{/each}}
</div>
```

在列表模板中删除最后一点的地方，添加{{outlet}}。也就是说， [listings.hbs](https://cosmicjs.com/blog/listings.hbs) 现在看起来是这样的:

```
{{!-- app/templates/listings.hbs --}}<div class="container">
  <div class="row">
    <div class="col-md-12">
      <div class="jumbotron" style="margin-top: 20px">
        <h1>Welcome to Cosmic Real Estate!</h1>
        <p>
          Check out our awesome real estate listings -
          stored and managed with Cosmic JS and
          rendered right in your browser.
        </p>
      </div>
    </div>
  </div>
</div><div class="container">
  {{outlet}}
</div>
```

{{outlet}}帮助器只是指定子路由在模板中的呈现位置。

最后，通过将以下代码片段从 app/routes/listings.js 移动到 app/routes/listings/index.js，使呈现所有列表成为特定于索引路由的行为。

```
model() {
    return this.get('store').findAll('listing');
  }
```

3.为个别物品建立刊登路线

首先，运行:

```
ember g route listings/listing
```

当我们打开一个单独的列表时，我们希望它在 url 中得到反映，所以将下面的代码添加到路由器中，以在 URL 的末尾显示列表 id。

```
// app/router.jsRouter.map(function() {
  this.route('listings', function() {
    this.route('listing', { path: '/:listing_id'});
  });
});
```

4.准备列表模板以显示数据

在我们让用户签出清单之前，我们需要给他们一些可以看的东西。将以下 HTML 添加到列出的子路由中:

```
<div class="row">
  <div class="col-sm-12">
    <ul class="nav nav-pills">
      <li role="presentation"><a href="#description">Description</a></li>
      <li role="presentation"><a href="#stats">Stats</a></li>
      <li role="presentation">{{#link-to 'listings'}}Return to Listings{{/link-to}}</li>
    </ul>
  </div>
  <div class="col-sm-9">
    <div class="page-header">
      <h1>
        {{model.title}}
      </h1>
    </div>
  </div>
  <div class="col-sm-3">
      <h1>
        <span class="text-success">{{format-price model.price}}</span>
      </h1> </div>
</div>
<div class="row">
  <div class="col-md-6">
    <img src={{model.profileImage}} class="img-responsive" />
  </div>
  <div class="col-md-6">
      <ul class="list-group">
      <li class="list-group-item"><strong>Address:</strong><span class="pull-right">{{model.address}}</span></li>
      <li class="list-group-item"><strong>Style:</strong><span class="pull-right">{{model.style}}</span></li>
      <li class="list-group-item"><strong>Neighborhood:</strong><span class="pull-right">{{model.neighborhood}}</span></li>
      <li class="list-group-item"><strong>Beds:</strong><span class="pull-right">{{model.beds}}</span></li>
      <li class="list-group-item"><strong>Baths:</strong><span class="pull-right">{{model.baths}}</span></li>
      <li class="list-group-item"><strong>Square Feet:</strong><span class="pull-right">{{model.squareFeet}}</span></li>
      </ul>
  </div> <div class="col-sm-6">
    <h3 id="description">Description</h3>
    <span class="text-muted">{{{model.content}}}</span>
  </div>
</div>
```

请注意我们正在使用的三个新的手柄特性:在{{{model.content}}}中使用三重括号强制手柄不要在内容字段中对 HTML 进行转义。菜单中的{ { # link-to } }帮助器会自动为我们生成一个正确的链接，链接到我们指定的路线。我们将在下一步中使用它来实际动态链接到清单。*我们接下来将制作的{{format-price}}助手。

5.生成一个助手来格式化价格

在 Ember 中，我们可以选择生成自定义助手。我们这样做是为了以货币形式显示列表的价格。运行:

```
ember g helper filter-price
```

然后，在新格式的价格帮助器中，添加以下代码:

```
// app/helpers/format-price.jsexport function formatPrice(value) {
  return '$' + value.toLocaleString();
}
```

6.链接到列表

我们需要从列表的 slug 中动态生成一个到列表的特定页面的链接。我们可以使用上一步中使用的{{#link-to}}}助手轻松地完成这项工作。

在房地产列表组件中，将{{listing.title}}包装在{{#link-to}}中，如下所示:

```
{{!-- app/templates/components/real-estate-listing.hbs --}{{#link-to 'listings.listing' listing}}{{listing.title}}{{/link-to}}
```

我们将模型作为第二个参数传递，Ember 自动负责匹配路线。然而，你可能会注意到，我们的链接现在指向一些无意义的字符串，Ember 将这些字符串用作列表的唯一 ID。作为一个简单的解决方案，进入您的列表序列化程序，添加以下代码行来告诉 Ember 使用列表 slug 作为它的主键:

```
// app/serializers/listing.jsexport default DS.RESTSerializer.extend({
  primaryKey: 'slug',
  normalizeResponse(store, primaryModelClass,
  // etc...
```

我们现在有了单独的路线列表和链接，正如我们所期望的那样。

# 让它社交化

在这一点上，我们有自己的一个功能正常的房地产上市应用程序。但是，我们不会以一些无聊的东西结束，我们会增加一点趣味。我们希望我们的用户能够(在这种情况下匿名)对我们的清单投票。宇宙 JS 将帮助我们管理这一点。

1.更新 Cosmic JS bucket 中的列表对象、模型和序列化程序，将缺省值为 0 的元字段 upvotes 添加到列表对象中。相应地更新列表模型:

```
// app/models/listing.js//etc...
  zipCode: DS.attr(),
  content: DS.attr(),
  upvotes: DS.attr()
});
```

*   更新列表序列化程序中的 buildNormalizedListing()

```
// app/serializers/listing.jsfunction buildNormalizeListing(source) {
  return {
    //etc...
    zipCode: source.metadata.zip_code,
    upvotes: source.metadata.upvotes
  }
}
```

2.更新房地产列表组件

添加了一个向上投票按钮、一个向下投票按钮和一个投票计数器后，您的房地产列表模板将如下所示:

```
{{!-- app/templates/components/real-estate-listing.hbs --}}<div class="panel panel-default">
  <div class="panel-heading">
    <h4>
      {{#link-to 'listings.listing' listing}}{{listing.title}}{{/link-to}}
      <span class="pull-right">
        {{upvotes}}
      </span>
    </h4>
  </div>
  <div class="panel-body">
    <p>
      <img src="{{listing.profileImage}}" class="img-thumbnail" style="background-color: #fff;height:auto"/> </p>
    <p>
      <strong>Address:</strong> {{listing.address}}
    </p>
    <p>
      <strong>Style:</strong> {{listing.style}}
    </p>
    <p>
      <strong>Neighborhood:</strong> {{listing.neighborhood}}
    </p>
  </div>
  <div class="panel-footer">
    <p class="pull-left">
      <button {{action "vote" "down"}} class="btn btn-default"><span class="glyphicon glyphicon-thumbs-down"></span></button>
      <button {{action "vote" "up"}} class="btn btn-default"><span class="glyphicon glyphicon-thumbs-up"></span></button>
    </p>
    <p class="text-right">
       <h4>{{format-price listing.price}}</h4>
    </p>
  </div>
</div>
```

值得注意的是，我们添加了一个拇指向上和拇指向下按钮，利用 Ember 的动作助手来处理点击。我们通过“vote”来指定要调用的动作，通过“up”或“down”来指示投票的方向。为了让按钮工作，我们需要在房地产列表控制器中实现 vote()动作。

3.实施投票操作

为了处理投票操作，我们首先来看一下房地产列表组件的控制器。

在这个过程中，我们的第一步是让控制器访问列表的 slug 和 upvotes。我们通过计算属性来实现这一点，如下所示:

```
// app/components/real-estate-listing.jsexport default Ember.Component.extend({
  slug: Ember.computed('slug', function() {
    return this.get('listing.id');
  }), //etc...
```

注意，我们使用 [listing.id](https://cosmicjs.com/blog/listing.id) 来检索 slug，因为我们已经将 slug 定义为模型的主键。

其次，我们添加属性 store: Ember.inject.service()来访问组件中的数据存储。

最后，我们在操作散列中定义了 vote()操作:

```
// app/componenets/real-estate-listing.jsactions: {
    vote(direction) {
      let upvotes = this.get('upvotes');
      direction === 'up' ?
        upvotes++ :
        upvotes--;
      this.set('upvotes', upvotes);
      let slug = this.get('slug');
      let store = this.get('store');
      updateVote(upvotes,slug,store);
    }
}
```

(updateVote()在导出之上实现)

综上所述，您的房地产列表组件将如下所示:

```
import Ember from 'ember';function updateVote(upvotes,listing_slug,store) {
  store.findRecord('listing', listing_slug).then(function(listing) {
    listing.set('upvotes',upvotes);
    listing.save();
  })
}export default Ember.Component.extend({
  store: Ember.inject.service(),
  upvotes: Ember.computed('upvotes', function() {
    return this.get('listing.upvotes');
  }),
  slug: Ember.computed('slug', function() {
    return this.get('listing.id');
  }),
  actions: {
    vote(direction) {
      let upvotes = this.get('upvotes');
      direction === 'up' ?
        upvotes++ :
        upvotes--;
      this.set('upvotes', upvotes);
      let slug = this.get('slug');
      let store = this.get('store');
      updateVote(upvotes,slug,store);
    }
  }
});
```

按钮现在增加/减少投票计数，但是如果你刷新页面，你会注意到变化没有持续。如果你检查一下控制台，你就会知道为什么。

接下来，我们需要修改我们的老朋友适配器和序列化器来处理我们的更新请求。

4.(再次)调整序列化程序

复制以下代码以覆盖 serialize()方法:

```
// app/serializers/listing.jsexport default DS.RESTSerializer.extend({
  primaryKey: 'slug',
  normalizeResponse(store, primaryModelClass, payload, id, requestType) {
    // etc...
  },
  serialize(snapshot, options) {
    var json = this._super(...arguments);
    let payload = {
      "slug": snapshot.id,
      "metafields": [
        {
            "required": true,
            "value": json.price,
            "key": "price",
            "title": "Price",
            "type": "text",
            "children": null
        },
        {
            "required": true,
            "value": json.address,
            "key": "address",
            "title": "Address",
            "type": "text",
            "children": null
        },
        {
            "value": json.profileImage.split('/').slice(-1)[0],
            "key": "profile",
            "title": "Profile",
            "type": "file",
            "children": null,
            "url": json.profileImage,
            "imgix_url": "https://cosmicjs.imgix.net/" + json.profileImage.split('/').slice(-1)[0];
        },
        {
            "required": true,
            "options": [
                {
                    "value": "House"
                },
                {
                    "value": "Apartment"
                },
                {
                    "value": "Condo"
                }
            ],
            "value": json.style,
            "key": "style",
            "title": "Style",
            "type": "radio-buttons",
            "children": null
        },
        {
            "value": json.beds,
            "key": "beds",
            "title": "Beds",
            "type": "text",
            "children": null
        },
        {
            "value": json.baths,
            "key": "baths",
            "title": "Baths",
            "type": "text",
            "children": null
        },
        {
            "value": json.squareFeet,
            "key": "square_feet",
            "title": "Square Feet",
            "type": "text",
            "children": null
        },
        {
            "required": true,
            "value": json.neighborhood,
            "key": "neighborhood",
            "title": "Neighborhood",
            "type": "text",
            "children": null
        },
        {
            "required": true,
            "value": json.zipcode,
            "key": "zipcode",
            "title": "zipcode",
            "type": "text",
            "children": null
        },
        {
            "value": json.upvotes,
            "key": "upvotes",
            "title": "Upvotes",
            "type": "text",
            "children": null
        }
    ]
    }
    return payload;
  }
});
```

5.再次调整适配器

在适配器的导出中，我们需要覆盖 urlForUpdateRecord()和 UpdateRecord()。一旦我们这样做了，我们就有:

```
// app/adapters/listing.jsimport DS from 'ember-data';export default DS.RESTAdapter.extend({
  host: 'https://api.cosmicjs.com/v1/cosmic-real-estate',
  urlForFindAll(modelName, snapshot) {
    let path = this.pathForType(modelName);
    return this.buildURL() + '/object-type/' + path;
  },
  urlForFindRecord(slug) {
    return this.buildURL() + '/object/' + slug;
  },
  urlForUpdateRecord() {
    return this.buildURL() + '/edit-object';
  },
  updateRecord: function(store, type, snapshot) {
    var data = {};
    var serializer = store.serializerFor(type.modelName); serializer.serializeIntoHash(data, type, snapshot);
    data = data.listing;
    var id = snapshot.id;
    var url = this.buildURL(type.modelName, id, snapshot, 'updateRecord'); return this.ajax(url, "PUT", { data: data });
  }
});
```

# 让一切看起来都很好

随着我们的投票功能的加入，我们将添加一些功能，以便在完成之前将应用程序整合在一起。

1.对投票进行筛选

正如我们对价格所做的一样，我们将制作一个过滤器来使投票看起来漂亮。运行:

```
ember g helper format-upvotes
```

然后，在新创建的 Format Upvotes 过滤器中，添加以下内容(替换默认参数):

```
// app/helpers/format-upvotes.jsexport function formatUpvotes(upvote) {
  if (upvote > 0) {
    return '<span class="text-success">' + upvote + '</span>';
  } else if (upvote < 0) {
    return '<span class="text-danger">' + upvote + '</span>';
  } else {
    return '<span class="text-muted">' + upvote + '</span>';
  }
}
```

若要使其正常工作，请将房地产列表模板中的{{upvotes}}替换为{{{format-upvotes upvotes}}}。(记住，三重括号告诉车把不要对返回值进行转义。)

2.将向上投票计数添加到单个列表中

在个人列表模板中，其中{{model.title}}是，添加:

```
{{!-- app/templates/listings/listing.hbs --}}<div class="page-header">
  <h1>
    {{model.title}} <small>Upvotes: {{{format-upvotes model.upvotes}}}</small>
  </h1>
</div>
```

# 部署应用程序

0.拼凑一个端点来提供 Express 中的密钥

假设您正在使用 Cosmic JS 来部署您的应用程序，您将通过 Node 的 process.env 自动访问 bucket 的 slug、read key 和 write key。然而，由于我们已经完全隔离了我们的前端，并且仅使用 Express 来服务它，因此使这些 env 变量可访问并不是最简单的任务。

我们将通过 Express 服务器上的一个端点为他们提供服务。我们将在清单适配器中用一个简单的 AJAX 调用来检索它们，因为我们在其他地方不需要它们。(请注意，我们不会安全地这样做，但是添加身份验证不会有太多工作。)

现在从 CosmicRealEstate 开始，我们将使用路由器来服务一个 JSON 对象，该对象包含我们在/api 的键。

```
// CosmicRealEstate/app/routes.jsmodule.exports = function(app) {
    app.get('/api', function(req,res){
      return res.json({
        bucket: process.env.COSMIC_BUCKET,
        writeKey: process.env.COSMIC_WRITE_KEY,
        readKey: process.env.COSMIC_READ_KEY
      });
    });
    app.get('*', function(req, res) {
      var path = require('path');
      res.sendFile(path.join(__dirname, '../public', 'index.html')); // load our public/index.html file
    });};
```

(确保/api 路由在通配符路由之前，以覆盖它)。

然后，回到我们的 Ember 应用程序来处理列表适配器，我们需要做三件事:1。导入 ember 以使我们能够访问 jQuery 2。导入配置文件，以便我们可以检查构建环境 3。对我们刚刚设置的 Express 端点进行 Ajax 调用

一旦我们完成了这一步，清单适配器在导出之前将是这样的(不会改变)。

```
import DS from 'ember-data';
import config from '../config/environment';
import Ember from 'ember';var cosmic;
if (config.environment === "production") {
  Ember.$.ajax('/api', {
    data: {format: 'json'},
    async: false,
    success: function(data) {
      cosmic = {
        bucket: data.bucket,
        writeKey: data.writeKey,
        readKey: data.readKey
      }
    }
  });
} else {
  //config.environment === "development"
  cosmic = {
    bucket: 'cosmic-real-estate',
    writeKey: null,
    readKey: null
  }
}
```

(这里选择使用 async: false 是为了保持简单，尽管这不是最佳实践。这个想法是，在提供密钥之前，应用程序不能做任何事情——没有伤害，就没有犯规。)

1.构建前端

我们的 Ember 应用程序现已准备就绪，密钥也已准备就绪，我们将通过构建它来开始部署过程。在 cosmic-real-estate 的终端中，运行:

```
ember build --environment=production --output-path=../public/
```

我们指定了我们的构建环境，并使用父节点应用程序的公共目录作为构建路径。

2.测试节点应用程序

导航到 CosmicRealEstate 并运行:

```
node server.js
```

你应该在 localhost:3000 上看到你的应用程序

# 结论

使用 Cosmic JS、Express.js 和 Ember.js，您刚刚为自己构建了一个模块化的应用程序，您可以向您选择的任何方向扩展。

如果你坚持我们的设计理念，将应用程序部署到 Cosmic JS，你现在可以从一个地方完全管理你的网站及其内容。如果你认为任何不是 API first 的东西都比周一更糟糕，不能忍受对数据库的担心，并试图尽可能快地以最少的担心交付你的内容，你会发现 Cosmic JS 是你的快乐之地。

> 这篇文章最初出现在宇宙 JS 博客上。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)