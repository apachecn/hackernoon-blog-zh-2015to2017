# 使用 Bitbucket 管道构建和发布 Cosmic JS 扩展

> 原文：<https://medium.com/hackernoon/building-and-publishing-a-cosmic-js-extension-using-bitbucket-pipelines-c170e30b01e>

![](img/6dcca714eede9631fb5e28041a22844f.png)

Cosmic JS 刚刚发布了他们新的扩展功能，这使得任何人都可以在 Cosmic CMS 中嵌入他们自己的定制应用程序！

在本教程中，我们将经历一些必要的步骤，使我们的亚马逊产品搜索扩展。

# TL；速度三角形定位法(dead reckoning)

[查看 GitHub 上的完整代码库](https://github.com/cosmicjs/amazon-product-search)
[在您的存储桶中安装扩展在您的存储桶中>扩展>浏览扩展](https://cosmicjs.com/login)
[查看扩展文档](https://cosmicjs.com/docs/extensions)了解关于构建扩展的更多信息

# 我们正在建造的扩建部分

我们正在构建一个简单的扩展，允许您搜索亚马逊产品目录，并将带有产品名称、描述、图像和附属链接的对象添加到您的存储桶中。

这是一个非常简单的应用程序，使用 react 构建，我们不会对与新的 Cosmic JS Extensions API 没有直接关系的部分进行过多的详细描述。

# 在你的扩展中获得对宇宙 API 的访问

Cosmic JS 将扩展作为一个`<iframe>`嵌入到其 CMS 接口中，扩展被赋予一个带有查询字符串的 url，该字符串提供了应用程序访问相关桶所需的所有信息，如下所示:

`[https://43d32000-5ce7-11e7-8fc4-c1f6eec4f920.cosmicext.com/?bucket_slug=my-bucket-slug&read_key=foo&write_key=bar](https://43d32000-5ce7-11e7-8fc4-c1f6eec4f920.cosmicext.com/?bucket_slug=my-bucket-slug&read_key=foo&write_key=bar)`

提取这些变量非常容易，使用像`qs`这样的库甚至更容易:

```
import qs from "qs";const { bucket_slug, read_key, write_key, } = qs.parse(window.location.search.slice(1, Infinity));console.log({
   bucket_slug,
   read_key,
   write_key,
});
```

然后，可以使用这些键对当前使用扩展的存储桶进行 API 调用:

```
fetch(
   `https://api.cosmicjs.com/v1/${bucket_slug}/object/amazon-credentials${
      read_key
         ? "?read_key=" + read_key
         : ""
   }`,
   opts,
);
```

在我们的应用程序中，我们使用这些键为我们找到的亚马逊产品添加新对象。

# 扩展. json

您的扩展中必须包含的一个文件是`extension.json`文件。这告诉 Cosmic 关于你的应用程序的重要元数据，比如它的名称、图标、显示图像，以及在首次安装扩展时应该创建的默认对象和对象类型。我们应用的`extension.json`如下:

```
{
  "title": "Amazon Product Search",
  "font_awesome_class": "fa-shopping-basket",
  "image_url": "http://fla.fg-a.com/shopping-cart/shopping-cart-black-3.png",
  "objects": [
    {
      "title": "Amazon Credentials",
      "slug": "amazon-credentials",
      "type": "amazon-credentials",
      "metafields": [
        {
        "key": "amz-key",
        "title": "Key",
        "type": "text",
        "value": ""
        },
        {
        "key": "amz-secret",
        "title": "Secret",
        "type": "text",
        "value": ""
        },
        {
        "key": "amz-tag",
        "title": "Tag",
        "type": "text",
        "value": ""
        }
      ]
    }
  ],
  "object_types": [
    {
      "title": "Amazon Products",
      "slug": "amazon-items",
      "singular": "Amazon Product",
      "metafields":[
        {"key":"image_url","type":"text","value":"","title":"image_url","parent":false,"children":null},
        {"key":"affiliate_link","type":"text","value":"","title":"affiliate_link","parent":false,"children":null}
      ]
    }
  ]
}
```

`title`字段是您的扩展将在 Cosmic 中显示的名称`font_awesome_class`字段必须是有效的[字体牛逼的](http://fontawesome.io/icons/)图标名称`image_url`字段是将与您的扩展一起显示的显示图像。

您还可以指定安装扩展时将创建的对象列表，这里我们详细描述了一个对象，它将存储我们的 Amazon 产品搜索凭证。这意味着宇宙将为我们保存这些信息！

# 部署和持续集成

将你的扩展上传到 Cosmic 非常简单，你只需要拿一个包含你的`extension.json`文件和`index.html`文件(以及你需要的任何其他资产)的文件夹，压缩它，然后上传。

我们的构建文件夹如下所示:

```
build
├── extension.json
├── index.html
└── static
 └── js
 └── main.a651cd8a.js
```

您可以通过 Cosmic JS CMS 上传您的 zip 文件，或者使用 rest API。

我们使用 Bitbucket 来托管我们的代码，它有一个内置的 CI 服务，称为 Pipelines。您可以将管道配置为在每次提交 git repo 的`master`分支时运行，并将最新版本的应用程序上传到 Cosmic 的服务器。

我们将像这样配置我们的位桶管道:

```
image: codogo/pipelines-universal:latestpipelines:
  branches:
    master:
      - step:
          script:
          - yarn install 
          - yarn run lint
          - yarn run build
          - ./uploadNewExtension.sh
```

其中`uploadNewExtension.sh`包含:

```
#!/bin/bash# $BUCKET_SLUG, $READ_KEY, and $WRITE_KEY are environment variablesEXTENSION_NAME="${EXTENSION_NAME:-Amazon Product Search}"echo "geting previous version extension id..."
PREVIOUS_EXTENSION_ID="$( curl --progress-bar "https://api.cosmicjs.com/v1/extensions-2?hide_metafields=true&read_key=foo" | jq -r ".bucket.extensions | map(select(.title == \"$EXTENSION_NAME\" )) | .[0].id"  )"
# jq is a neat little program for extracting data from json, it is available here: https://jqplay.org/if [ "$PREVIOUS_EXTENSION_ID" == "null" ] ; then
echo "There is no '$EXTENSION_NAME' extension on cosmic to delete"
else
echo "Found id for '$EXTENSION_NAME': $PREVIOUS_EXTENSION_ID"
echo "deleting old extension..."
curl --progress-bar -X DELETE "https://api.cosmicjs.com/v1/$BUCKET_SLUG/extensions/$PREVIOUS_EXTENSION_ID" -d "{\"write_key\":\"$WRITE_KEY\"}" -H "Content-type: application/json" > /dev/null
fiecho "upload new version of extension..."
curl --progress-bar --url "https://api.cosmicjs.com/v1/$BUCKET_SLUG/extensions" --header "content-type: multipart/form-data" --form "write_key=$WRITE_KEY" --form "zip=@./build.zip" > /dev/null
```

这个脚本将删除我们扩展的任何先前上传的版本，然后将我们压缩的扩展上传到我们的 bucket。

# 摘要

在本教程中，我们已经介绍了 Cosmic JS 托管和集成您的扩展的方式，并解释了如何使用 Bitbucket 的持续集成来上传您的扩展。如果你学到了什么，请[分享这篇文章](https://twitter.com/intent/tweet?text=Building%20and%20Publishing%20a%20Cosmic%20JS%20Extension%20Using%20Bitbucket%20Pipelines%20@cosmic_js:%20https://cosmicjs.com/blog/building-and-publishing-a-cosmic-js-extension-using-bitbucket-pipelines)！

如果你正在用 Cosmic JS [制作一个扩展或者其他东西，请联系我们的 Slack](https://cosmicjs.com/community) 或者 [Twitter](https://twitter.com/cosmic_js) ，我们很想看看你在制作什么。

本文由 [Codogo](https://consulting.codogo.io/) 撰写，这是一家屡获殊荣的数字机构，致力于创造令人惊叹的数字体验。

> 这篇文章最初发表在宇宙 JS 博客上。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)