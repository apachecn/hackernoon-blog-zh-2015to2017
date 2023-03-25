# 用 Node.js 构建一个简单的静态页面生成器

> 原文：<https://medium.com/hackernoon/building-a-simple-static-page-generator-with-node-js-4f58f680c47d>

在开发相当简单的网站时，静态页面生成器非常棒，而且超级方便。我已经使用了大概两到三个看起来像是现在可用的 100 个中的[。但对我来说，使用一个工具是不够的，这是通常的情况，因为我真的喜欢构建自己的工具来了解幕后发生的事情。因此，今天我将与你分享我自己的方法，用 Node.js 创建一个简单的静态页面生成器。](https://www.staticgen.com/)

![](img/e564521429fa528453475cb5e1c79fba.png)

Photo by [Farzad Nazifi](https://unsplash.com/photos/p-xSl33Wxyc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

## 步骤 0:成分列表(也称为依赖性和须知)

*   我假设大多数人都熟悉创建一个包，所以我不打算在这里深入讨论。如果您需要一些指导，请看一下[文档](https://docs.npmjs.com/cli/init)。
*   `[npm i -s live-server](https://www.npmjs.com/package/live-server)` —生成网站的服务器，支持热重装。这个包可以替代任何其他热重装服务器包，但我非常喜欢它，因为它非常简单，可以完成工作。
*   `[npm i -s nodemon](https://www.npmjs.com/package/nodemon)` —监视文件并运行指定的任务。当文件改变时，我们将使用这个包自动重建我们的静态网站。
*   `[npm i -s concurrently](https://www.npmjs.com/package/concurrently)` —脚本/任务的并发执行是一切顺利运行所必需的。我很确定有很多很棒的包可以完成同样的事情，但是我对这个特别的包很熟悉，所以我会坚持使用它。
*   节点的[文件系统模块](https://nodejs.org/api/fs.html#fs_file_system)和`[npm i -s fs-extra](https://www.npmjs.com/package/fs-extra)` —文件系统模块是读/写/删除文件所必需的，所以我们将使用它来实际生成静态网站。是在此基础上的一个很好的扩展，允许你执行更多的操作，比如删除整个文件夹而不管内容，这特别有用。
*   我们将在 Markdown 中编写页面内容，所以我们将使用这个包作为它们的解析器。非常简单，如果你需要的话，它有很多插件。
*   [Javascript 模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) —如果你不熟悉模板文字，不要担心。对于本文的目的，您需要知道的是，您可以使用反斜杠(```)来编写多行字符串，并且使用`${expression}`将会用内部表达式的结果替换美元符号和花括号。

[](https://dev.to/sarah_chima/an-introduction-to-es6-template-literals-94l) [## ES6 模板文字简介

### 还记得当您必须在一个字符串中插入几个变量时，JavaScript 中的编码有多糟糕吗？哦，数不清的…

开发到](https://dev.to/sarah_chima/an-introduction-to-es6-template-literals-94l) 

## 步骤 1:创建必要的目录和文件

在我们进入代码之前，我们需要为整个项目定义一个结构。请记住，这是我对这个问题的*看法*，它绝不是唯一或最好的看法，所以请随意做任何你喜欢的调整:

*   目录:在这个目录中，我将添加我的页面，用 Markdown 编写。然后这些将被转换成 HTML 文件，使用适当的脚本。
*   **pages_meta** 目录:我觉得通用元数据不是最好的主意，所以我将使用这个目录中的 JSON 文件来添加特定于每个页面的元数据。除了文件扩展名之外，每个 JSON 文件的名称必须与 pages 目录中的 markdown 文件的名称完全匹配(例如，`index.json`是`index.md`的元数据文件)。JSON 文件的内容如下所示:

JSON file metadata structure

*   **build_scripts** 目录:我将在这里添加脚本，将 Markdown 和 JSON 文件转换成 HTML 文档。我将使用两个文件，一个是 **builder.js** 脚本，它将是静态页面生成器的主要入口点，另一个是 **page_template.js** ，我将使用它作为呈现每个页面的模板。将代码分成两个文件可能是一个好主意，因为根据某些条件，您可能会扩展构建文件中的逻辑以使用不同的模板，因此这允许您独立于核心构建逻辑来处理页面呈现。
*   **css** 、 **js** 和 **images** 目录:这些目录包含页面将依赖的 css 文件、它们可能使用的脚本和可能显示的图像。它们都将被复制到输出目录中。
*   **构建**目录:这是静态页面生成器的输出目录。您生成的静态页面和所有需要复制的文件都将保存在这个文件夹中。您不应该手动更改这里的任何内容，因为构建脚本应该在输出每个新构建之前清理这个文件夹。

## 步骤 2:设置环境

我们几乎准备好开始写一些代码，但是首先我们需要在我们的`package.json`中定义脚本，这样我们就可以启动我们的静态页面生成器。我设置我的方式是这样的:

```
"build-pages": "node ./build_scripts/builder.js",                           "start": "concurrently --kill-others \"nodemon -e js,json,css,md -i build -x \\\"npm run build-pages\\\"\" \"live-server ./build\""
```

首先，我们需要`build-pages`脚本来告诉 Node.js 运行我们的构建脚本。一旦我们编写了必要的代码，这将从我们的文件生成静态网站。

另一个剧本，`start`，稍微复杂一些。我们使用`concurrently`同时运行两个进程。第一个是`nodemon`，我们明确地让它监视扩展名为`js, json, css, md`的文件中的变化，并忽略`build`目录。一旦`nodemon`看到变化，它就会执行`build-pages`脚本，这样静态网站就会重建。第二个进程正在从`build`文件夹中运行`live-server`。这就是我们启动和运行静态页面生成器所需要的全部内容。现在我们只需要编写一些代码，将 Markdown 和 JSON 文件转换成 HTML 文档。

## 第 2.5 步:喝杯咖啡(可选，但强烈推荐)

如果你已经做到了这一步，你做得很好！在接下来的几个步骤中，您将编写决定最终结果的代码，所以您可能想在开始工作之前喝杯咖啡(或任何其他饮料)。

![](img/b34d902040184af25d58ac327b79bde7.png)

Coffee break

## 步骤 3:为你的页面创建一个模板

请记住，我们使用`markdown-it`将我们的 Markdown 文件呈现为 HTML。然而，我们还需要输出一堆不是由`markdown-it`创建的 html 文档的部分，比如元数据和 HTML 的包装标签、head 和 body。我们将使用我们的 **page_template.js** 文件来做到这一点，并通过在模板中创建一个默认的元数据对象来确保没有元数据丢失。

我不会详细解释每一个小步骤，但在向您展示我的源代码之前，我想强调一件非常重要的事情:

```
`${pageMeta.hasOwnProperty('stylesheets')                                  
  ? pageMeta.stylesheets.length                                 
    ? pageMeta.stylesheets.map(value => `<link rel="stylesheet" href="${value}">`)                                 
    : ''                               
  : this.defaultMeta.stylesheets.map(value => `<link rel="stylesheet" href="${value}">`)                           
}`
```

在上面的代码片段中，您可以看到如何利用表达式和嵌套模板文本在最终的 HTML 文档中创建多个元素。简而言之，它判断应该使用模板中定义的默认`stylesheets`属性还是为特定页面定义的属性，然后继续生成链接到样式表列表中定义的每个 CSS 文件的适当标签。我相信这个例子展示了模板文字有多强大，以及使用它们可以完成多少工作。

话虽如此，我们正在创建的模板必须为每个页面处理一些事情:

*   输出`<!DOCTYPE html>`，文档开始和结束的适当的`<html>`标签，以及`<head>`和`<body>`标签。
*   根据提供的元数据，输出一个合适的`<title>`。
*   从提供的元数据中输出所有适当的`<meta>`标签。
*   根据需要，输出脚本和样式表的`<script>`和`<link>`标签列表。
*   最后，输出从`<body>`中的 Markdown 文件生成的 HTML。

在实现了所有这些特性之后，你应该得到类似于[我的模板](https://github.com/Chalarangelo/node-static-page-generator/blob/master/build_scripts/page_template.js)的东西。

## 步骤 4:创建构建脚本

你就快到了！现在我们只需要编写构建脚本，它将根据需要把所有的东西放在一起。请记住，有多种方法可以做到这一点，您肯定可以调整这个脚本来创建更复杂的网站和处理不同的情况，但现在我们将坚持一个非常基本的实现，它只会做我认为构建静态网站所必需的事情:

1.  加载`fs-extra`、`path`和你的`./page_template`。
2.  声明您的 **pages** 、 **pages_meta** 、 **css** 、 **js** 、 **images** 和 **build** 目录相对于您的`package.json`文件的路径。我们将在后面的代码中使用这些来减少混乱。
3.  使用同步方法，比如对所有事情都使用`[readdirSync](https://nodejs.org/api/fs.html#fs_fs_readdirsync_path_options)`。这将确保我们的代码从上到下运行。如果你对自己的异步编程技能有信心，那么就继续下去，异步实现所有的东西，不管怎样，这可能更有效率。
4.  清理**建立**目录。
5.  循环遍历 **pages** 目录中的所有页面，并将它们存储在一个列表中。对 **pages_meta** 目录中的元数据文件执行相同的操作。
6.  使用 Markdown 和 JSON 文件作为模板文件的`generatePage()`函数的参数来生成每个页面。
7.  将 **css** 、 **js** 和 **images** 目录原样复制到 **build** 目录中。

这就是让构建过程正常工作所需要做的全部工作。如果你做的一切都正确，你应该会得到一个类似于[这个](https://github.com/Chalarangelo/node-static-page-generator/blob/master/build_scripts/builder.js)的构建脚本。

## 第五步:发挥创意，建立你一直想要的网站

如果您一直坚持下去，现在应该已经有了一个静态页面生成器。现在有趣的部分开始了！你可以随心所欲地定制它，创建你想要的内容，并添加你需要的所有功能，但在其他地方找不到。没有什么是你做不到的！

我对改进和试验它的建议:

*   添加将在特定条件下应用的第二个模板。
*   向模板文件添加导航栏、页眉或页脚，为静态网站创建应用程序外壳。
*   调整`markdown-it`的[配置](https://markdown-it.github.io/markdown-it/#MarkdownIt.configure)或者增加几个[插件](https://www.npmjs.com/browse/keyword/markdown-it-plugin)。
*   将构建脚本转换为异步脚本。
*   支持不同格式的页面和文件。

*本文的完整源代码可以在*[*Github*](https://github.com/Chalarangelo/node-static-page-generator)*上找到。记住启动存储库，这样您可以在以后找到返回的方法！*