# <helloreact>第 1 天—从零开始反应— 30 天的反应</helloreact>

> 原文：<https://medium.com/hackernoon/helloreact-part-1-getting-started-with-react-bd9938d95bc0>

你想开始[学习](https://hackernoon.com/tagged/learning)react 的基础知识而不陷入创建开发环境的困境吗？

如果你是 web 开发的新手，当你刚刚尝试学习 React 或第一次学习 React 时，建立一个[开发](https://hackernoon.com/tagged/development)环境可能会让你感到有点害怕。

> 如果[您喜欢本课程，请利用免费提前访问我的课程](http://helloreact.io/p/hello-react/?product_id=324291&coupon_code=30DAYSFREE)或看看下面的视频课程:

[If you like this lesson please take advantage of free early access to my course](http://helloreact.io/p/hello-react/?product_id=324291&coupon_code=30DAYSFREE) or take a look at this video lesson here.

在本文中，我们将了解如何开始使用 React，只使用文本编辑器和浏览器，不使用其他工具。

# #1 —用 Emmet 设置锅炉板代码

让我们从第一步开始。我们将从浏览器中的一个名为“index.html”的文件开始。我们将从锅炉板代码 HTML 代码开始。作为一个快速的开始，我推荐使用 [Emmet](https://emmet.io/) 和你有的任何文本编辑器，在第一行输入`html:5`，然后按 shift 键得到下面的代码。或者您可以继续从下面复制并粘贴代码。

我们可以把标题填成“大家反应一下！”。

```
<title>Let's React</title>
```

此内容不会出现在您的网页中。HTML 文件头部分的任何内容都是元数据，我们的浏览器将使用它来解释正文部分的代码。这个标题将会出现在我们页面的标签上，而不是真正出现在页面上。

# #2 获取脚本标签以利用 React 和 Babel 库的能力

好了，第一项从我们的清单上划掉了。让我们看看第二项。我们将通过使用脚本标签引入 React 和 Babel 来设置我们的开发人员环境。这不是真实的开发环境。这将是一个相当复杂的设置。它还会给我们留下大量的模板代码和库，让我们远离学习 React 基础知识的主题。本系列的目标是复习 React 的基本语法并直接进入编码。

我们将使用

```
<head>
...
<!-- REACT LIBRARY -->
<script src="[https://unpkg.com/react@15.5.4/dist/react.js](https://unpkg.com/react@15.5.4/dist/react.js)"></script>
<!-- REACT DOM LIBRARY -->
<script src="[https://unpkg.com/react-dom@15.5.4/dist/react-dom.js](https://unpkg.com/react-dom@15.5.4/dist/react-dom.js)"></script>
<!-- BABEL LIBRARY -->
<script src="[https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/6.25.0/babel.min.js](https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/6.25.0/babel.min.js)"></script>
...
<title>Let's React</title>
</head>
```

随着这些库的更新版本的出现，您可以自由地使用它们。他们不应该对我们报道的内容做任何突破性的改变。

我们在这里做什么？

**HTML <脚本>元素**用于嵌入或引用可执行脚本。“src”属性指向 React 库、ReactDOM 库和 Babel 库的外部脚本文件。

这就像你有一把电动剃须刀。除非你能把电动剃须刀插在墙上通电，否则不管它有多漂亮，对你来说都毫无用处。如果我们的浏览器不能插入这些库来理解和解释我们要做的事情，那么我们将要编写的 react 代码将毫无用处。

这就是我们的应用程序将如何获得 React 的能力，这将是我们将 React 插入 Dom 的方式。我们将 React 和 ReactDOM 作为两个不同的库的原因是因为存在 React Native 这样的用例，在这些用例中，移动开发不需要渲染到 DOM，所以该库被拆分，以便人们根据他们正在进行的项目来决定他们需要什么。因为我们将需要 React 来创建 DOM，所以我们将使用这两个脚本。

Babel 是我们如何利用 Es5 之外的 ECMA 脚本，并处理我们将在 React 中使用的称为 JSX (JavaScript as XML)的东西。我们将在接下来的课程中深入探讨巴别塔的魔力:)

好了，我们已经完成了第 1 步和第 2 步。我们已经建立了我们的锅炉板代码，并建立了我们的开发环境。

# **#3 渲染对 DOM 的反应**

我们接下来的两个步骤将是在 DOM 中选择我们想要呈现 react 内容的位置。并在正文中使用另一个脚本标签作为我们的 React 内容。一般来说，作为一个良好的关注点分离实践，这将在它自己的文件中，然后链接到这个 html 文档。我们将在后面的课程中介绍这一点。

现在，我们让它停留在我们当前所在的 html 文档的主体中。

现在我们来看看在 DOM 上选择一个位置来呈现我们的 React 内容是多么简单。

我们会进入身体内部。最佳实践不仅仅是将 React 放入要显示的 body 标记中，而是创建一个单独的元素，通常是一个 div，您可以将它作为根元素来插入 React 内容。

```
<body>
  <div id="app>React has not rendered yet</div>
</body>
```

我们将创建一个简单的

元素，并给它一个“app”的 id。我们将能够定位到这个位置来插入我们的 React 内容，就像您可以使用 CSS 来定位您选择的样式的 id 一样。

任何 react 内容都将在 id 为 app 的 div 标记中呈现。与此同时，我们会留下一些文字说“react 尚未渲染”，如果我们在预览页面时看到这一点，这意味着我们在某些地方错过了渲染 React。

现在，让我们继续在我们的主体中创建一个脚本标记，我们将在这里第一次使用 react 创建。我们脚本标签需要的语法是添加一个“type”属性。这指定了脚本的媒体类型。上面我们使用了一个 src 属性，它指向 React 库、ReactDOM 库和 Babel 库的外部脚本文件。

```
<body>
  <div id="app>React has not rendered yet</div>
  <script type="text/babel"> </script></body>
```

我们将使用的脚本的“类型”用引号括起来，并将其设置为“文本/巴别塔”。

当我们与 JSX 合作时，我们需要马上使用巴贝尔的能力。首先，我们将渲染 DOM 的 React。我们将使用 ReactDOM.render()方法来实现这一点。这将是一个方法，记住方法只是一个附加在对象上的函数。这个方法需要两个参数。

```
<body>
  <div id="app>React has not rendered yet</div>
  <script type="text/babel"> ReactDOM.render(React What, React Where);</script></body>
```

第一个争论是反应的“什么”。第二个参数是您希望它在 DOM 中放置的位置的“位置”

让我们从调用 ReactDOM.render()方法开始。

我们的第一个论点是我们的 JSX。

```
<body>
  <div id="app>React has not rendered yet</div>
  <script type="text/babel"> ReactDOM.render(
    <h1>Hello World</h1>, 
    React Where
  );</script></body>
```

react 官方文档称“这个有趣的标记语法既不是字符串也不是 HTML。它被称为 JSX，是 JavaScript 的语法扩展。我们建议将它与 React 一起使用来描述 UI 应该是什么样子。JSX 可能会让你想起模板语言，但它具有 JavaScript 的全部功能。JSX 生产反应“元素”

很多时候，JSX 让已经开发了一段时间的人感到害怕，因为它看起来像 HTML。在很小的时候，开发人员就被教导关注点分离。HTML 有它的位置，CSS 有它的位置，JavaScript 有它的位置。JSX 似乎模糊了界限。你正在使用看起来像 HTML 的东西，但正如脸书所说，它具有 JavaScript 的全部功能。

这可能会吓坏退伍军人，所以许多反应教程开始没有 JSX，这可能是相当复杂的。我们不会那么做的。因为这个课程是针对那些在事业上非常年轻的人的，所以当你看到这个语法时，你可能不会感到危险。

JSX 非常直观。您可能很容易阅读这段代码，并看到这将是显示文本“Hello World”的最大的 header 标记。没有神秘感，非常简单。

现在，让我们看看第二个论点是什么。

```
<body>
  <div id="app>React has not rendered yet</div>
  <script type="text/babel">ReactDOM.render(
    <h1>Hello World</h1>, 
    Document.getElementById("app)
  );</script></body>
```

这是我们希望 react 内容呈现给 dom 的地方。你可能在过去已经这样做过很多次了。我们只需输入 document.getElementById()。我们将进入应用程序 id 的讨论。就是这样。我们现在将把 id 为 app 的 div 作为目标来插入我们的 react 内容。

我们希望确保我们的内容得到保存。继续在浏览器中打开它，你应该会看到“Hello World”。您可能已经猜到，使用 React 不是创建 Hello World 应用程序的最快或最好的方式。我们还没有看到它的好处。但是现在，我们知道一切都在运转。

继续打开控制台，查看“元素”。你可以在 mac 上用 command + shift + j 或者在 Windows 和 Linux 上用 Ctrl + Shift + J

如果你点击 head 标签，我们可以看到我们包含的脚本库。然后我们可以进入文档的主体。让我们点击 id 为“app”的 div。当我们这样做时，我们会看到我们的

# 标签，内容为“Hello World”。

让我们快速回顾一下。在我们的 head 标签中，我们抓取了 React、React DOM 和 babel 的脚本标签。这些是我们的浏览器在元数据中需要的工具，用来读取我们的 react 代码和 JSX。

然后，我们通过创建一个 id 为“app”的元素 div，在 DOM 中找到想要插入 React 的位置。

接下来，我们创建了一个脚本标签来输入我们的 react 代码。我们使用了带有两个参数的 ReactDOM.render()方法。react 内容的“什么”,在本例中是我们的 JSX，第二个参数是您希望将 React 内容插入 DOM 的“位置”。在这种情况下，它是 id 为“app”的位置。

您可以在此处访问本课的最终代码:

[](https://github.com/robgmerrill/hello-react/blob/master/section-one/index.html) [## robgmerrill/hello-react

### hello-react - Hello React 课程代码库

github.com](https://github.com/robgmerrill/hello-react/blob/master/section-one/index.html)