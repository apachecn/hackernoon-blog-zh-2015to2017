# 面向 PHP 开发人员的 Golang:依赖性管理

> 原文：<https://medium.com/hackernoon/golang-for-php-developers-dependency-management-19bf2c9bc2c4>

[PHP](https://hackernoon.com/tagged/php) 生态系统被用于依赖管理的 [Composer](https://getcomposer.org/) 彻底破坏了。Composer 非常简单易用。对于 [Golang](https://hackernoon.com/tagged/golang) 生态系统 [Glide](http://glide.sh/) 就是 Composer 对于 PHP 的意义；对于 Golang 来说，Glide 是最简单的(可能是)最常用的依赖管理工具。

![](img/ab3f62fb40a1e937f63911502e467464.png)

The Composer and Glide logo’s

本文通过四个重要的 Composer 命令来比较 Glide 和 Composer:init、install、require 和 update。不包括[安装滑道](https://glide.readthedocs.io/en/latest/#installing-glide)。

# 滑行作曲者初始化

Glide 还知道在 Golang 项目中初始化依赖管理的“init”子命令。在询问您一些关于您正在设置的软件包的问题后，这将为您设置初始的`glide.yaml`。

为您的项目创建一个`glide.yaml`:

```
$ glide init
```

然后，Glide 将创建`glide.yaml`文件，并尝试填充它。如果你在一个代码库上初始化 glide，而这个代码库已经有了。转到源文件。

# Glide 的 composer 安装

现在你知道了如何在项目中初始化 glide，是时候安装依赖项了。Composer 和 Glide 在这方面很像。要安装依赖项，只需调用:

```
$ glide install
```

Glide 将在`vendor/`文件夹中安装依赖项，就像 Composer 一样。

# 滑行需要作曲家

Glide 的“get”子命令相当于 Composer 的“require”。它的工作原理也大致相同。以下面两个命令为例:

```
$ composer require rtuin/somepackage # Add a dev dependency 
$ composer require --dev rtuin/somepackage # Or, with version specified 
$ composer require rtuin/somepackage:^1.0
```

Glide 的对应物，注意用`#`代替`:`来表示版本:

```
$ glide get github.com/rtuin/somepackage # Add a dev dependency 
$ glide get --test github.com/rtuin/somepackage # Or, with version specified 
$ glide get github.com/rtuin/somepackage#^1.0
```

Glide 和 Composer 一样尊重[表示版本号](https://glide.readthedocs.io/en/latest/versions/)的方式。

# Glide 的作曲更新

Composer“更新”和 Glide“更新”有完全相同的结果。它将根据`glide.yaml`文件中列出的包找到新的依赖版本，并根据您配置的版本范围约束检查这些版本是否需要安装。

如果有任何包需要更新，Glide 会像 Composer 一样，将这个包下载到`vendor/`文件夹，并将新版本写入`glide.lock`文件。最终导致可重复的安装。

# Glide 的 composer.json

Glide 使用 YAML 格式而不是 json，但除此之外，`glide.yaml`文件的本质与`composer.json`文件相似。

下面你会看到一个`composer.json`文件和一个`glide.yaml`文件，让你知道它们有多相似。

有三个主要区别:

*   `glide.yaml`中的包定义不包含版本号。软件包版本是由您将启用 Glide 的软件包放入的版本控制系统定义的。例如，这可以是散列引用、修订号或(语义版本)标记名。
*   没有“开发”导入，但是有“测试”导入。(但是你可以用同样的方法使用它们。)
*   Glide 中没有自动加载指令。这是因为 Golang 有自己固定的加载依赖项的方式，你不能使用 Glide 来影响它们。在 Golang 中，包加载是可预测的，不需要依赖项管理器中的配置值。

composer.json:

```
{
  "name": "rtuin/somepackage",
  "description": "This is some package description",
  "homepage": "https://github.com/rtuin/somepackage",
  "license": "MIT",
  "version": "1.0.0",
  "authors": [
    {
      "name": "Richard Tuin",
      "email": "git@rtuin.nl",
      "homepage": "http://www.rtuin.nl"
    }
  ],
  "autoload": {
    "psr-4": {"Rtuin\\Somepackage": "src/"}
  },
  "require": {
      "rtuin/someotherpackage": "^4.1",
  },
  "require-dev": {
    "rtuin/somedevdependency": "^5.3"
  }
}
```

glide.yaml

```
package: github.com/rtuin/somepackage
homepage: https://github.com/rtuin/somepackage
license: MIT
owners:
- name: Richard Tuin
  email: git@rtuin.nl
  homepage: http://www.rtuin.nl
import:
- package: github.com/rtuin/someotherpackage
  version: ^4.1
testImport:
- package: github.com/rtuin/somedevdependency
  version: ^5.3
```

# 结论

事实证明，Golang 的 Glide 与 PHP 的 Composer 非常相似。Glide 不是 Golang 唯一受欢迎的依赖管理器，但它绝对是最容易使用的，并且与 Composer 非常相似。

我还在 slideshare 上的幻灯片中提供了这些内容:

*最初发布于*[*rt uin . nl*](http://www.rtuin.nl/2017/06/golang-for-php-developers-dependency-management/)*。*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)