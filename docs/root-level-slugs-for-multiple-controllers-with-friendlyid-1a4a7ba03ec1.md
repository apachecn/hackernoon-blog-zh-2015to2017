# 具有 FriendlyId 的多个控制器的根级 slugs

> 原文：<https://medium.com/hackernoon/root-level-slugs-for-multiple-controllers-with-friendlyid-1a4a7ba03ec1>

# FriendlyId gem 可以让你的应用程序与“友好的”URL slugs 一起工作，如“myapp /cities/amsterdam”。您可以将 slug 映射到根路径，但是如果您有两个包含友好 slug 的资源，并且您希望它们都在根路径中，该怎么办呢？

最近我在为[women hocode](http://www.womenwhocode.com)做网站时遇到了这个问题。这是我们解决方案的基本版本。

我们希望实现应用程序可以在根级别为 2 个不同且不相关的资源使用带有 slugs 的 URL，让我们将它们命名为`City`和`Flower`。代替像`myapp/cities/amsterdam,`这样的 URL，我们希望两个资源都在根级别上，这样应用程序就可以使用`myapp/amsterdam` *和* `myapp/tulips`。

## 基本设置友好 ID

在我们开始之前，快速提醒一下基本的 FriendlyId 设置:

> 对于 5.2.1 之前的 Rails 5.1+和 FriendlyId 版本:
> 在运行迁移之前，进入生成的迁移文件并指定 Rails 版本:
> `class CreateFriendlyIdSlugs < ActiveRecord::Migration**[5.1]**`

我们解决方案的关键是带有`friendly_id_slugs`表的`:history`模块，它是由 friendly_id 生成器(6^).行)生成的该表有一个`slugs`列，还有一个`sluggable_type`和`sluggable_id`。sluggable _ type 以 slug 所属的资源命名。看到了吗？这在以后会派上用场的！

设置好 FriendlyId 后，我们就可以使用 gem 的`finder`方法了。给定一个有`id: 123`的东西`:something`，我们可以友好地找到它们；`Thing.friendly.find(params[:id])`会返回我们的`something`无论我们传入的是 slug 还是`id`。因此应用程序可以同时使用`/things/something`和`things/123`。

对于本文，我们使用 friendly_id 默认值；查看文档中的调整和提示。

## 目标

通过`resources: flowers`照常设置路由，显示视图通过`myapp/flowers/tulip`指向。但是现在我们想要根级别的鼻涕虫:`myapp/tulip`。

对于单个控制器，我们可以通过在路线中添加`path`选项来实现:

```
# one controller root level slug
# routes.rb resources :flowers, only: :show, path: '' 
```

…然后`/myapp/tulip`转到`flowers`控制器的`#show`动作

到目前为止没问题。**但是现在我们要的是** `**flowers**` **鼻涕虫*和***`**cities**` **鼻涕虫的根层次。**

如果我们为`cities`添加第二个路由声明，也是用`path:''`，那么 Rails 路由器不知道它应该指向 flowers 控制器还是 city 控制器。实际上:因为路线是按照它们在`routes.rb,`中出现的顺序执行的，所以它总是指向首先列出的控制器。所以，如果花路线出现在城市路线之前，用`myapp/amsterdam`，是*花*控制器介入并试图用`:slug` 参数找到 a _flower_ 而不是我们要找的城市。

## 行动计划:SlugsController

解决方案:我们将添加一个新的 SlugsController，而不是使用资源自己的控制器。我们将不会使用资源表，而是使用 friendly_id_slugs 表来查找正确的记录并呈现其显示视图(步骤 1)。我们将把根级 slugs 路由到这个新方法(步骤 2)。为了更好的用户界面(和消毒？)我们将规范化用户输入(步骤 3)。最后，我们需要确保 slug 不仅对于它自己的模型是唯一的，而且对于共享根级别 slug 的其他模型也是唯一的。为此，我们需要一个定制的唯一性验证器(步骤 5)，由数据库约束支持(步骤 6)。

计划是这样的:

1.  创建(或`generate`)一个具有`:show`动作的 SlugsController，它使用 friendly_id_slugs 表及其`sluggable_type`字段来区分`flowers`和`cities`，并呈现视图。
2.  将根级 slugs 指向新的 SlugsController
3.  规范化用户输入
4.  为两个模型的 slugs 的唯一性创建一个定制的验证器
5.  添加数据库约束

## 步骤 1 - Slugs 控制器找到正确的记录

这里发生了什么？首先，我们在`friendly_id_slugs`表(L4)中找到`slug`记录。

现在我们已经可以访问`sluggable_type,`了，我们使用类型来区分`:render_view` 方法(L19)中的模型。然后，我们使用 friendly_id finder 方法来设置 ivar 并呈现视图。正确的 ivar 被一起发送，因此视图可以使用它，我们不必通过资源自己的控制器。

但是，这在当前的路线上是行不通的，所以让我们来解决这个问题。

## 步骤 2 -将废料发送到新的废料控制器

这里，我们将 slugs 指向 slugs 控制器中的 show 动作。常规航线排在第一位，以保持像`/myapp/cities`这样的航线运行。
如果你碰巧有命名空间的路线，比如`/admin/flowers`，确保把它们放在 routes 文件中更高的位置，否则 slugs 控制器会去寻找一朵花或者一个叫做‘admin’的城市。

> 为了实现这一点，请确保检查原始的显示操作。在那里设置的任何 ivar，或者在 ivar 上执行的任何逻辑，在视图中都不可用。在我们的例子中，这意味着我们需要重构 show 动作，最终得到一个漂亮干净的 oneliner: `@city = City.friendly.find(params[:id]` 剩下的部分被转移到视图助手、模型中的范围和实例方法。

现在，让我们确保我们只接受像 FriendlyId 格式的 slug。

## 步骤 3 -规范化用户输入

normalizer 方法去掉所有非单词字符，加上`—`，因为 FriendlyId 使用它作为单词分隔符。regex 中的`\w`匹配字母、数字和下划线。

我们将规范化的字符串存储在`@slug_params`中。有了这里设置的 ivar，我们就不再需要`:slug_params`方法了，所以我们跳过它，把所有的`slug_params`都改成`@slug_params` (L6，还有`render_view`方法中的 2 次)。

路由和渲染工作正常。还有一件事要做:验证子弹的独特性。我们不希望一株名为“阿姆斯特丹”的郁金香被允许使用“阿姆斯特丹”这个鼻涕虫，如果这个鼻涕虫已经被用于“阿姆斯特丹”这个城市的话。

## 步骤 4 -验证所有 slugs 的唯一性:每个验证器

模型中常规的 Rails 唯一性验证器将确保一个资源的 slugs 是唯一的。但是我们有一个额外的要求，因为我们使用了“双路由”。与 FriendlyId 的默认行为相反，我们的花蛞蝓不能与现有的城市蛞蝓相同。

因此，我们将创建一个定制的验证器来验证两个模型的唯一性。我们将在每个模型中使用这个验证器。

[EachValidator](http://api.rubyonrails.org/v3.2/classes/ActiveModel/EachValidator.html) 是验证属性的方法。它需要实现一个`validate_each`方法。我使无效 slugs 的消息与标准唯一性验证的消息相同。
`valid_slug?`方法(L10)迭代带有模型名称的数组。我们需要模型*常量*，而不是模型名称，所以我们`constantize`字符串。

然后(L13)我们使用 FriendlyId::FinderMethods 模块中的谓词方法来检查每个模型中是否存在 slug。我对`return false if-` [语句](https://hackernoon.com/tagged/statement)不是特别满意，但是现在，我更喜欢它，主要是为了可读性。

只要有匹配，我们就退出循环，因为任何匹配都会使新的 slug 无效。在我们的例子中，其中一个模型比另一个模型有更多的记录；该型号在数组中排在最后(L4)。

我们在两个模型中都称这个验证器为:

```
#in flower.rb and city.rb: validates :slug, presence: true, **slug: true**, if: :slug_changed?
```

`slug:true`是对自定义 SlugValidator 的实际调用。只有当废料浆发生变化时，它才会被触发。在示例项目中，这仅发生在`:create`；在实际项目中，这需要微调。

## 步骤 5 -添加数据库约束

我从这个问题中学到的一个有价值的教训是用数据库约束来支持惟一性验证。当两个用户同时添加相同的 slug 时，Rails 验证可能会通过。在这种情况下，我们希望[数据库](https://hackernoon.com/tagged/database)防止相同的 slugs 都被保存。

我们已经有了每个模型的数据库约束(见上面，第一个要点，L7 & 8)。我们所要做的就是为 friendly_id_slugs 表添加一个惟一的索引。(注意拼写`uniq`。)

```
# in terminal:$ rails g migration AddIndexToFriendlyIdSlugs slug:**uniq**# don't migrate yet
```

这将创建迁移文件。拆下`add_column`线。

```
# in db/migrate/2017...._add_index_to_friendly_id_slugs.rb**class *AddIndexToFriendlyIdSlugs*** < ***ActiveRecord***::***Migration***[5.1] **def** *change
    # remove the following line: 
    add_column* :friendly_id_slugs, :slug, :string*add_index* **:friendly_id_slugs**, **:slug**, **unique**: **true
  end**# then run 
$ rails db:migrate
```

从变更方法中移除冗余行并迁移后，以下索引被添加到`db/schema.rb`:

```
*t*.index [**"slug"**], **name**: **"index_friendly_id_slugs_on_slug"**, **unique**: **true**
```

最后一步，我们的根级 slug 系统已经就绪。这么🎉！

为了简化示例项目，我们使用默认的 FriendlyId。请务必查看文档并阅读其他示例，以便为您的用户提供更好的体验。
完整样本项目:[https://github.com/F3PiX/slugs_example](https://github.com/F3PiX/slugs_example)

**谢谢各位代码和文字评审:** [扎斯敏](https://medium.com/u/35f228a3e290?source=post_page-----1a4a7ba03ec1--------------------------------)[珍妮·索特伍德](https://medium.com/u/76884823fc9d?source=post_page-----1a4a7ba03ec1--------------------------------)和托尼(推特:@托尼)。
感谢 Pablo Rivera 关于技术帖子的鼓舞人心的故事:[https://dev.to/yelluw/how-to-do-technical-blogging](https://dev.to/yelluw/how-to-do-technical-blogging)我用这些技巧写了这篇帖子。

阅读更多信息:

*   friendly id:[https://github.com/norman/friendly_id](https://github.com/norman/friendly_id)
*   一篇关于开始并微调 friendly_id 的好文章:[http://vaidehijoshi . github . io/blog/2015/12/15/youve-get-a-friend-in-friend-id/](http://vaidehijoshi.github.io/blog/2015/12/15/youve-got-a-friend-in-friendly-id/)
*   RailsCast:过时，但永远是一种乐趣:[http://rails casts . com/episodes/314-pretty-URLs-with-friendly id？view = ascicast](http://railscasts.com/episodes/314-pretty-urls-with-friendlyid?view=asciicast)
*   关于验证和约束:(短:[https://stack overflow . com/questions/2367281/ruby-on-rails-it-better-to-validate-in-the-model-or-the-database](https://stackoverflow.com/questions/2367281/ruby-on-rails-is-it-better-to-validate-in-the-model-or-the-database)；long:[https://robots . thoughtbot . com/validation-database-constraint-or-both](https://robots.thoughtbot.com/validation-database-constraint-or-both)

对文本或代码有问题或意见吗？我很想收到你的来信！