# 使用 Ractive.js 和 HTML5 Fetch API 消费 REST API

> 原文：<https://medium.com/hackernoon/using-ractive-js-and-html5-fetch-api-to-consume-rest-api-f03c47904202>

![](img/76eb63c10f9e8e6e6266d889d75438cc.png)

credit: [https://burst.shopify.com/photos/developer-reviewing-javascript](https://burst.shopify.com/photos/developer-reviewing-javascript)

最近我接到一个任务，使用 [Ractive.js](http://www.ractivejs.org/) 和 [HTML5 的 Fetch API](https://developer.mozilla.org/en/docs/Web/API/Fetch_API) 从 [Open Weather](http://openweathermap.org/api) 中使用一个[Open](http://openweathermap.org/)rest API

这里的示例[应用程序](http://jsfiddle.net/xameeramir/boe2vcj6/embedded/result/#Result)将城市名称作为输入，并在其下方显示相应的天气信息。

# 免费获取 OpenWeather API 密钥！！！

为了从 OpenWeather 的 REST API 中获取 JSON 数据，我们需要做的第一件事就是获取他们的[免费 API key](http://openweathermap.org/appid) 。只需将这个应用程序 id 作为查询参数传入，就可以调用 API。就是这样！

# 设置 UI 容器

创建一个 HTML5 元素作为应用程序的前端。在我们的例子中，是`div#container`。

# 使用引导实现响应

只是为了实现响应，我们已经包括了[引导参考](https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css)。类`.row .col-md-6 .col-sm-12` 负责响应本身。

# 设置活动模板

在添加了 [ractive 的链接](http://cdn.ractivejs.org/latest/ractive.min.js)之后，我们已经确保了小提琴中的代码能够感知 ractive。

模板在脚本标签中。注意，我们已经将`script`的类型属性设置为‘text/ractive’——尽管它可以是除‘text/[JavaScript](https://hackernoon.com/tagged/javascript)之外的任何内容。我们可以通过多种方式加载模板。为了方便起见，我们将把它包含在一个脚本标记中，这样我们就不需要使用 AJAX 或多行字符串了。

例如，花括号中包含了 ractive 函数中的数据集，例如`ractive.set('location', JSON.stringify(data.name) + ', ' + JSON.stringify(data.sys.country));`

# 设置活动上下文

`Ractive`构造函数将用于设置活动上下文。参数及其描述如下:

*   `el`:前端要渲染到的元素
*   `template`:活性模板
*   `data`:前端数据

# 捕捉事件

Ractive events 看起来与纯粹的 [HTML](https://hackernoon.com/tagged/html) 事件略有不同，例如点击事件。它们在某种意义上与 jQuery 类似。

# 启动获取请求

使用`Request`构造函数准备获取请求，并使用`fetch(request)` 触发它。

# 使用 localStorage 维护最近的搜索历史

城市输入以数组的形式存储在 localStorage 实例中，并显示为`ul`的`li`，每次单击都会显示天气获取请求摘要。

见[全码](http://jsfiddle.net/xameeramir/boe2vcj6/)此处。

**来源:**

*   [获取 API](https://developer.mozilla.org/en/docs/Web/API/Fetch_API)
*   [开放天气图](http://openweathermap.org/appid)
*   [行动](http://www.ractivejs.org/)
*   [我的博客](http://xameeramir.github.io/Ractive.js-HTML5-Fetch-API-consume-REST-API/)