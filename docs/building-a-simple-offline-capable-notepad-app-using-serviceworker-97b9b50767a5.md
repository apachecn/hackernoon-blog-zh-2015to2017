# 使用 ServiceWorker 构建一个简单的支持离线的记事本应用程序

> 原文：<https://medium.com/hackernoon/building-a-simple-offline-capable-notepad-app-using-serviceworker-97b9b50767a5>

今天，我们将构建一个渐进式[记事本](https://hackernoon.com/tagged/notepad)应用程序，它可以很好地在离线模式下使用，在所有可用的设备上响应，并在设备上本地保存内容。所以，这个记事本的核心功能是让它离线工作。为了满足这个需求，我们将使用 [ServiceWorkers](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers) ，我将在下面介绍它。

# 向前

# 什么是服务人员？

服务人员是一个在后台运行的脚本，独立于你的网页。它响应事件，包括从其服务的页面发出的网络请求。服务人员的寿命故意设计得很短。当它得到一个事件时就被唤醒，并且只在需要处理它的时候运行。

ServiceWorkers 提供的首要特性是让 webapp 能够离线工作。除此之外，ServiceWorkers 还包括类似[推送通知](https://developers.google.com/web/updates/2015/03/push-notifications-on-the-open-web)和[后台同步](https://developers.google.com/web/updates/2015/12/background-sync)的功能。未来，服务人员将支持定期同步或地理围栏等其他功能。本教程中讨论的核心特性是拦截和处理[网络](https://hackernoon.com/tagged/network)请求的能力，包括以编程方式管理响应缓存。

# 现在，我们来谈谈什么是渐进式 Web App？

*渐进式 web 应用程序使用现代 Web 功能来提供类似应用程序的用户体验。它们从浏览器标签中的页面演变成身临其境的顶级应用，时刻保持着网络的低摩擦。*

*   这意味着一个进步的 web 应用应该是响应性的、独立于连接的、类似应用的、新鲜的、可安装的等等。因此，要使我们的记事本成为一个进步的网络应用，我们需要包括所有上述功能。让我们开始吧。

# 构建记事本

让我们首先在您最喜欢的本地 web 服务器上创建一个名为 Notepad 的文件夹(在我的例子中，我使用了 [XAMPP](https://www.apachefriends.org/index.html) ),并将以下文件添加到其中:

*   `index.html`
*   `sw.js` -我们将在这里编写 ServiceWorker 逻辑
*   清单让你的网络应用在用户的主屏幕上有一个更像本地人的外观。它允许应用程序以全屏模式启动(没有地址栏)，提供对屏幕方向的控制，并且在 Android 上的 Chrome 的最新版本中，支持为地址栏定义[闪屏](https://developers.google.com/web/updates/2015/10/splashscreen?hl=en)和[主题颜色](https://developers.google.com/web/updates/2014/11/Support-for-theme-color-in-Chrome-39-for-Android?hl=en)。它还用于根据前面提到的闪屏和主屏幕图标的大小和密度来定义一组图标。

现在，我们要做的第一件事是当我们的应用程序第一次访问浏览器时注册一个`ServiceWorker`。为此，在根目录下创建一个名为`js`的文件夹，并将文件`app.js`添加到其中，并在该文件中添加以下代码。

以上代码将尝试检查当前浏览器是否支持`ServiceWorker`，如果支持，它将注册一个。注意，我们已经在 register 函数中传递了一个名为`sw.js`的文件，这个文件我们还没有创建。

在根目录下创建一个名为`sw.js`的文件，并在其中添加以下内容。

注意，如果您想要在服务工作器中导入任何外部脚本，您可以使用 importScripts()来完成。在本例中，我们将使用 [cache-polyfill](https://github.com/coonsta/cache-polyfill) ，因为对缓存的支持是有限的。

现在，我们需要缓存第一次注册`ServiceWorker`时想要缓存的所有文件。为了做到这一点，我们将在`var CACHE_VERSION = 'app-v10';`行后添加以下内容:

```
var CACHE_FILES = [
    '/',
    'index.html',
    'js/app.js',
    'css/style.css',
    'favicon.ico',
    'manifest.json'
];
```

所有这些文件都将被缓存以供脱机使用。这里你可以看到我们用我们想要的缓存名(在我们的例子中是`CACHE_VERSION` )调用`caches.open()`，之后我们调用 cache.addAll()并传入我们的文件数组，即`CACHE_FILES`。

接下来，我们将把以下内容添加到文件`manifest.json`中。

您可以在这里看到，我们在`short_name`中提供了我们的应用程序名称，应用程序的默认方向是`standalone`，我们还提供了我们应用程序的不同大小的图标，您可以从[这里](https://github.com/amitmerchant1990/notepad/tree/master/img)获得。

现在让我们转到`index.html`并添加以下内容:

因此，正如你在这里看到的，我们已经取得了一个文本区，并给了它`id` = `note`，我们将使用它来跟踪文本区的`onKeyUp`事件。为此，我们将使用 [jQuery](https://jquery.com/) 。我们还要注意，为了让应用程序在所有设备上都响应，我们使用了 [Bootstrap](http://getbootstrap.com/) 。我已经在`index.html`中包含了所有必要的文件。你可以从[这里](https://github.com/amitmerchant1990/notepad/tree/master/js)和[这里](https://github.com/amitmerchant1990/notepad/tree/master/css)得到所有需要的文件，并将它们添加到相关的文件夹中。我还包含了文件 [style.css](https://github.com/amitmerchant1990/notepad/blob/master/css/style.css) ，该文件将对页面进行一些必要的修改，使其具有响应性。

现在，再次移动到文件`js/app.js`并添加以下内容:

正如你所看到的，我们有 textarea 的事件，这样它将在用户输入时获取文本，并将其保存到“本地存储”

```
if(localStorage.getItem("note") && localStorage.getItem("note")!=''){
    var noteItem = localStorage.getItem("note")
    $('#note').val(noteItem);
  }
```

以上代码将检查`localStorage`中是否有任何内容，如果有，将在用户下次访问网站时用可用内容填充`textarea`。

最后，再次移动到`sw.js`文件，并将所有剩余文件添加到`CACHE_FILES`中。

```
var CACHE_FILES = [
    '/',
    'index.html',
    'js/app.js',
    'js/jquery.min.js',
    'js/bootstrap.min.js',
    'css/bootstrap.min.css',
    'css/style.css',
    'manifest.json',
    'img/icon-48.png',
    'img/icon-96.png',
    'img/icon-144.png',
    'img/icon-196.png'
];
```

把上面所有的零碎东西放在正确的地方，我们的记事本应用程序现在可以离线使用了。前往 [http://localhost](http://localhost/) 或相关的本地 web 服务器 URL，并检查最终的应用程序。

你可以查看整个[代码库](https://github.com/amitmerchant1990/notepad)或者查看[演示](https://www.amitmerchant.com/notepad/)。

或者你可以在这里看到它的动作[。](https://twitter.com/amit_merchant/status/756876111959601152)

发现任何错别字或想要贡献？通过分叉和发送您的修复和建议来帮助我们改进我们的文档。[改进本页！](https://github.com/amitmerchant1990/amitmerchant1990.github.io/blob/master/_posts/2016-11-15-Building-Simple-Offline-Notepad-Using-Service-Worker.md)

*感谢阅读！*

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[在脸书](http://bit.ly/HackernoonFB)上给我们点赞/发消息，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！