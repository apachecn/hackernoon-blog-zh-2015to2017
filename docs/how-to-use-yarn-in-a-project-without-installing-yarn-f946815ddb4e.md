# 如何在项目中使用纱线，而不安装纱线

> 原文：<https://medium.com/hackernoon/how-to-use-yarn-in-a-project-without-installing-yarn-f946815ddb4e>

能够在 CI 服务器上使用 yarn 而不是 npm 是很好的，但是通常您需要 root 访问 npm install -g yarn。下面是一个 bash 脚本，您可以使用它来安装 yarn(如果没有的话)，然后运行“yarn”:

```
#!/bin/bash# Choose which version of yarn you want to use
EXPECTED_YARN_VERSION=**"0.16.1"

function install_yarn** {
  mkdir -p .yarn
  DOWNLOAD_URL=**"https://github.com/yarnpkg/yarn/releases/download/v$EXPECTED_YARN_VERSION/yarn-v$EXPECTED_YARN_VERSION.tar.gz"** echo **"Downloading from $DOWNLOAD_URL"** curl -fL $DOWNLOAD_URL *> .yarn/yarn.tar.gz* tar zxf .yarn/yarn.tar.gz  --strip-components=1 -C .yarn
}

**if [ -f** .yarn/bin/yarn **]**; **then** YARN_VERSION=**$**(node -e **'const fs = require("fs"); console.log(JSON.parse(fs.readFileSync(".yarn/package.json")).version);'**)
  **if [ "$YARN_VERSION" != "$EXPECTED_YARN_VERSION" ]**; **then** echo **"The yarn version is $YARN_VERSION, expected $EXPECTED_YARN_VERSION. Re-downloading"** rm -rf .yarn
    *install_yarn* **fi
else** echo **"The file .yarn/bin/yarn does not exist, installing yarn"**.
  *install_yarn* **fi** ./.yarn/bin/yarn
```

## 该脚本将:

1.  创建一个文件夹”。yarn ”,它将在那里下载 yarn 本身(如果您不想显式提交它，最好忽略这个目录)
2.  检查纱线是否已经安装。如果已经安装，检查安装的是哪个版本的 yarn。如果是错误的版本，那么它会删除这个版本。
3.  尝试下载正确版本的 yarn。
4.  运行纱线。

## 您可以在脚本中调整的内容

1.  如果希望使用较新或较旧的 YARN 版本，请将 EXPECTED_YARN_VERSION 更改为另一个版本。
2.  不用每次都下载 yarn，您可以提交整个。yarn 文件夹到 git 存储库中，这样它就已经存在，可供 CI 服务器使用。
3.  如果你想在一个本地代理上保存纱线 tar.gz 文件，比如 Nexus，请更改下载 URL。
4.  更改最后一个命令，这样它就可以带参数运行 yarn。

祝你好运！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！