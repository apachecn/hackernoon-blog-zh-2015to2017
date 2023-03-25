# 如何在 Ruby on Rails 5 的 URL 中使用散列 ID

> 原文：<https://medium.com/hackernoon/how-to-use-hash-ids-in-your-url-in-ruby-on-rails-5-e8b7cdd31733>

![](img/e892b2882ac2436ed62febd86a00f6da.png)

当我构建一个 Rails 应用程序时，我希望以某种形式面向公众，我不想在我的 URL 中显示自动递增的、基于整数的 id。在我看来，它不仅在视觉上看起来更好。这是一种安全性增强，通过 URL 消除了记录发现的可预测性。尽管自动递增的整数主键在 ActiveRecord 中已经根深蒂固，但我不想让事情变得太糟。有些人建议使用 UUIDs，但是我觉得它们对于我喜欢的 URL 来说太长了。所以，我开始讲述如何在我的 URL 中使用散列作为 id，并希望与你分享。

本教程将假设您已经有了现有的模型，但是，您只需要向您的新模型添加一个“hash_id:string”列。此外，您还需要安装 [friendly_id gem](https://github.com/norman/friendly_id) 。

```
# Gemfile
gem 'friendly_id', '~> 5.1.0'
```

…当然还有

```
bundle install
```

太好了，我们有了在模型上进行散列 ID 查找所需的宝石。接下来，我们需要将功能包含到我们的模型中。我选择创建一个模型关注点，它将能够包含在任何我们想要散列 ID 功能的模型中…

```
# app/models/concerns/friendlyable.rbmodule Friendlyable
  extend ActiveSupport::Concern included do 
    extend ::FriendlyId
    before_create :set_hash_id
    friendly_id :hash_id
  end def set_hash_id
    hash_id = nil
    loop do
      hash_id = SecureRandom.urlsafe_base64(9).gsub(/-|_/,('a'..'z').to_a[rand(26)])
      break unless self.class.name.constantize.where(:hash_id => hash_id).exists?
    end
    self.hash_id = hash_id
  endend
```

让我们检查一下那个文件。前两行是标准的模型关注样板。

```
included do 
    extend ::FriendlyId
    before_create :set_hash_id
    friendly_id :hash_id
  end
```

include 块基本上允许您将行插入到模型文件中，就像您手动将它们放在那里一样。因此，我们添加了 FriendlyId 功能，添加了 before_create 回调，最后告诉 FriendlyId 我们使用的 hash_id 列具有 FriendlyId 查找列。

```
def set_hash_id
    hash_id = nil
    loop do
      hash_id = SecureRandom.urlsafe_base64(9).gsub(/-|_/,('a'..'z').to_a[rand(26)])
      break unless self.class.name.constantize.where(:hash_id => hash_id).exists?
    end
    self.hash_id = hash_id
  end
```

在文件的后半部分，我们定义了从 before_create 钩子调用的方法。本质上，我们正在创建一个循环来创建一个 URL 安全散列，如果 hash_id 不与任何现有记录冲突，就设置 hash_id 属性。如果成功，我们就中断循环，并设置 ActiveRecord 的属性来保存。

既然我们已经准备好了模型关注点，那么将它包含到模型中就非常容易了:

```
class User < ApplicationRecord
  include Friendlyable ...end
```

…就是这样！让我们添加迁移来完成这个任务。

```
class AddHashIdToUsers < ActiveRecord::Migration[5.0]
 def up
   add_column :users, :hash_id, :string, index: true
   User.all.each{|m| m.set_hash_id; m.save}
  end def down
   remove_column :users, :hash_id, :string
  endend
```

迁移将添加该列，对其进行索引，然后更新任何现有记录，使其具有 hash_id。

这里的最后一部分将通过 FriendlyId 查找记录，这是对应用程序中任何发现的简单更新:

```
User.friendly.find(params[:id])
```

…它将使用主“id”键或您的“hash_id”来查找记录。你有它！从现在开始，您可以在您的 Rails 应用程序中使用像[http://localhost:3000/users/90 upoijsz](http://localhost:3000/users/90upoijsz)这样的 URL。

PS——这个概念和其他许多概念在我的新书《在 Rails 5(【https://BuildASaaSAppinRails.com】)上构建 SaaS Ruby》中得到了应用，现在已经开始预售了。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)