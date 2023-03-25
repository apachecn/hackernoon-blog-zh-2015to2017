# 重组一个目录结构来解决“找不到模块/预置/插件”的问题

> 原文：<https://medium.com/hackernoon/reorganising-a-directory-structure-to-solve-cannot-find-module-preset-plugin-in-grunt-with-babel-891030b1c501>

你好

我的组织有一个项目目录结构，如:

*   grunt/Gruntfile.js
*   grunt/package.json
*   grunt/节点模块/
*   web/index.html *(嗯，。php)*
*   web/js/script.js

对于我们的 **grunt** [toolchain](https://hackernoon.com/tagged/toolchain) 用于 SCSS、JS 等已经很好了。

当我通过**浏览器确认**添加 JSX 和[反应](https://hackernoon.com/tagged/react) 时，我发现我遇到了一个*“运行咕噜-浏览器确认时出错”*

*> >错误:无法从“/directory/of/source/jsx”中找到模块“react”*

可能有一个简单的方法来告诉 **grunt-browserify** (或者类似的，我在调查替代方案时也有同样的错误)node_modules/ for modules、presets 和 plugins 的位置，但是在我发现它是什么之前，我已经重新组织到:

*   /Gruntfile.js
*   /package.json
*   /节点 _ 模块
*   网页/索引. html
*   web/js/react-app.js

并更改了我的 **Gruntfile.js** 以反映 web/* source files 文件夹位于子目录(web/)中，而不是兄弟目录(../web/)。

*> >捆绑 web/js/react-app.js 创建。*

我宁愿不移动我的目录，但是现在，我的强迫症可以忍受父目录的一点污染。