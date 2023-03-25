# 将暂存文件集成到您的 Rails 开发工作流中

> 原文：<https://medium.com/hackernoon/as-rails-developers-it-is-imperative-that-we-be-comfortable-and-productive-in-the-console-7a72a78d97d3>

## ***融合了*** 控制台的即时反馈与 Ruby 对象的结构和组织

作为 Rails 开发人员，我们必须在控制台中保持舒适和高效。控制台是我们反馈周期最短的地方，非常适合学习和修补。 [Rails](https://hackernoon.com/tagged/rails) 控制台在调试代码、[操作](https://hackernoon.com/tagged/manipulating)本地数据以及尝试新想法和实现方面非常有用。

然而，一旦我们从杂草中出现并开始处理更复杂的问题，控制台的刀刃就开始失去锋利。幸运的是，我们可以通过使用便笺式文件将控制台的即时反馈与 Ruby 对象/方法的结构和组织结合起来。

> 草稿栏是自动加载到所有 Rails 控制台会话中的文件。

![](img/10ce19919300c1f3ac06e8a1ab86ff87.png)

Adding a scratchpad file to my workflow has been a game changer

## 使用 Pry 制作便笺式文件

我推荐使用撬杆([撬杆](https://github.com/rweng/pry-rails)作为控制台。每当启动控制台(`rails console`或`binding.pry`)时，Pry 会自动在应用程序的根目录(也在`~/.pryrc`中)加载一个名为`.pryrc`的文件。我们可以使用该文件来加载我们的便签簿。

首先，在 Rails 根目录(`touch scratch.rb`)下创建一个 scratch.rb 文件。然后在`.pryrc`中加载文件:

```
load ‘./scratch.rb’
```

下面是一个带有示例的基本草稿文件:

```
module Scratch
  def scratch!
    load __FILE__
  end def my_scratch_method
    'placeholder return'
  end
endinclude Scratch
```

Scratch 模块加载到`scratch.rb`中，并包含在 Pry 控制台会话中，这意味着在控制台中可以使用其中定义的所有方法。

请注意，我在模块中添加了一个`#scratch!`方法，它将文件重新加载到最新保存的版本，而无需重新加载整个控制台。这允许您更改`Scratch`模块中的代码，并通过链接`#scratch!`实时接收反馈:

```
scratch!; my_scratch_method
```

scratch 文件的另一个便利用途是添加不属于源代码的常用助手方法。例如，我在手边放了一个进度条:

```
module Scratch
  ...
  require 'ruby-progressbar' # Iterates through scope while displaying a progress bar
  def each_with_progress_bar(scope, &block)
    progress_bar = ProgressBar.create
    progress = progress_bar(scope.count)
    scope.find_each do |item|
      block.call(item).tap do
        progress.increment
      end
    end
  end
  ...
endinclude Scratch
```

在控制台中提供暂存文件有许多有趣的用例。如果你有一个有趣的，给我留言。我很想知道其他人是如何使用便签簿的。

我从我的同事、朋友、行走的 vim 百科斯科特·皮尔斯( [@ddrscott](http://ddrscott.github.io/) )那里学到了这个窍门。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)