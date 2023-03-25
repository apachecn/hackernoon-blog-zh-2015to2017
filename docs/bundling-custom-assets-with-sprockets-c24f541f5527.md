# 将自定义资产与链轮捆绑在一起

> 原文：<https://medium.com/hackernoon/bundling-custom-assets-with-sprockets-c24f541f5527>

**本文解释了如何通过定制链轮配置，使用 Rails 资产管道处理定制资产文件。**

让我们考虑一个使用客户端[小胡子](https://hackernoon.com/tagged/mustache)模板渲染的 Rails 应用程序的例子。从 [JavaScript](https://hackernoon.com/tagged/javascript) 中访问模板的有效方法是什么？

这里有许多方法。最基本的方法是将模板直接嵌套到 HTML 视图代码中。像这样:

```
<script type='text/template' id='post-template'>
  <h2>{{ title }}</h2>
  <p>{{ body }}</p>
</script>
```

但这远非理想:

1.  很可能会有更多的模板，您需要在不同的视图之间共享其中的一些。因此，保持模板有组织是有意义的，而不是把它们分散在 HTML 中。
2.  嵌套模板是不必要的手动操作。最好能马上从 JavaScript 中获得模板，而不是依赖于您不会忘记添加一个模板。
3.  有时在 *<脚本>* 元素中嵌套一个模板会破坏语法高亮，因此将它们保存在单独的 **中。小胡子*档通常效果更好。但是，这取决于文本编辑器的配置。

为了解决这些问题，让我们让 Rails 资产管道在 JavaScript bundle 中生成一个 JSON 对象，并从位于 *app/assets/templates* 的 templates 目录中填充这个对象。对于消费者来说，它看起来像这样:

```
window.Templates = {
  post: "<h2>{{ title }}</h2><p>{{ body }}</p>",
  comment: "..."
}
```

通过这种方式，模板将被预加载到公共包中，并且在使用它的每个视图中都是可用的。

**第一步。注册新的 MIME 类型**

在典型的 Rails 应用程序中，定制链轮配置应该在*应用程序*类中的*config . assets . configure*块下定义。所以下面的例子是基于这样一个假设，即在你的 *config/application.rb* 中已经存在这样一个块:

```
module MyApplication
  class Application < Rails::Application
    config.assets.configure do |env| **// Custom Sprockets configuration should go here** end
  end
end
```

MIME 类型注册非常简单。只需定义新类型与您计划处理的文件扩展名列表之间的关联:

```
env.register_mime_type('text/mustache', extensions: ['.mustache'])
```

**步骤二。注册新资产变压器**

这里最重要的部分是回调。它是一个可调用对象，将资产文件内容从 *source_type* 转换为不同的 *target_type。*回调应该返回一个散列，其中包含*中已处理的内容:数据*键:

```
source_type = 'text/mustache'
target_type = 'application/javascript'**callback = -> (input) { { data: '// HELLO ${input[:name]}' } }**env.register_transformer(source_type, target_type, callback)
```

Lambda 表达式可能不适合现实生活中的情况。在 Rails 配置之外定义的服务类将是更好的选择。但是现在让我们先完成样板文件。真实的变压器示例如下。

**第三步。启用自定义文件类型处理**

为此，在*资产*初始化器(*config/initializer/assets . Rb*)中的*预编译*数组中添加一个正则表达式:

```
Rails.application.config.assets.precompile += [/\.(?:mustache)\z/]
```

**第四步。捆绑转换的资产**

在注册了新的 transformer 之后， [*require* 和 *require_tree*](http://guides.rubyonrails.org/asset_pipeline.html#manifest-files-and-directives) 指令都可以很好地使用自定义资产类型。

如果您在 *app/assets/templates* 下保留 Mustache 模板，那么将这一行添加到*app/assets/JavaScript s/application . js*将为每个 **注入 transformer 回调输出。小胡子*文件:

```
//= require_tree ../templates
```

上一步中的虚拟 lambda-transformer 将用如下注释文件名列表替换这一行:

```
// HELLO template_file_name
// HELLO another_template_file_name
// ...
```

要看到它工作，不要忘记重启 Rails 服务器，并确保清除资产缓存。

下面是一个实际的 transformer 示例，它从 Mustache 模板文件生成 JavaScript 对象:

```
class MustacheTransformer
  def self.call(input)
    key = input[:name]
    body = JSON.dump(input[:data])
    obj = 'window.Templates'
    { data: "#{obj} = #{obj} || {}; #{obj}['#{key}'] = #{body}" }
  end
end
```

除了*:数据、*之外，*输入*对象包含许多其他扩展字段，这些字段在内容转换过程中可能很有价值。查看[的官方参考，完整列表](https://github.com/rails/sprockets/blob/master/guides/extending_sprockets.md#extension-keys)。

本文中阐述的小胡子模板只是扩展链轮的一个具体例子。除了模板之外，还可以将任何其他资产与适合您需求的任意转换逻辑捆绑在一起。

和平。

参考资料:

*   [go rails 论坛原创讨论](https://gorails.com/forum/extend-sprockets-to-bundle-mustache-templates)。
*   [延伸链轮](https://github.com/rails/sprockets/blob/master/guides/extending_sprockets.md)，官方指南。
*   [链轮::变压器](http://www.rubydoc.info/github/rails/sprockets/Sprockets%2FTransformers%3Aregister_transformer) API 参考。
*   [用 Rails 资产管道预编译资产](http://guides.rubyonrails.org/asset_pipeline.html#precompiling-assets)。
*   [MIME 类型的完整列表](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types)。
*   [handlebars_assets](https://github.com/leshill/handlebars_assets) gem:一个现成的替代品，用于将模板嵌入 Rails 资产包的特殊情况。