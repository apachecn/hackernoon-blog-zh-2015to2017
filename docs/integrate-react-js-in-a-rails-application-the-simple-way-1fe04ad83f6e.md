# 将 React.js 集成到 Rails 应用程序中——简单的方法

> 原文：<https://medium.com/hackernoon/integrate-react-js-in-a-rails-application-the-simple-way-1fe04ad83f6e>

React 是一个构建客户端 javascript 应用程序的伟大框架。与其他客户端框架相比，它只负责 MVC 中的 V。将它与 jQuery 或 backbone.js 一起使用非常容易。

在过去的几个月里，围绕 react 发布了一些很棒的东西，比如 [react native](https://facebook.github.io/react-native/) 、 [relay](http://facebook.github.io/react/blog/2015/08/11/relay-technical-preview.html) 、 [graphql](https://github.com/facebook/graphql) 。React 推动整个 javascript 生态系统使用新工具，编写更好的代码，并重新思考最佳实践。

与客户端 javascript 相关的新标准之一是使用 [CommonJS](http://requirejs.org/docs/commonjs.html) 模块。CommonJS 包括以一种安全和明确的方式管理依赖关系，使得模块可以要求其他模块，就像任何真正的编程语言一样。唯一的问题是浏览器不支持开箱即用。有几个工具可以解决这个问题，包括[requires](http://requirejs.org/)、 [browserify](http://browserify.org/) 和最近的 w [ebpack](https://github.com/webpack/webpack) 。

Browserify 可以作为一个独立的工具链来构建一个 js 应用程序(不需要使用 gulp 或 grunt)。一个很好的例子是脸书通量 [Todo MVC 样本代码](https://github.com/facebook/flux/tree/master/examples/flux-todomvc)。Browserify 基本上是在 react 应用程序之外创建一个 *bundle.js* 文件，然后您可以将 *bundle.js* 包含在您的 index.html 页面中。

Ruby on rails 使用资产管道以自己的方式管理 javascripts。资产管道是一项伟大的技术，因为您可以使用 coffeescript 编写模块化的 javascripts。它可以预编译你的文件用于生产，等等。

不过，资产管道有两个主要缺点:

1.  **基本不可能在 rails** 之外使用。例如，每次我试图使用 sinatra 开发一个独立的基于资产的 ruby 应用程序，30 分钟后我就创建了一个 rails 应用程序，并且只使用资产管道。这是一个大问题，因为除了 ruby on rails 之外，没有人能使用这项技术，所以它永远不会成为前端开发的标准技术。
2.  **与 commonJs 模块**不兼容。资产管道有另一种方式*需要*模块，这是相当强大的，但是每当你在 npm 上找到一个好的模块，你不能 npm 安装它并在你的 rails 应用程序中使用它。

# 铁路

有两种常见的方法允许您在 rails 中使用 react:

1.  **铁轨道**。2014 年， [@bigardone](https://twitter.com/bigardone) 在[系列博客文章](http://codeloveandboards.com/blog/2014/08/24/rails-and-react-i-a-happy-medium/)中对此进行了大量记录。他解释了如何使用 *react_rails* gem，如何用 JSX + CoffeeScript 编码，猜猜会发生什么？有用！ [react_rails](https://github.com/reactjs/react-rails) gem 的最大好处是它允许你从 erb 实例化组件，并从 ruby 对象传递属性。这种方法的主要问题是它不能用于 npm 模块。
2.  **Rails+browserify 方式**:为了和 npm 模块一起使用 react，你需要一个 browser ify 这样的工具。 [browserify-rails](https://github.com/browserify-rails/browserify-rails) gem 允许您在标准 rails 资产中要求()模块。react 的完整方法在本文中有很好的记录，作者是 [@olance](https://twitter.com/olance) 。这种方法在理论上很棒，但是它假设在编译您的资产时已经安装了 *browserify* 。例如，如果您将应用程序部署到 heroku，或者使用 heroku buildpacks，情况就不一样了。因此，使用这种技术意味着要么为部署定制一个构建步骤，要么在部署之前预编译您的资产。您可能不希望这样做，尤其是如果您在现有项目中包含 react，而该项目的资产在部署期间是预编译的，并且您不希望在短期内对此进行更改。

# 简单的方法

在我看来，上面描述的方法有很大的缺点，尽管它们确实允许您在 rails 应用程序中执行一些 react 代码。我们可以采用的另一种方法是在开发中使用 browserify 和一个标准的 *package.json* 文件，并让它在您的 rails 资产中输出一个 *bundle.js* 文件:

1.  首先创建一个简单的*包. json* ，包括 react、 *browserify* 和 *reactify* ，如下所示:

The devDependencies part can be created incrementally with npm install — save-dev some_library, whereas the dependencies par can be updated using — save option.

2.如果你使用的是 turbolinks，你可能会在 head 标签中包含 *application.js* ，这对 react 来说不是很好。例如，您可以在*/app/assets/JavaScript s/react/application . js*中创建一个新的 js 文件，并使它像下面这样简单:

3.不要忘记通过编辑 */config/application.rb* 将其添加到您的预编译文件中

4.然后，您可以像普通 javascript 一样在任何 erb 视图中引用这个新文件:

5.更新你的*。gitignore* 。您应该忽略 *node_modules* 目录，因为它不会部署到生产中。你应该**不要**忽略*/app/assets/JavaScript s/react/bundle . js*，因为你希望它被部署到生产中。

6.运行 *npm start* 运行 react 应用程序

额外的构建步骤？然后，您可以运行 *npm start* ，使用 [watchify](https://github.com/substack/watchify) 实时更新 react 应用程序，您的 js 文件将在您的 rails 应用程序中更新。资产管道将负责缩小您的文件，这样您就不必添加额外的步骤来构建用于生产的文件。

# 结论

这种方法的有趣之处在于，它向您的 rails 应用程序添加了一个新的 *js* 文件，而不需要您进行供应商浏览或出售您的 npm 模块。您可以在应用程序的其余部分继续使用 jquery 和 turbolinks(因为您正在一些 erb 视图的底部加载新的 *js* 文件)。这让你[不必重写整个应用](https://www.youtube.com/watch?v=BF58ZJ1ZQxY)并在你的 rails 应用的一小部分上引入 react。

另一个好处是，如果您不处理 react 部分，您就不必运行 *npm start* 。相反，如果您在 react 部分工作，您将需要运行 *npm start* 来查看您的更改在开发时的效果，而不需要额外的部署步骤。因此，在部署到生产环境之前，不可能忘记构建这个 javascript。