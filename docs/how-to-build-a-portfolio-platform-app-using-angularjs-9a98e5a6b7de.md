# 如何使用 AngularJS 构建投资组合平台 App

> 原文：<https://medium.com/hackernoon/how-to-build-a-portfolio-platform-app-using-angularjs-9a98e5a6b7de>

![](img/24dd01a96a000a29893a9676c0864a03.png)

在本教程中，我将向你展示如何使用 AngularJS、一点 Node 和 Cosmic JS 创建一个“投资组合平台”应用程序。在本教程结束时，我们将有一个平台，使多个投资组合网站的管理。用户可以登录并管理他们的投资组合网站。管理员可以从 Cosmic JS 仪表板管理多个用户。让我们开始吧。

# TL；速度三角形定位法(dead reckoning)

[下载 GitHub repo。](https://github.com/cosmicjs/portfolio-platform)
[查看试玩。点击几下](https://cosmicjs.com/apps/portfolio-platform/demo)
[安装到你的宇宙 JS 桶。](https://cosmicjs.com/apps/portfolio-platform)

# 开始使用:

首先，让我们创建一个新目录来构建我们的项目，并创建一个 package.json 文件。

```
mkdir cosmicapp-porfoliocosmicapp-porfolio$ touch package.json
```

现在，在您的 package.json 中，复制并粘贴以下代码:

```
//cosmicapp-porfolio/package.json
{
  "name": "photography-portfolio-app",
  "version": "1.0.0",
  "main": "app-server.js",
  "engines": {
    "node": "4.1.2",
    "npm": "3.5.2"
  },
  "description": "",
  "dependencies": {
    "body-parser": "^1.17.2",
    "bower": "^1.7.7",
    "buffer-to-vinyl": "^1.1.0",
    "express": "^4.13.3",
    "gulp": "^3.9.1",
    "gulp-autoprefixer": "^3.1.0",
    "gulp-concat": "^2.6.0",
    "gulp-concat-css": "^2.2.0",
    "gulp-env": "^0.4.0",
    "gulp-minify-css": "^1.2.4",
    "gulp-ng-config": "^1.4.0",
    "gulp-npm-script-sync": "^1.1.0",
    "gulp-webserver": "^0.9.1",
    "http-server": "^0.9.0",
    "stripe": "^4.22.0",
    "wiredep": "^3.0.0"
  },
  "scripts": {
    "postinstall": "bower install && gulp config && gulp js",
    "start": "npm run production",
    "production": "node app-server.js",
    "gulp": "gulp"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "gulp-npm-script-sync": "^1.1.0",
    "gulp-remote-src": "^0.4.2"
  }
}
```

其次，我们来做一个 bower.json 文件。

```
cosmicapp-porfolio$ touch bower.json
```

现在，在您的 bower.json 中，复制并粘贴以下代码:

```
//cosmicapp-porfolio/bower.json
{
  "name": "photography-portfolio-app",
  "description": "Photography Portfolio App",
  "version": "0.0.0",
  "homepage": "https://github.com/kutsaniuk/cosmicapp-porfolio",
  "license": "MIT",
  "private": true,
  "dependencies": {
    "angular": "~1.5.x",
    "angular-mocks": "~1.5.x",
    "angular-bootstrap": "~1.1.x",
    "angular-cookies": "~1.5.x",
    "angular-route": "~1.5.x",
    "angular-ui-router": "0.2.x",
    "angular-resource": "1.5.x",
    "angular-animate": "~1.5.x",
    "ng-dialog": "0.6.1",
    "bootstrap": "3.3.x",
    "cr-acl": "",
    "angular-chosen-localytics": "*",
    "bootstrap-chosen": "*",
    "ng-flow": "^2.7.4",
    "angular-mask": "*",
    "checklist-model": "0.9.0",
    "angular-ui-notification": "^0.2.0",
    "angular-ui-calendar": "^1.0.2",
    "angular-ui-switch": "^0.1.1",
    "ng-scrollbars": "^0.0.11",
    "jquery.scrollbar": "*",
    "angular-flash-alert": "^2.4.0",
    "components-font-awesome": "^4.7.0",
    "angular-loading-bar": "^0.9.0",
    "angular-environment": "^1.0.8",
    "angular-dragdrop": "",
    "angular-sanitize": "1.5.*",
    "angular-drag-and-drop-directives": "",
    "jquery-ui-touch-punch": "",
    "angular-touch":"1.5.*",
    "textAngular":"1.5.*"
  },
  "resolutions": {
    "angular": "~1.5.x"
  },
  "devDependencies": {
    "cr-acl": "^0.5.0"
  }
}
```

配置应用服务器:

```
cosmicapp-porfolio$ touch app-server.js//cosmicapp-porfolio/app-server.js
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.set('port', process.env.PORT || 3000)
app.use(express.static(__dirname))
app.use(bodyParser.json())
var http = require('http').Server(app)
// Route
app.get('/', (req, res) => {
  res.sendFile(__dirname + '/index.html');
})
http.listen(app.get('port'), () => {
  console.log('Photography Portfolio App listening on ' + app.get('port'))
})
```

# 我们正在安装什么，为什么:

1.  我们将使用 AngularJS 框架来构建单页面应用程序
2.  我们正在为创建多视图安装 angular-ui-router。
3.  我们将使用 gulp 把所有的 js 和 css 文件构建成一个文件。

# 构建我们的应用程序:

现在我们将构建我们的文件结构，这样我们就可以组织我们的 angular 模块和 js 文件。这就是我们的 cosmicapp-portfolio 目录应该看起来的样子:

```
cosmicapp-porfolio
|----app
|       |----auth
|                 |----auth.ctrl.js
|                 |----auth.service.js
|       |----config
|                 |----config.js
|       |----portfolio
|                 |----about
|                           |----portfolio.about.mdl.js
|                 |----contact
|                           |----portfolio.contact.mdl.js
|                 |----intro
|                           |----portfolio.intro.mdl.js
|                 |----projects
|                           |----add
|                                   |----portfolio.projects.add.ctrl.js
|                           |----edit
|                                   |----portfolio.projects.edit.ctrl.js
|                           |----portfolio.projects.mdl.js
|                 |----settings
|                           |----portfolio.settings.ctrl.js
|                           |----portfolio.settings.mdl.js
|                 |----portfolio.ctrl.js
|                 |----portfolio.mdl.js
|                 |----portfolio.service.js
|       |----user
|                 |----user.service.js
|       |----main.mdl.js
|----dist
|       |----css
|       |----img
|       |----js
|----css
|----views
|----gulpfile.js
|----app-server.js
|----bower.json
|----package.json
```

现在我们将建立我们的 index.html。将以下代码复制并粘贴到您的 index.html 文件中:

```
<!DOCTYPE html>
<html lang="en" ng-app="main">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="description" content="">
    <meta name="author" content=""> <title>Photography Portfolio App</title> <!-- bower:css -->
    <!-- endbower --> <!-- Bootstrap Core CSS -->
    <link href="bower_components/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet"> <!-- Custom CSS --> <link href="dist/css/main.min.css" rel="stylesheet"> <!-- HTML5 Shim and Respond.js IE8 support of HTML5 elements and media queries -->
    <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
    <!--[if lt IE 9]>
    <script src="https://oss.maxcdn.com/libs/html5shiv/3.7.0/html5shiv.js"></script>
    <script src="https://oss.maxcdn.com/libs/respond.js/1.4.2/respond.min.js"></script>
    <![endif]--></head>
<body><div ui-view></div><!-- bower:js -->
<!-- endbower --><script src="dist/js/main.js"></script>
</body>
</html>
```

在这里，我们将把我们的“根”视图作为目标，稍后放置我们的角度模块。位于我们的 dist 目录中的 main.js 文件是我们的 gulpfile.js 文件在捆绑了我们所有的 angular 模块之后将产生的文件。现在，设置我们的 gulpfile.js 文件来捆绑我们所有的 js 文件，并将该捆绑文件导出到我们的 dist 目录中。将以下代码复制到 gulpfile.js 文件中:

```
//cosmicapp-porfolio/gulpfile.js
'use strict';'use strict';var gulp = require('gulp'),
    webserver = require('gulp-webserver'),
    minifyCSS = require('gulp-minify-css'),
    concat = require('gulp-concat'),
    wiredep = require('wiredep').stream,
    gulpNgConfig = require('gulp-ng-config'),
    autoprefixer = require('gulp-autoprefixer'),
    b2v = require('buffer-to-vinyl'),
    sync = require('gulp-npm-script-sync');sync(gulp);gulp.task('css', function () {
  return gulp.src('css/**/*.css')
    .pipe(minifyCSS())
    .pipe(concat('main.min.css'))
    .pipe(autoprefixer())
    .pipe(gulp.dest('dist/css'));
});gulp.task('js', function() {
    return gulp.src('app/**/**/*.js')
        .pipe(concat('main.js'))
        .pipe(gulp.dest('dist/js/'));
});gulp.task('config', function () {
    const json = JSON.stringify({
        BUCKET_SLUG: process.env.COSMIC_BUCKET,
        MEDIA_URL: 'https://api.cosmicjs.com/v1/' + process.env.COSMIC_BUCKET + '/media',
        URL: 'https://api.cosmicjs.com/v1/',
        READ_KEY: process.env.COSMIC_READ_KEY || '',
        WRITE_KEY: process.env.COSMIC_WRITE_KEY || '',
        DEFAULT_IMAGE: process.env.DEFAULT_IMAGE || ''
    });
    return b2v.stream(new Buffer(json), 'config.js')
        .pipe(gulpNgConfig('config'))
        .pipe(gulp.dest('app/config'));
});gulp.task('default', function () {
  gulp.watch('css/**/*.css', ['css']);
  gulp.watch('app/**/**/*.js', ['js']);
  gulp.watch('bower.json', ['bower']);
});gulp.task('bower', function () {
  gulp.src('index.html')
    .pipe(wiredep({
      directory: 'bower_components'
    }))
    .pipe(gulp.dest(''));
});
```

之后，我们可以创建主模块。将以下代码复制并粘贴到 main.mdl.js 文件中:

```
(function () {
    'use strict'; angular
        .module('main', [
            'ui.router',
            'ui.bootstrap',
            'ngMask',
            'ngCookies',
            'ngRoute',
            'ngDialog',
            'cr.acl',
            'ui-notification',
            'ngFlash',
            'ngAnimate',
            'textAngular',
            'flow',
            'angular-loading-bar',
            'ngDragDrop',
            'ngSanitize',
            'ngTouch', 'portfolio', 'config'
        ])
        .config(config)
        .run(run); config.$inject = ['$stateProvider', '$urlRouterProvider', 'cfpLoadingBarProvider', 'NotificationProvider', '$locationProvider'];
    function config($stateProvider, $urlRouterProvider, cfpLoadingBarProvider, NotificationProvider, $locationProvider) {
        cfpLoadingBarProvider.includeSpinner = false; NotificationProvider.setOptions({
            startTop: 25,
            startRight: 25,
            verticalSpacing: 20,
            horizontalSpacing: 20,
            positionX: 'right',
            positionY: 'bottom'
        }); $urlRouterProvider.otherwise(function ($injector) {
            var $state = $injector.get("$state");
            $state.go('login');
        }); $stateProvider
            .state('blog', {
                url: '/blog',
                templateUrl: '../blog.html'
            })
            .state('login', {
                url: '/login',
                templateUrl: '../views/auth/login.html',
                controller: 'AuthCtrl as auth',
                onEnter: ['AuthService', 'crAcl', function(AuthService, crAcl) {
                    AuthService.clearCredentials();
                    crAcl.setRole();
                }],
                data: {
                    is_granted: ['ROLE_GUEST']
                }
            })
            .state('register', {
                url: '/register',
                templateUrl: '../views/auth/register.html',
                controller: 'AuthCtrl as auth',
                onEnter: ['AuthService', 'crAcl', function(AuthService, crAcl) {
                    AuthService.clearCredentials();
                    crAcl.setRole();
                }],
                data: {
                    is_granted: ['ROLE_GUEST']
                }
            }); } run.$inject = ['$rootScope', '$cookieStore', '$state', 'crAcl'];
    function run($rootScope, $cookieStore, $state, crAcl) { $rootScope.globals = $cookieStore.get('globals') || {}; crAcl
            .setInheritanceRoles({
                'ROLE_ADMIN': ['ROLE_ADMIN', 'ROLE_GUEST'],
                'ROLE_USER': ['ROLE_USER', 'ROLE_GUEST'],
                'ROLE_GUEST': ['ROLE_GUEST']
            }); crAcl
            .setRedirect('login'); if ($rootScope.globals.currentUser) {
            crAcl.setRole($rootScope.globals.currentUser.role);
            $state.go('portfolio.intro');
        }
        else {
            crAcl.setRole();
        } }})();
```

现在，我们将设置我们的授权控制器。将以下代码复制并粘贴到 auth.ctrl.js 文件中:

```
(function () {
    'use strict'; angular
        .module('main')
        .controller('AuthCtrl', AuthCtrl); function AuthCtrl(crAcl, $state, AuthService, Flash, $log) {
        var vm = this; vm.login = login;
        vm.register = register; vm.loginForm = null; vm.credentials = {};
        vm.user = {}; function login(credentials) {
            function success(response) {
                function success(response) {
                    if (response.data.status !== 'empty') {
                        var currentUser = response.data.objects[0]; crAcl.setRole('ROLE_USER');
                        AuthService.setCredentials({
                            slug: currentUser.slug,
                            first_name: currentUser.metadata.first_name,
                            last_name: currentUser.metadata.last_name,
                            email: currentUser.metadata.email,
                            role: 'ROLE_USER'
                        });
                        $state.go('portfolio.intro', {slug: currentUser.slug});
                    }
                    else
                        Flash.create('danger', 'Incorrect username or password');
                } function failed(response) {
                    $log.error(response);
                } if (response.data.status !== 'empty')
                    AuthService
                        .checkPassword(credentials)
                        .then(success, failed);
                else
                    Flash.create('danger', 'Incorrect username or password'); $log.info(response);
            } function failed(response) {
                $log.error(response);
            } if (vm.loginForm.$valid)
                AuthService
                    .checkUsername(credentials)
                    .then(success, failed);
        } function register(credentials) {
            function success(response) {
                login(credentials); $log.info(response);
            } function failed(response) {
                $log.error(response);
            } if (vm.loginForm.$valid)
                AuthService
                    .register(credentials)
                    .then(success, failed);
        } }
})();
```

创建授权服务，复制并粘贴以下代码:

```
(function () {
    'use strict'; angular
        .module('main')
        .service('AuthService', function ($http,
                                          $cookieStore,
                                          $q,
                                          $rootScope,
                                          URL, BUCKET_SLUG, READ_KEY, WRITE_KEY) {
            var authService = this;
            $http.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest'; authService.checkUsername = function (credentials) {
                return $http.get(URL + BUCKET_SLUG + '/object-type/users/search', {
                    params: {
                        metafield_key: 'email',
                        metafield_value_has: credentials.email,
                        limit: 1,
                        read_key: READ_KEY
                    }
                });
            };
            authService.checkPassword = function (credentials) {
                return $http.get(URL + BUCKET_SLUG + '/object-type/users/search', {
                    ignoreLoadingBar: true,
                    params: {
                        metafield_key: 'password',
                        metafield_value: credentials.password,
                        limit: 1,
                        read_key: READ_KEY
                    }
                });
            };
            this.register = function (user) {
                return $http.post(URL + BUCKET_SLUG + '/add-object', {
                        title: user.first_name + ' ' + user.last_name,
                        type_slug: 'users',
                        slug: user.username,
                        metafields: [
                            {
                                key: "first_name",
                                type: "text",
                                value: user.first_name
                            },
                            {
                                key: "last_name",
                                type: "text",
                                value: user.last_name
                            },
                            {
                                key: "email",
                                type: "text",
                                value: user.email
                            },
                            {
                                key: "password",
                                type: "text",
                                value: user.password
                            },
                            {
                                key: "intro",
                                type: "html-textarea",
                                value: null
                            },
                            {
                                key: "about",
                                type: "html-textarea",
                                value: null
                            },
                            {
                                key: "contact",
                                type: "html-textarea",
                                value: null
                            },
                            {
                                key: "projects",
                                type: "objects",
                                objects: [],
                                object_type: "projects",
                                value: null
                            }
                        ], write_key: WRITE_KEY
                    }, { ignoreLoadingBar: false });
            };
            authService.setCredentials = function (user) {
                $rootScope.globals = {
                    currentUser: user
                }; $cookieStore.put('globals', $rootScope.globals);
            };
            authService.clearCredentials = function () {
                var deferred = $q.defer();
                $cookieStore.remove('globals'); if (!$cookieStore.get('globals')) {
                    $rootScope.globals = {};
                    deferred.resolve('Credentials clear success');
                } else {
                    deferred.reject('Can\'t clear credentials');
                } return deferred.promise;
            };
        });
})();
```

# 这是怎么回事:

1.  我们使用 ui 路由器来配置路由。
2.  我们为对我们的 Cosmic JS API 的异步调用创建了 Auth Service。
3.  我们创建了 Auth Controller 来检查凭证和注册用户。

为获取和更新用户创建用户服务，复制并粘贴以下代码:

```
(function () {
    'use strict'; angular
        .module('main')
        .service('UserService', function ($http,
                                          $cookieStore,
                                          $q,
                                          $rootScope,
                                          URL, BUCKET_SLUG, READ_KEY, WRITE_KEY) {
            $http.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest'; this.getCurrentUser = function (ignoreLoadingBar) {
                return $http.get(URL + BUCKET_SLUG + '/object/' + $rootScope.globals.currentUser.slug, {
                    ignoreLoadingBar: ignoreLoadingBar,
                    params: {
                        read_key: READ_KEY
                    }
                });
            };
            this.getUser = function (slug, ignoreLoadingBar) {
                return $http.get(URL + BUCKET_SLUG + '/object/' + slug, {
                    ignoreLoadingBar: ignoreLoadingBar,
                    params: {
                        read_key: READ_KEY
                    }
                });
            };
            this.updateUser = function (user) {
                user.write_key = WRITE_KEY; return $http.put(URL + BUCKET_SLUG + '/edit-object', user, {
                    ignoreLoadingBar: false
                });
            }; });
})();
```

为获取和更新创建组合服务，复制并粘贴以下代码:

```
(function () {
    'use strict'; angular
        .module('main')
        .service('PortfolioService', function ($http,
                                          $cookieStore,
                                          $q,
                                          $rootScope,
                                          URL, BUCKET_SLUG, READ_KEY, WRITE_KEY, MEDIA_URL) { $http.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest'; this.getPortfolioBySlug = function (slug) {
                return $http.get(URL + BUCKET_SLUG + '/object/' + slug, {
                    params: {
                        read_key: READ_KEY
                    }
                });
            }; this.updatePortfolio = function (portfolio) {
                portfolio.write_key = WRITE_KEY; return $http.put(URL + BUCKET_SLUG + '/edit-object', portfolio);
            }; this.upload = function (file) {
                var fd = new FormData();
                fd.append('media', file);
                fd.append('write_key', WRITE_KEY); var defer = $q.defer(); var xhttp = new XMLHttpRequest(); xhttp.upload.addEventListener("progress",function (e) {
                    defer.notify(parseInt(e.loaded * 100 / e.total));
                });
                xhttp.upload.addEventListener("error",function (e) {
                    defer.reject(e);
                }); xhttp.onreadystatechange = function() {
                    if (xhttp.readyState === 4) {
                        defer.resolve(JSON.parse(xhttp.response)); //Outputs a DOMString by default
                    }
                }; xhttp.open("post", MEDIA_URL, true); xhttp.send(fd); return defer.promise;
            }
        });
})();
```

创建投资组合控制器，用于更新和添加、编辑项目，复制并粘贴以下代码:

```
(function () {
    'use strict'; angular
        .module('main')
        .controller('PortfolioCtrl', PortfolioCtrl); function PortfolioCtrl($rootScope, $stateParams, $sce, $scope, $state, ngDialog, AuthService, UserService, PortfolioService, $log) {
        var vm = this; getPortfolio(); vm.currentUser = $rootScope.globals.currentUser ? $rootScope.globals.currentUser : getUser(); vm.logout = logout;
        vm.updatePortfolio = updatePortfolio;
        vm.openAddProjectDialog = openAddProjectDialog;
        vm.openEditProjectDialog = openEditProjectDialog; vm.portfolio = {}; vm.toolbarEditor = [
            ['h1', 'h2', 'h3', 'h4', 'h5', 'h6', 'p', 'bold', 'italics', 'underline', 'justifyLeft', 'justifyCenter', 'justifyRight']
        ]; function chunk(arr, size) {
            var newArr = [];
            for (var i = 0; i < arr.length; i += size) {
                newArr.push(arr.slice(i, i + size));
            }
            return newArr;
        } function getUser() {
            function success(response) {
                var currentUser = response.data.object; vm.currentUser = {
                    slug: currentUser.slug,
                    first_name: currentUser.metadata.first_name,
                    last_name: currentUser.metadata.last_name,
                    email: currentUser.metadata.email
                };
            } function failed(response) {
                $log.error(response);
            } UserService
                .getUser($stateParams.slug, true)
                .then(success, failed);
        } function getPortfolio() {
            function success(response) {
                vm.portfolio = response.data.object; if (Array.isArray(vm.portfolio.metadata.projects))
                    vm.projectsChunk = chunk(vm.portfolio.metadata.projects, 2);
                else
                    vm.projectsChunk = []; vm.contact = $sce.trustAsResourceUrl('mailto:' + vm.currentUser.email); $log.info(response);
            } function failed(response) {
                $log.error(response);
            } PortfolioService
                .getPortfolioBySlug($stateParams.slug)
                .then(success, failed);
        } function updatePortfolio() {
            function success(response) {
                getPortfolio(); $log.info(response);
            } function failed(response) {
                $log.error(response);
            } PortfolioService
                .updatePortfolio(vm.portfolio)
                .then(success, failed);
        } function openAddProjectDialog(slug) { var options = {
                templateUrl: '../views/portfolio/portfolio.project.edit.html',
                showClose: true,
                controller: 'PortfolioProjectsAddCtrl as vm'
            }; ngDialog.open(options).closePromise.finally(function () {
                getPortfolio();
            });
        } function openEditProjectDialog(slug) { var options = {
                templateUrl: '../views/portfolio/portfolio.project.edit.html',
                showClose: true,
                controller: 'PortfolioProjectsEditCtrl as vm',
                data: {
                    slug: slug
                }
            }; ngDialog.open(options).closePromise.finally(function () {
                getPortfolio();
            });
        } function logout() {
            function success(response) {
                $state.go('auth'); $log.info(response);
            } function failed(response) {
                $log.error(response);
            } AuthService
                .clearCredentials()
                .then(success, failed);
        } $scope.state = $state; }
})();
```

创建投资组合模块，复制并粘贴下面的代码:

```
(function () {
    'use strict'; angular
        .module('portfolio', [
            'portfolio.intro',
            'portfolio.projects',
            'portfolio.about',
            'portfolio.contact',
            'portfolio.settings'
        ])
        .config(config); config.$inject = ['$stateProvider'];
    function config($stateProvider) { $stateProvider
            .state('portfolio', {
                url: '/:slug/',
                abstract: true,
                templateUrl: '../views/portfolio/portfolio.html',
                controller: 'PortfolioCtrl as vm',
                data: {
                    is_granted: ['ROLE_USER', 'ROLE_GUEST']
                }
            });
    }
})();
```

# 这是怎么回事:

1.  我们为获取和更新创建了组合服务。
2.  我们为更新和添加，编辑项目创建了投资组合控制器。
3.  我们创建了投资组合模块。

创建用于获取作者信息的作者控制器，复制并粘贴以下代码:

# 结论:

我们能够使用我们的动作和调度程序函数来消费 Cosmic JS API。我希望你和我一样喜欢这个教程，如果你有任何问题[在 Twitter 上联系我们](https://twitter.com/cosmic_js)和[加入我们的 Slack 社区](https://cosmicjs.com/community)。