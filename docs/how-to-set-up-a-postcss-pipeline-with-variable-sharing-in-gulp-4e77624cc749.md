# 如何在 gulp 中设置带有变量共享的 postcss 管道

> 原文：<https://medium.com/hackernoon/how-to-set-up-a-postcss-pipeline-with-variable-sharing-in-gulp-4e77624cc749>

## 利用 [postcss](http://postcss.org/) 和 [cssnext](http://cssnext.io/) 面向未来的 css，以及 css 和 js 之间的变量共享，从头开始创建一个样式管道。

用 gulp 为 postcss 设置一个基本的[工作流](https://hackernoon.com/tagged/workflow)相当简单。Cssnext 还允许我们在一个中央 [javascript](https://hackernoon.com/tagged/javascript) 文件中指定变量，这些变量可以在整个代码库中使用。这意味着您可以进一步标准化样式，对于 css 和 javascript 中的样式有时是不可避免的(例如数据可视化)。

我将主要关注提供对变量、自定义媒体查询和部分导入的支持。为了使本教程保持合理的简短，我不能涵盖更多，但我希望到最后你会有足够的概述，能够添加自己的插件，以利用 postcss 的更高级的功能。

这个例子的源代码可以在 [github](https://github.com/zimrick/postcss-starter-gulp) 上找到。

# 1.吞咽的基本设置

要开始使用 npm 初始化一个新文件夹。在这个文件夹中，你需要一个 src 和 css 文件夹。src 文件夹将包含未编译的 css，css 文件夹将包含已处理/已编译的 css 文件。

```
$ mkdir postcss-starter-gulp && cd postcss-starter-gulp
$ npm init
...
$ mkdir src css
```

# 2.添加 gulp 依赖项

接下来，你将不得不安装 gulp 和一些额外的 gulp 包。确保它们都列在`package.json`中。

```
$ npm install gulp gulp-postcss autoprefixer postcss-cssnext postcss-partial-import --save-dev
```

# 3.添加 gulpfile

在项目的根目录下创建一个`gulpfile.js`。文件应该是这样的:

```
*// gulpfile.js*var gulp = require("gulp")
var postcss = require("gulp-postcss")
var partialImport = require("postcss-partial-import")
var cssnext = require("postcss-cssnext")gulp.task("css:build", function() {
    var processors = [
        partialImport(),
        cssnext({
            browsers: ["> 1%"],
        })
    ]
    return gulp.src("./src/main.css")
               .pipe(postcss(processors))
               .pipe(gulp.dest("./css"))
})gulp.task("default", function() {
    gulp.watch("./src/**/*.css", ["css:build"])
})
```

我们的 gulpfile 包含两个任务，一个用于构建 css 文件，另一个默认任务用于观察 src 目录的变化。当 src 文件夹中的任何内容发生变化时，观察器将运行 build 命令。构建任务将在 src 目录中查找 main.css 文件，并使用指定的处理器(在本例中为 partialImport 和 cssnext)处理该文件。 [Cssnext 将对 css 进行改造，以兼容市场份额超过 1%的浏览器(根据 caniuse 数据库)](http://cssnext.io/usage/#browsers)。处理后的 css 将被输出到`./css`目录。

为了在不全局安装 gulp 的情况下运行我们项目中的 gulp 任务，我们需要向我们的`package.json`添加一些 npm 脚本。

```
*// package.json*...
  "scripts": {
    "css:watch": "gulp",
    "css:build": "gulp css:build", 
    "test": "echo \"Error: no test specified\" && exit 1"
  },
...
```

现在我们可以使用 npm、`$ npm run css:watch`和`$ npm run css:build`运行 gulp 任务。

# 4.添加示例 css

在 src 目录中创建一个 main.css 文件，并添加一些示例 css(如下所示)来检查编译过程。

```
*// ./src/main.css*:root {
  --color-blue: #2196F3;
  --color-pink: #E91E63;
}@custom-media --md-viewport (min-width: 62em);.blue-container {
  background: var(--color-blue);
}[@](http://twitter.com/media)media (--md-viewport) {
  .blue-container {
    background: var(--color-pink);
  }
}
```

一旦你通过 npm 运行吞咽任务…

```
$ npm run css:build
```

…上面的 css 应该成功编译成这样:

```
*// ./css/main.css*.blue-container {
  background: #2196F3;
}[@](http://twitter.com/media)media (min-width: 62em) {
  .blue-container {
    background: #E91E63;
  }
}
```

# 5.添加 js 变量和 js 媒体查询

更进一步，我们可以让变量`color-blue`和`color-pink`对 css 文件和 javascript 文件都可用。为此，你必须创建一个`./src/styleVariables.js`，一个`./src/mediaQueries.js`，并修改`./src/main.css`和`gulpfile.js`。

首先，从 main.css 中删除所有变量定义和自定义媒体查询定义。

```
*// ./src/main.css*.blue-container {
  background: var(--color-blue);
}[@](http://twitter.com/media)media (--md-viewport) {
  .blue-container {
    background: var(--color-pink);
  }
}
```

接下来，在`./src/styleVariables.js`中的一个简单 javascript 对象中定义变量。

```
*// ./src/styleVariables.js*module.exports = {
  "color-blue": "#2196F3",
  "color-pink": "#E91E63",
}
```

然后在`./src/mediaQueries.js`中将您的媒体查询也定义为一个简单的 javascript 对象。

```
*// ./src/mediaQueries.js*module.exports = {
  "md-viewport": "(min-width: 62em)",
}
```

最后，您必须让 cssnext 处理器知道 styleVariables.js 和 mediaQueries.js 的存在。为此，将`styleVariables.js`和`mediaQueries.js`导入 gulpfile，并将其插入 cssnext 选项。

```
*// gulpfile.js*var gulp = require("gulp")
var postcss = require("gulp-postcss")
var partialImport = require("postcss-partial-import")
var cssnext = require("postcss-cssnext")**var styleVariables = require("./src/styleVariables")
var mediaQueries = require("./src/mediaQueries")**gulp.task("css:build", function() {
    var processors = [
        partialImport(),
        cssnext({
            browsers: ["> 1%"],
            **features: {
              customProperties: {
                variables: styleVariables,
              },
              customMedia: {
                extensions: mediaQueries,
              }
            }**
        })
    ]
    return gulp.src("./src/main.css")
               .pipe(postcss(processors))
               .pipe(gulp.dest("./css"))
})gulp.task("default", function() {
    gulp.watch("./src/**/*.css", ["css:build"])
})
```

所以现在你有了一个简单的 postcss 处理器，它允许你在 css 和 js 之间共享变量。对于可以添加到这个设置中的其他酷东西，请查看 [postcss github repo](https://github.com/postcss/postcss#plugins) 。

你可以在 [github](https://github.com/zimrick/postcss-starter-gulp) 上找到本教程使用的代码。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个大家庭的一员。我们现在[正在接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！