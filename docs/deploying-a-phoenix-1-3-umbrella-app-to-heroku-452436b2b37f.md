# 将 Phoenix 1.3 雨伞应用程序部署到 Heroku

> 原文：<https://medium.com/hackernoon/deploying-a-phoenix-1-3-umbrella-app-to-heroku-452436b2b37f>

Heroku 构建包基于早期版本对你的项目做了一些假设，但是稍作调整就可以用于 1.4 版的 Elixir 和 1.3 版的 Phoenix 。

这假设您使用`mix phx.new hello_phoenix --umbrella`创建了您的项目。在适当的地方用你的应用名称替换 hello_phoenix 和 HelloPhoenix。

http://www.phoenixframework.org/docs/heroku 的大部分指令仍然有效。完成这些操作，然后进行以下更改:

*   这可能是显而易见的，但提到的`config/prod.exs`是 web app 文件夹中的那个:`hello_phoenix_umbrella/apps/hello_phoenix_web/config/prod.exs`。
*   将`prod.exs`中的参考从`HelloPhoenix.Endpoint`更改为`HelloPhoenix.Web.Endpoint`。
*   你还需要从`hello_phoenix_umbrella/config/prod.exs`中移除`import_config "prod.secret.exs"`。
*   在项目的根目录下创建一个名为`phoenix_static_buildpack.config`的文件，并添加下面一行`phoenix_relative_path=apps/hello_phoenix_web`。
*   将 Procfile 中的行改为`web: MIX_ENV=prod mix phx.server`。
*   如果您的应用程序使用数据库，您可以将这一行添加到 Procfile 中，以便在发布时运行迁移:`release: mix ecto.migrate`。
*   创建一个名为`elixir_buildpack.config`的文件，并设置您的 Erlang 和 Elixir 版本以及运行时路径(/app 是您的项目的根):

```
erlang_version=19.1
elixir_version=1.4.2
runtime_path=/app
```

# 如果您使用 Elm:

在 Phoenix buildpack 中有一个关于[支持 Elm](https://github.com/gjaldon/heroku-buildpack-phoenix-static/issues/30) 的未决问题🤗。现在，如果你正在用 elm-brunch 编译你的 Elm 应用程序，你可以把`elm`作为一个依赖项添加到你的`apps/hello_phoenix_web/assets/package.json`中。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！