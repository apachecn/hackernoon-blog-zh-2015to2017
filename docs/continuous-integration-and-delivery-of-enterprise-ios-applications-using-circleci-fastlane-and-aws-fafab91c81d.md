# 使用 CircleCI、fastlane 和 AWS S3 持续集成和交付企业 iOS 应用程序

> 原文：<https://medium.com/hackernoon/continuous-integration-and-delivery-of-enterprise-ios-applications-using-circleci-fastlane-and-aws-fafab91c81d>

背景: [Budbee](https://www.budbee.com) 是一家为电子商务购物者提供送货上门的最后一英里物流服务。我们是一家位于瑞典斯德哥尔摩的科技创业公司。

在 Budbee，我们有几个 [iOS](https://hackernoon.com/tagged/ios) 应用程序，供多个利益相关方使用，例如司机。由于我们的开发必须快速进行，我们使用[苹果企业版](https://developer.apple.com/programs/enterprise/)来发布这些应用程序(我们不可能等待 1-4 周的部署时间)。

我个人一直认为管理代码签名资产和执行 iOS 应用程序的部署是一件麻烦的事情，所以我们决定尽可能地自动化这个过程，以便我们可以专注于更重要的事情。

我们使用 [CircleCI](https://circleci.com) 和 [fastlane](https://fastlane.tools/) 来持续构建我们的 iOS 应用程序，并将其分发到一个 S3 桶中。这个 bucket 有一个描述最新版本的 JSON 文件，因此如果有新版本可用，我们可以提示用户进行升级。我们使用 [sentry](https://sentry.io) 进行崩溃分析，当我们部署新版本时，我们的 dSYMs 会自动上传。

# 使用 match 管理代码签名资产

每个开发人员都需要有自己的一套用于开发的私有和公共密钥，无论是谁进行分发，都必须在他们的机器上有分发证书。当设置一个新的开发人员或者迁移到一个新的机器上时，你总是会把事情搞砸。Bleh，我们只是想把我们的应用发布出去——所以我们使用 [match](https://codesigning.guide/) 来管理我们的代码签名资产。它保持 git 上的一切同步。

1.  创建一个新的私有 github repo 来存储配置文件
2.  创建一个新的 Apple ID 用于代码签名，该 ID 将在您的团队之间共享(例如 ios-dev@company.com)
3.  运行匹配以生成新的证书和预置描述文件

```
$ MATCH_FORCE_ENTERPRISE=”1" match enterprise
```

默认情况下，Match 不允许企业分发，这是由于它的危险性质(如果你的密钥公开，任何人都可以滥用你的公司名称),因此有了`MATCH_FORCE_ENTERPRISE`变量。我们确保我们的 github repo 是私有的，只有 iOS 开发团队可以访问它，并且他们的帐户有双重认证。

4.按照 match 中的说明，使用您新创建的 Apple ID 进行鉴定。

# 设置 CircleCI

在 iOS 项目的根目录下，创建一个 circle.yml 文件，并用下面的模板填充它:

```
machine:
  environment:
    MATCH_FORCE_ENTERPRISE: "1"
  xcode:
    version: 8.2dependencies:
  pre:
    - gem update fastlane
    - brew install getsentry/tools/sentry-cli
  cache_directories:
    - 'vendor/bundle'
    - 'budbee-driver/Pods'
    - '~/.cocoapods'compile:
  override:
    - fastlane buildtest:
  override:
    - fastlane testdeployment:
  staging:
    branch: develop
    commands:
      - fastlane stagingrelease:
    tag: /v?[0-9]+(\.[0-9]+)*/
    commands:
      - fastlane deploy
      - fastlane dsym
```

在我们的 circle.yml 中，我们设置环境标志`MATCH_FORCE_ENTERPRISE`以允许企业分发，将 Xcode 版本设置为 8.2(这里使用您需要的任何版本)，更新到 fastlane 的最新版本并安装`sentry-cli`(用于上传 dSYMs)

你需要一个在 CircleCI 的 OS X 计划来构建 iOS/OSX 项目。遵循您的 github repo，并确保使用 circle.yml 设置开始构建。

您需要在 CircleCI 项目中填充环境变量，即`MATCH_PASSWORD`(您解锁代码签名证书的密码，您在设置 match 时选择了这个密码)、`S3_ACCESS_KEY`、`S3_SECRET_ACCESS_KEY`(您的 [AWS](https://hackernoon.com/tagged/aws) IAM keys 可以访问 S3 桶)和`SENTRY_AUTH_TOKEN`(由 [sentry.io](https://sentry.io) 生成的一个 auth 令牌)

我们用不同的通道来分离我们的快速文件，用于编译、测试和分发。当我们将代码推送到任何一个分支时，项目都会被构建和测试。当我们推进到开发分支时，应用程序的试运行版本被部署到 S3，我们推进新的版本标记(v1.2.3 ),生产版本被部署到 S3，dSYM 被上传到 Sentry。

# 设置快速通道

快速通道/快速文件:

```
fastlane_version "2.17.1"default_platform :iosplatform :ios dolane :build do
    match
    gym
  enddesc "Runs all the tests"
  lane :test do
    scan(
      workspace: 'budbee-app/myapp.xcworkspace',
      scheme: 'myapp',
      devices: ['iPhone 5s']
    )
  enddesc "Deploy new version to S3 bucket"
  lane :deploy do
    aws_s3(
      access_key: ENV['S3_ACCESS_KEY'],
      secret_access_key: ENV['S3_SECRET_ACCESS_KEY'],
      bucket: 'budbee-apps',
      region: 'eu-west-1',
      ipa: 'myapp.ipa',
      dsym: 'myapp.app.dSYM.zip',
      app_directory: 'com.budbee.myapp',
      upload_metadata: true,
      html_template_path: 'fastlane/s3_ios_html_template.erb',
      version_template_path: 'fastlane/s3_ios_version_template.erb',
      version_file_name: 'app_version.json'
    )
  enddesc "Upload dSYM to Sentry"
  lane :dsym do
    sentry_upload_dsym(
      auth_token: ENV['SENTRY_AUTH_TOKEN'],
      org_slug: 'budbee-ab',
      project_slug: 'budbee-app',
      dsym_path: './myapp.app.dSYM.zip'
    )
  enddesc "Deploy new staging version to S3 Bucket (staging)"
  lane :staging do
    aws_s3(
      access_key: ENV['S3_ACCESS_KEY'],
      secret_access_key: ENV['S3_SECRET_ACCESS_KEY'],
      bucket: 'budbee-apps',
      region: 'eu-west-1',
      ipa: 'myapp.ipa',
      dsym: 'myapp.app.dSYM.zip',
      app_directory: 'com.budbee.myapp.staging',
      upload_metadata: true,
      html_template_path: 'fastlane/s3_ios_html_template.erb',
      version_template_path: 'fastlane/s3_ios_version_template.erb',
      version_file_name: 'app_version.json'
    )
  end
end
```

快速通道/应用文件:

```
app_identifier "com.budbee.myapp" # The bundle identifier of your app
apple_id "[ios-dev@company.com](mailto:ios-dev@budbee.com)" # Your Apple email addressteam_id "[[DEV_PORTAL_TEAM_ID]]"  # Developer Portal Team ID
```

快车道/健身房:

```
workspace "budbee-app/myapp.xcworkspace"
scheme "myapp"
export_method "enterprise"output_directory "./"
```

快速通道/匹配文件:

```
git_url "[git@github.com](mailto:git@github.com):budbee/ios-certificates.git"type "enterprise"readonly trueapp_identifier ["com.budbee.myapp"]
username "[ios-dev@company.com](mailto:ios-dev@budbee.com)" # Your Apple Developer Portal username
```

安装所需的 fastlane 插件

```
$ fastlane add_plugin aws_s3
$ fastlane add_plugin sentry
```

fast lane/S3 _ IOs _ html _ template . erb:

```
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Install <%= title %></title>
  </head>
  <body>
    <style type="text/css">
      * {
        font-family: "Helvetica Neue",Helvetica,Arial,sans-serif;
        text-align: center;
        background-color: #ffffff;
      }
      .oneRow {
        width: 100%;
        overflow: auto;
        overflow-y: hidden;
        white-space: nowrap;
        text-align: center;
      }
      .download {
        margin: 30px;
        font-size: 130%;
      }
      a {
        text-decoration: none;
        color: blue;
      }
      a:hover {
        text-decoration: underline;
      }
      #finished { display: none; }
      #tutorial {
        text-align: center;
        max-width: 300px;
      }
      #logo  {
        text-align: center;
        max-width: 150px;
        margin-top: 10px;
      }
    </style><h1 style="text-align: center;"><%= title %></h1><div class="oneRow">
      <span class="download" id="ios">
        <a href="itms-services://?action=download-manifest&url=<%= plist_url %>" id="text" class="btn btn-lg btn-default" onclick="document.getElementById('finished').id = '';">
          Install <%= title %> <%= bundle_version %>
        </a>
      </span>
    </div><h3 id="desktop">Please open this page on your iPhone!</h3><div id="finished">
      <p>App is being installed. You may close Safari.</p>
</div><img src="[https://company.com/logo.png](https://carrier-images.s3.amazonaws.com/buyers/logo/1693de3a-f310-4607-b8fc-0a36f791069a.png)" id="logo" />
  </body><script type='text/javascript'>
    if (/iPhone|iPad|iPod/i.test(navigator.userAgent))
    {
      document.getElementById("desktop").remove()
    }
    else
    {
      document.getElementById("ios").remove()
    }
  </script>
</html>
```

fast lane/S3 _ IOs _ version _ template . erb:

```
{
    "latestVersion": "<%= bundle_version %>",
    "updateUrl": "itms-services://?action=download-manifest&url=<%= plist_url %>"
}
```

# 保持应用程序最新

在启动我们的 iOS 应用程序时，它检查 S3 桶是否有新版本可用，如果有，就提示用户更新。

```
$ pod install Alamofire
$ pod install Version
```

UpdateManager.swift:

```
import Foundation
import Alamofire
import Version[@objc](http://twitter.com/objc) class UpdateManager: NSObject {

    func checkForUpdate() {

        let baseUrl = "[https://budbee-apps.s3-eu-west-1.amazonaws.com](https://budbee-enterprise-apps.s3-eu-west-1.amazonaws.com)"
        let bundleIdentifier = Bundle.main.bundleIdentifier!
        let url = "\(baseUrl)/\(bundleIdentifier)/app_version.json"

        let currentVersion: Version = Version(Bundle.main.infoDictionary?["CFBundleShortVersionString"] as! String)

        Alamofire.request(url).responseJSON { response in
            if let result = response.result.value {
                let JSON = result as! NSDictionary

                if let version = JSON.value(forKey: "latestVersion") {
                    let latestVersion: Version = Version(version as! String)
                    let updateUrl = JSON.value(forKey: "updateUrl") as! String
                    if (currentVersion < latestVersion) {
                        self.displayUpdateAlert(url: updateUrl)
                    }
                }

            }
        }
    }

    func displayUpdateAlert(url: String) {
        let alertController: UIAlertController = UIAlertController(title: "New Version", message: "There is a new App version available. You must update to continue", preferredStyle: .alert)

        let button: UIAlertAction = UIAlertAction(title: "Update App", style: .default) { (action) in
            UIApplication.shared.openURL(URL(string: url)!)
        }

        alertController.addAction(button)

        UIApplication.shared.keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
}
```

就是这样！您应该能够在任何 iOS 设备上访问您的 S3 桶的 url，并安装企业应用程序。当您部署新版本时，系统会提示您直接从应用程序中进行更新。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！