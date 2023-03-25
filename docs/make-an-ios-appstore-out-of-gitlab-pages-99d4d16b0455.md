# 用 Gitlab 页面制作一个自动化的 iOS 应用商店，而不要向撒旦牺牲处女

> 原文：<https://medium.com/hackernoon/make-an-ios-appstore-out-of-gitlab-pages-99d4d16b0455>

[Gitlab](https://gitlab.com) 越来越成为许多任务的“一个解决方案”,它最初是一个安装在内部的 github 克隆服务，现在它专注于 CI/CD。
什么曾经可能是 github/gitlab/？？？+ jenkins 设置现在只需 gitlab 本身即可完成。
在我的环境中，我们有一个苹果企业订阅和几个开发 iOS 应用的供应商，我们[通过 OTA](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/DistributingEnterpriseProgramApps/DistributingEnterpriseProgramApps.html) 分发这些应用。
用于签署应用程序的证书不会与供应商共享，因此对于每次应用程序更新，我们都必须在分发给最终用户之前亲自签署应用程序(在我们的案例中，通过 **MDM** ，应用程序仅供公司员工使用)。供应商、我们的团队和公司的一些关键用户需要一种快速的方法来安装正确签名的应用程序的测试版本。
有很多解决方案可以达到向用户分发测试版的目的( [Fabric](https://get.fabric.io) 、 [TestFlight](https://developer.apple.com/testflight/) 等)。)，但是在我们的环境中，我们需要一个*内部*解决方案，因为由 MDM 管理的设备会受到一些限制，这些限制不适合这些类型的服务。

**关于 OTA 分发的小题外话**
OTA 分发相当简单，在过去是一场噩梦，但现在 xcode 处理得相当好。当您使用 xcode 为 *Enterprise* 或 *AdHoc* 发行版构建 ipa 时，您可以要求它生成一个清单文件。
**清单**文件是一个 plist(一个 xml ),它包含了一些关于应用程序、版本、捆绑包 id、图标和获取 ipa 文件的链接的信息。
要创建用户可从 iOS 设备使用的安装链接，您必须使用一个指向清单 plist 的自定义 apple 方案:

```
itms-services://?action=download-manifest&amp;url=https://yourdomainname.com/app.plist
```

请注意:

*   清单文件**必须在`https`连接下**被提供
*   网络服务器必须能够服务于这些 MIME 类型`.ipa application/octet-steam`和`.plist text/xml`

*我们的目标是简化自动化流程，使人们能够将代码推送到 gitlab 存储库，然后从 iOS 设备转到 HTML 页面，直接安装更新的应用程序。*

## 首次设置

第一个设置是用**git lab**+**Jenkins**+**一个静态 HTML 站点生成器**完成的。
对于自动化程度不同的应用程序，工作流程略有不同，大致如下:

*   一家供应商向 gitlab 推出新变化
*   jenkins 上的**构建**作业被触发，该作业构建应用程序、签名并在共享位置移动生成的 *ipa* (可由其他作业访问)
*   jenkins 上的**发布**作业是手动触发的，或者是由之前的作业触发的，通过混合使用脚本和静态站点生成器(我们使用 [punch](http://laktek.github.io/punch/) 只是因为它使用 json 文件作为内容输入)，生成最终的 HTML *~AppStore* ，其中包含所有应用程序页面和正确的 manifest.plist，以便手动将它们安装到设备上。

这种方法很好，因为它做了它必须做的事情，但是涉及到几个完全分离的软件并在不同的机器上运行:gitlab 托管源代码，jenkins 在 OSX 的机器上构建和签名，另一台机器使用 web 服务器来服务最终的网站。
此外，它还涉及许多关于配置、脚本和文件路径的*“惯例的魔力”*。

## 中间设置

当 gitlab 开始集成 [CI/CD](https://about.gitlab.com/gitlab-ci) 时，我们改变了这一工作流程，将 Jenkins 从等式中移除，并利用 gitlab 的其他功能，如 [API](https://docs.gitlab.com/ce/api/README.html) 及其 [Oauth 提供者](https://docs.gitlab.com/ce/integration/oauth_provider.html)。软件栈减少到 **gitlab** ，一个简单的 [gitlab runner](https://docs.gitlab.com/ce/ci/runners/README.html) 和一个用 python 制作的 **webapp** 和 [Flask](http://flask.pocoo.org) 。
新的工作流程是:

*   将新代码推送到 gitlab
*   已配置的 gitlab CI 被触发， *runner* 构建并存储已签名的 ipa 作为 gitlab 上的[工件](https://docs.gitlab.com/ce/user/project/pipelines/job_artifacts.html)。

不需要发布步骤，因为所有其他事情都由 flask webapp 处理。

webapp 并不简单:

*   它使用 gitlab 的 oauth 特性对已经可以访问 gitlab 的人进行身份验证，并获得一个用于 gitlab API 的`user token`
*   用获得的令牌调用 API 检索用户可见的项目列表(更具体地说，过滤掉非 iOS 项目)
*   转到特定的项目页面，再次通过 gitlab API，我们检索最新成功构建的工件
*   我们读取 ipa 内部，并为安装链接动态生成一个清单文件(我们可以使用 xcode 生成的清单，并将它作为工件保存在 ipa 中)

这个解决方案更好，因为我们删除了一个软件，但我们仍然有 3 台机器来运行一切(一台用于 gitlab，一台用于 runner，另一台用于 webapp)。
我们移除了*【惯例魔法】*和所有的脚本，将整个逻辑嵌入到 webapp 中。
但是我们不得不做一些技巧(又名*坏事*)来解决一些 gitlab API [的怪癖](https://gitlab.com/gitlab-org/gitlab-ce/issues/22378)，这些怪癖使得 flask 应用程序很难维护并且整体上很难看。

## 最终设置

当 gitlab 在社区版上发布[页面](https://docs.gitlab.com/ce/user/project/pages/)时，我们决定再次重构所有的东西。
有了这个设置，我们可以完全杀死恼人的 flask 应用程序，并删除一台机器。
我不会深入 gitlab pages 是如何工作的，但是*长话短说*您可以在 CI 配置上添加一个*作业*来将文件夹发布为静态站点，站点所在的主机名取决于您配置 CI *作业*的存储库。查看[文档](https://docs.gitlab.com/ce/user/project/pages/)了解更多细节。

新流程现在是这样的:

*   将新代码推送到 gitlab
*   已配置的 gitlab CI 被触发，并且*运行程序*构建并存储已签名的 ipa 和清单文件，作为 gitlab 上的[工件](https://docs.gitlab.com/ce/user/project/pipelines/job_artifacts.html)。
*   一个 **pages** *job* 生成 HTML 静态文件，这些文件将与之前的 job 工件(ipa 和 manifest)一起发布在项目页面下。

最后一步可以用几种方法来完成，你可以使用一个成熟的静态站点生成器或者只是一个简单的脚本来生成一个 html 页面。

基于你想要完成的，你可以有一个`namespace site`来列出你想要公开的所有项目，然后有一个`project site`来链接安装构建(如果你不知道我的意思，还是用 [RTFM](https://docs.gitlab.com/ce/user/project/pages/getting_started_part_one.html#gitlab-pages-domain) )。

这个设置的一个简单的`.gitlab-ci.yml`可能是这样的:

```
stages:
  - build
  - publish

build_production:
  stage: build
  script:
    - BASE_URL=https://namespace.yourdomain.com/projectname APPNAME=mightyapp fastlane gym

  artifacts:
    paths:
      - public/*.ipa
      - public/*.plist
    expire_in: 1 day // we don't need to save this since it is persisted by the pages job later

pages:
  stage: publish
  script:
  - python publish.py "https://namespace.yourdomain.com/projectname"
  artifacts:
    paths:
    - public
```

我建议你使用浪子[体育馆](https://github.com/fastlane/fastlane/tree/master/gym)而不是 xcodebuild，以简化构建 ipa 和生成清单文件。

下面是一个最小的`Gymfile`:

```
output_directory "./public"
export_options(
  method: "ad-hoc", 
  manifest: {
    appURL: "#{ENV["BASE_URL"]}/#{ENV["APPNAME"]}.ipa",
    displayImageURL: "https://placehold.it/600x600",
    fullSizeImageURL: "https://placehold.it/600x600"
  }
)
output_name ENV["APPNAME"]
```

最后，这可能是一个超级快速和肮脏，但工作脚本生成一个 html 页面:

```
# -*- coding: utf-8 -*-
"""
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <title>Install {title}</title>

</head>
<body>
    <div class="main" style="margin:50px; text-align:center;">
        <h1>{title}</h1>
        <p>{bundleid}</p>
        <p>{version}</p>
        <a href="itms-services://?action=download-manifest&url={baseurl}/manifest.plist">INSTALL</a>
    </div>
</body>
</html>
"""

import sys
from plistlib import readPlist
from hashlib import md5
from os import walk, environ
from datetime import datetime

def main():
    baseurl = sys.argv[1]
    plistfile = "public/manifest.plist"
    index_html_path = "public/index.html"
    plist = readPlist(plistfile)
    metadata = plist['items'][0]['metadata']
    info = dict()
    info["title"] = metadata['title']
    info["version"] = metadata['bundle-version']
    info["bundleid"] = metadata['bundle-identifier']
    info["baseurl"] = baseurl
    with open(index_html_path, "w+") as f:
        f.write(__doc__.format(**info))

if __name__ == '__main__':
    main()
```

该脚本非常简单明了，你可以通过使用模板引擎或任何你想要的**静态站点生成器**来发挥创造力。

希望能给你一些新的想法，或者至少让你尝试一下 gitlab，因为它是一个非常好的软件！

完全披露:不，他们不支付我很遗憾！

感谢 [Sl3](https://medium.com/u/fb8dedd8f96e?source=post_page-----99d4d16b0455--------------------------------) 的校对:P

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！