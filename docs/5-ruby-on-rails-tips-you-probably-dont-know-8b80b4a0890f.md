# 你可能不知道的 5 个 Ruby on Rails 技巧

> 原文：<https://medium.com/hackernoon/5-ruby-on-rails-tips-you-probably-dont-know-8b80b4a0890f>

![](img/ec0f8e343ba3ce284163eb8243eea5d7.png)

自从我开始在 [*Rails*](https://hackernoon.com/tagged/rails) 上使用 [*Ruby*](https://hackernoon.com/tagged/ruby) *以来，经过几年的使用，当我在 *Ruby* 世界中发现一些新的东西时，我就无法停止好奇。是的，这就是它的工作方式——你知道， *Ruby* 是为了开发者的快乐(除此之外还有一些其他原则),每年使用它并发现新的东西都支持这种内在的感觉。*

好了，这篇文章分享了我最近发现的一些东西。它们是很少使用的方法，在你的代码中也不是必须的，大部分是“语法糖”，但无论如何它会让你的代码更干净。一些东西只是随着新的 *Ruby* 或 *Rails* 版本出现的新变化。

# 哈希#摘要

我在 7 年的 RoR 开发中没有在任何人的代码中遇到这种情况，最近才发现。原因很明显:-)我使用的第一个 *Ruby* 版本是 1.8，但是这个方法是在 2.3 中引入的。

你做过多少次这样的事情:

```
... if params[:user] && params[:user][:address] && params[:user][:address][:somewhere_deep]
```

把`dig`看作是一种安全的导航操作符`&.`，但是对于*散列*对象。所以现在你可以重写这些东西:

```
... if params.dig(:user, :address, :somewhere_deep)
```

# 对象#存在 _ 在

这是我在一篇关于 Ruby on Rails 中的[查询对象的非常好的文章中发现的。当您实际上不需要包含检查的布尔结果，而是需要被检查的对象本身时，它允许您用单个方法调用替换条件(通常是三元的)。例如，您的代码看起来像:](https://medium.flatstack.com/query-object-in-ruby-on-rails-56ea434365f0)

```
sort_options = [:by_date, :by_title, :by_author]
...sort = sort_options.include?(params[:sort]) 
  ? params[:sort] 
  : :by_date# Another option
sort = (sort_options.include?(params[:sort]) && params[:sort]) || :by_date
```

这样不是更好看吗？

```
params[:sort].presence_in(sort_options) || :by_date
```

# 模块#别名 _ 属性

当我在一个项目中使用遗留数据库时，我发现这非常有用。它有一个带有奇怪列名的表格，比如`SERNUM_0`、`ITMDES1_0`和其他。我们将该表映射到一个 *ActiveRecord* 模型，而不是像`WeirdTable.where(SERNUM_0: ‘123’)`那样在其上编写查询和作用域，我们开始使用`alias_attribute`，因为它不仅生成 getter 和 setter(以及一个谓词方法)，还可以在查询中工作:

```
alias_attribute :name, :ITMDES1_0
...
scope :by_name, -> (name) { where(name: name) }
```

# 对象#存在

这个比其他的更受欢迎。在 [ApiDock](https://apidock.com/rails/Object/presence) 上有很好的解释。所以，`object.presence`相当于:

```
object**.**present? **?** object **:** nil
```

# 模块#委托

由于某些原因，大多数开发人员仍然很少使用它。这种方法的主要目的是松耦合，遵循 [*定律和*](https://en.wikipedia.org/wiki/Law_of_Demeter) 定律。想到的关于这个主题的一篇好文章是 Avdi Grimm 的[“Demeter:这不仅仅是一个好主意。这是法律。”](http://www.virtuouscode.com/2011/07/05/demeter-its-not-just-a-good-idea-its-the-law/)此外，查看简短的 [Rails 最佳实践文章](https://rails-bestpractices.com/posts/2010/07/24/the-law-of-demeter/)，了解如何在应用 Demeter 的*法则的背景下利用`delegate`。下面的片段也对此进行了描述:*

```
class Profile < ApplicationRecord
  belongs_to :user delegate :email, to: :user
end...
profile.email # equivalent to profile.user.email
```

我希望你刚刚发现了上面的一些技巧，并觉得它们很有用。编码快乐！

*如果你喜欢这个帖子，请点击* ✋ *来传播这个消息。*