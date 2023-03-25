# 与 Heroku 一起使用 Bower

> 原文：<https://medium.com/hackernoon/using-bower-with-heroku-cdc791320c88>

![](img/81e1fc362a56a501206e27b0acf37ee7.png)

Y es，我用 [Bower](http://bower.io) 来维护前端依赖关系，大多是为了我身边的项目。无论你说什么，Bower 是好的，我太急于弄乱前端开发的 npm 依赖关系。另外，我觉得维护与 Bower 的前端依赖关系有助于保持我的应用程序框架的整洁。

所以在我最近的两个项目中，我一直使用 Heroku 进行部署。因为我使用 Bower 维护前端依赖，所以我浏览了这篇关于在 Heroku 上生成构建包的文章。我发现生成构建包增加了应用程序部署过程中不必要的复杂性，至少对于快速原型制作来说是这样。因此，我选择了不同的道路。将 Bower 添加为 npm 依赖项似乎是一个简单的过程，不会增加任何复杂性。我发现只有当您使用 Bower 快速构建 JavaScript 应用程序时，它才是最棒的。

在您完成构建您的应用程序之后，您所要做的就是在`package.json`文件中添加`Bower`作为 npm 依赖项，然后在`npm scripts`下添加一个脚本来执行一个命令，该命令将在应用程序部署到 Heroku 之前安装 bower 依赖项。

在`package.json`中作为依赖的凉亭:

```
"dependencies": {
     "bower": "1.8.0" 
}
```

然后在`npm scripts`中添加一个`postinstall`命令:

```
"scripts": {
    "postinstall": "./node_modules/bower/bin/bower install" 
}
```

你可以在这里阅读更多关于定制 [**Heroku 构建过程**](https://devcenter.heroku.com/articles/nodejs-support#customizing-the-build-process) **。**

[***网站***](http://amandeepmittal.github.io) ***|见上*** [***推特***](http://twitter.com/amanhimself) ***|我的文章上***[***Hackernoon.com***](https://hackernoon.com/@amanhimself)