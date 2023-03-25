# 在干净的 mac 上设置浪子的唯一明智的方法

> 原文：<https://medium.com/hackernoon/the-only-sane-way-to-setup-fastlane-on-a-mac-4a14cb8549c8>

**macOS** 预装 ruby 使用现代的 ruby 工具很不方便，在这段时间里我努力为它设置一个合适的设置。
自从我发现自己在新机器上安装了[浪子](https://github.com/fastlane/fastlane)之后，每次我都不得不记起哪种方式更好，我写这篇文章主要是作为自己的参考，但我确信它对其他人也有帮助。

## 啤酒

如果你在 mac 上开发，并且还没有安装 [brew](https://brew.sh) ，那你就是*疯子*。

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## rbenv

我们需要一个 ruby 版本管理器来保持清醒的头脑，我发现 rbenv 不那么邪恶:

```
brew install rbenv ruby-build
```

把这个加到你的`.bashrc`或者你的毒药是加载 bash 还是 zsh 还是？？？：

```
if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi
```

## 红宝石

用 rbenv 安装一个较新的 ruby:

```
rbenv install 2.4.1
rbenv global 2.4.1
```

## 浪子

最后，我们可以安装[浪子](https://github.com/fastlane/fastlane)与新的闪亮的红宝石环境，我们有:

```
gem install fastlane
```

例如，这样你也可以安装`cocoapods`，并且保持正常。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个大家庭的一员。我们现在[正在接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！