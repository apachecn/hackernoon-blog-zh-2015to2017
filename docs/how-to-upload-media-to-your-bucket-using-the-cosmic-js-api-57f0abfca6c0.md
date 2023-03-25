# 如何使用 Cosmic JS API 将媒体上传到您的存储桶

> 原文：<https://medium.com/hackernoon/how-to-upload-media-to-your-bucket-using-the-cosmic-js-api-57f0abfca6c0>

![](img/48d21e598f59049c4ed90280099d1b70.png)

> 托尼·斯皮罗是 Cosmic JS T1 的联合创始人，这是一个 API 优先的 T2 内容管理平台 T3，用于构建基于内容的应用程序。

在这个简短的教程中，我将向你展示如何:
1。通过 Cosmic JS API 上传一张图片到你的 Cosmic JS 桶。
2。使用强大的 [Imgix](https://www.imgix.com/) 处理服务(每个宇宙 JS 桶免费提供)
3 改变你的图像。将上传的图像作为元字段添加到存储桶中的对象

# 入门指南

注册 Cosmic JS 来创建你自己的桶，或者只是跟随例子。

首先，让我们创建一个简单的 HTML 表单来上传媒体。

```
<html>
  <body>
    <form action="https://api.cosmicjs.com/v1/bucket-slug/media" method="post" enctype="multipart/form-data">
      <input type="file" name="media" />
      <input type="submit" value="Upload!" />
    </form>   
  </body>
</html>
```

接下来，让我们添加一个文件，让它撕裂。请注意，我们现在有了用于数据提取/存储的数据:

```
{
  "media": {
    "name": "57319310-b914-11e6-acef-b1b7e94fa195-earth.png",
    "original_name": "earth.png",
    "size": 1024912,
    "type": "image/png",
    "bucket": "5839c67f0d3201c114000004",
    "created": "2016-12-03T04:52:49.516Z",
    "location": "https://cosmicjs.com/uploads",
    "url": "https://cosmicjs.com/uploads/57319310-b914-11e6-acef-b1b7e94fa195-earth.png",
    "imgix_url": "https://cosmicjs.imgix.net/57319310-b914-11e6-acef-b1b7e94fa195-earth.png",
  }
}
```

# Imgix 的力量

在我们将媒体发布到我们的 Cosmic JS Bucket 之后，返回的数据片段之一是一个 Imgix URL。Imgix 为我们提供了强大的动态图像处理能力。只需在 Imgix url 的末尾添加查询参数，我们就可以对上传的图像进行调整大小、裁剪、添加过滤器等操作。例如，要将此图像调整为两倍移动大小，只需添加“？w=640 "到 URL 的结尾:

```
[https://cosmicjs.imgix.net/57319310-b914-11e6-acef-b1b7e94fa195-earth.png?w=640](https://cosmicjs.imgix.net/57319310-b914-11e6-acef-b1b7e94fa195-earth.png?w=640)
```

这使得创建轻量级响应网站更加容易，因为现在我们不必为不同的设备上传多个图像。我们现在所做的就是将 url 编程为我们想要的大小。浏览 [Imgix 文档](https://docs.imgix.com/?_ga=1.63747439.195266109.1474515080)查看所有触手可及的功能。Imgix 免费包含在每个 Cosmic JS 帐户中。

# 将上传的图像添加到对象

在将我们的图像添加到我们的宇宙 JS 桶之后，我们可能想要将它添加到一个对象。为此，我们只需获取发布媒体后返回的“name”值，并将其添加为文件元字段值，然后将其发布到我们的 Add Object API 端点:

```
POST /v1/:your-bucket-slug/add-object{
  "title": "New Object",
  "slug": "new-object",
  "type_slug": "tests",
  "content": "",
  "metafields": [{
    "type": "file",
    "title": "Image",
    "key": "image",
    "value": "57319310-b914-11e6-acef-b1b7e94fa195-earth.png"
  }]
}
```

现在，当我们进入我们的 Cosmic JS Bucket 时，我们会注意到“Tests”对象类型有一个名为“new Object”的新对象，我们新上传的文件作为文件元字段。

# 结论

Cosmic JS 使向任何网站或应用程序添加动态内容变得非常容易。在本教程中，我向您展示了如何使用 Cosmic JS API 添加媒体，使用可用的 Imgix 处理 URL 处理图像，然后将该图像保存到我们的 Bucket 中的一个对象，以便以后编辑。要了解更多关于 Cosmic JS 如何让您的网站和应用程序轻松存储媒体和 power 内容，请查看[文档页面](https://cosmicjs.com/docs)。

我希望你喜欢这个教程。如果您有任何问题，或者想了解更多关于 Cosmic JS 如何帮助您更快、更容易地构建基于内容的应用程序的信息，请[在 Slack 上加入 Cosmic JS](https://cosmicjs.com/community)和[在 Twitter 上联系我们](https://twitter.com/cosmic_js)。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)