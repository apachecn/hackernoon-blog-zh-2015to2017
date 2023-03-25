# 裸金属红宝石

> 原文：<https://medium.com/hackernoon/bare-metal-ruby-3f7c8504d370>

本手册解释了如何在 Linux 上安装最新稳定版本的 Ruby，而不使用 T2 rbenv 或其他版本管理器。目标是找到一种快速可靠的方法，为 Rails [开发](https://hackernoon.com/tagged/development)环境提供可消耗的虚拟机。

基本假设:

1.  安装速度很重要(不要在 Ruby 构建的时候等待)。
2.  整个系统只需要一个版本的 Ruby。
3.  我们用的是 Ubuntu。

**第一步。从 Brightbox 安装预建的 Ruby】**

注意 *dev* 包，除了主包之外还应该安装。需要为 Ruby gems 构建本机扩展。

```
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:brightbox/ruby-ng
sudo apt-get update
sudo apt-get install ruby2.4 ruby2.4-dev
```

Brightbox manual 提出了一个名为 [*ruby-switch*](https://www.brightbox.com/docs/ruby/ubuntu/#switching-the-default-ruby-version) 的工具，可以帮助在同一系统上的多个 ruby 之间进行切换。因为只有一个，所以这一步是不必要的。

**第二步。让宝石在没有数独的情况下工作**

默认情况下 *gem* 会尝试将新的 gem 安装到系统文件夹中(例如 */var/lib/gems/2.4.0* )，这样不好。Ruby 版本管理器用用户主目录下的内容覆盖了这个路径。但是同样的操作也可以手动完成。将目标目录永久设置为用户 home，并将这些行设置为您的 *~/。巴沙尔*:

```
export GEM_HOME=$HOME/.gem/ruby/2.4.0
export PATH=$HOME/.gem/ruby/2.4.0/bin:$PATH
```

下面是你需要知道的关于 Ruby 版本管理器的最重要的事情，以便理解它们在系统配置中到底做什么:

> RubyGems 的默认本地存储库可以用 GEM_PATH 和 GEM_HOME 环境变量覆盖。GEM_HOME 设置要安装到的默认存储库。GEM_PATH 允许在多个本地存储库中搜索 GEM。([http://guides.rubygems.org/command-reference/](http://guides.rubygems.org/command-reference/))

**第三步。运行快速临时测试**

登录到 shell，执行这些命令以确保 *gem* 和 *ruby* 二进制文件可用，gem home 路径配置正确，并且可以构建本机扩展:

```
cd
gem install bundler rails
rails new testapp
```

或者只执行 *gem env* 来查看路径，而不安装任何东西。

下面是一个完整的*流浪文件*，按照描述的方法，为新的 Linux 虚拟机提供 Ruby:[https://github.com/dreikanter/vagrant-rails](https://github.com/dreikanter/vagrant-rails)🍰

和平。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！