# 使用 browserify 构建 angular app

> 原文：<https://medium.com/hackernoon/building-angular-app-using-browserify-704a7947a09e>

这篇文章不是关于`Angular js`的[教程](https://hackernoon.com/tagged/tutorial)，网上有很多，我建议你去看看。这更多的是关于`Angular js`的设置，这样你就可以构建易于长期维护和管理的应用。

# 从`grunt`和`browserify`开始

如果你曾经使用过像`ember.js`这样的东西，你可能已经注意到所有的东西都在正确的文件夹里，并且可以从盒子里取出来并实时重新加载等等。有角度的就不一样了，所以很多人最终都做错了。让我们从创建一个`package.json`来维护所有的依赖关系开始

```
npm init --yes
```

现在假设您已经在全球范围内安装了`grunt-cli`,接下来您要做的就是。安装实时重新加载和构建应用程序所需的软件包。

```
npm i --save-dev grunt grunt-browserify grunt-contrib-copy grunt-contrib-watch
```

显然，如果你开发应用程序有一段时间了，你会知道`scss`和`jade`是你有用的朋友。让我们添加他们也需要的构建工具。如果你想使用 html 和 css，你可以去掉这一步。

```
npm i --save grunt-contrib-jade grunt-contrib-sass
```

不用说，如果你正在使用`grunt-contrib-sass`，确保你已经安装了用于`scss`的 ruby 模块。

# 安装`angularjs`和`angular-route`

在最后一步中，我们只添加了构建所需的依赖项，这是安装 angular 和 ngRoute 的时间。

```
npm i --save angular angular-route
```

请注意，我没有将它们添加到开发依赖项中。

# 准备好文件夹结构

这是一个小的应用程序结构，我更喜欢用它来保持我的代码的可管理性

```
.
├── app.js
├── components
│   ├── nav.jade
├── controllers
│   ├── index.js
│   └── testController.js
├── factory
│   └── index.js
├── index.jade
├── route.js
├── style
│   ├── main.scss
└── templates
    └── application.jade
```

你总是可以把它改成适合你的。这是在我的 npm 项目的一个`app`文件夹中。

# 构建项目

在你的根文件夹中编辑`Gruntfile.js`，包含`package.json`的文件夹，放入以下内容。

```
module.exports = function(grunt){
    grunt.initConfig({
      pkg: grunt.file.readJSON('package.json'),
      browserify: {
        'dist/app.js': ['app/app.js']
      },
      jade :{
        compile: {
          options: {
            client: false,
            pretty : false
          },
          files: [{
            cwd: "app",
            src: "**/*.jade",
            dest: "dist",
            expand: "true",
            ext: ".html"
          }]
        }
      },
      watch: {
        all: {
          options: {livereload: true},
          files: ['app/**'],
          tasks: ['jade','sass','browserify']
        }
      },
      sass:{
        dist: {
          files:{
            'dist/main.css':'app/style/main.scss'
          }
        }
      },
      copy: {
        main: {
          expand: true,
          cwd: 'static',
          src: '**',
          dest: 'dist/static',
        },
      }
    }); grunt.loadNpmTasks('grunt-contrib-jade');
  grunt.loadNpmTasks('grunt-contrib-sass');
  grunt.loadNpmTasks('grunt-contrib-watch');
  grunt.loadNpmTasks('grunt-browserify');
  grunt.loadNpmTasks('grunt-contrib-copy');

  grunt.registerTask('default', ['copy','jade','sass','browserify','watch']);
};
```

这就是我们构建`app`所需的全部内容，它将被`grunt`构建并放入`dist`文件夹。

# 在 angular app 中放一些实际内容

我只是要做一个简单的应用程序，导入导航。

```
<!--app/index.jade-->
doctype
html.no-js
  head
    meta(charset='utf-8')
    meta(http-equiv='X-UA-Compatible', content='IE=edge')
    base(href='/')
    title Application
    meta(name='description', content='')
    meta(name='viewport', content='width=device-width, initial-scale=1')
    link(rel='stylesheet',href='main.css')
  body(ng-app='application')
    div(ng-view)
    script(src='app.js')
    script(src='//localhost:35729/livereload.js')
```

`livereload`的最后一个脚本来自`grunt-watch`，当你修改代码时会重新加载页面。

```
// app/app.js
var angular = require('angular');
var ngRoute = require('angular-route');
var app = angular.module('application', [ngRoute]);require('./route')(app);
require('./controllers');
```

记住我们已经安装了`angular`和`angular-route`。

```
// app/routes.js
module.exports = function(app) {
  app.config(function($routeProvider, $locationProvider) {
    $routeProvider
      .when("/",{
        templateUrl: "templates/application.html"
      });
    $locationProvider.html5Mode(true);
  });
};
```

这是我们定义所有路线的文件。注意，`templates/application.html`是`templates/application.jade`的玉石输出。

```
// app/controllers/index.js
var app = require('angular').module('application');app.controller('testController',require('./testController.js'));
```

这是在`app/app.js`中导入的文件，我们需要在这里包含所有的控制器。在这种情况下，我用`testController`进行了演示，你可以从树形结构中看到。

# 组件呢？

这更多的是一些常见的可重复使用的东西，比如我保存的导航，这样我的模板目录就不会变得杂乱。`application.jade`中的示例，在本例中是登录页面，我可以使用

```
div(ng-include="'/components/nav.html'")
```

# 如何在浏览器中查看更改

首先你需要开始构建，简单地进入 npm 项目的根文件夹并从终端运行`grunt`。它将构建项目并开始寻找变化。

您可能还会看到一个名为`dist`的新目录。该目录是代码的最终构建输出。只需在该目录中启动一个`web-server`,并在浏览器中转到该 url，在那里您可以看到应用程序正在运行。

> *当您对代码进行任何更改时，grunt 会自动运行，浏览器会反映更新后的更改，而无需重新加载。*