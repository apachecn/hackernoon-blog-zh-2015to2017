# 如何使用 AngularJS 构建表情游戏

> 原文：<https://medium.com/hackernoon/how-to-build-an-emoji-game-using-angularjs-3fdf0de64cb5>

![](img/4233ab2ce7cc4e81d8334b214c9f1771.png)

在本教程中，我将向你展示如何使用一点 Node 和 [Cosmic JS](https://cosmicjs.com) 创建一个“表情符号”游戏。为了理解如何使用 Restful API，本教程将展示如何向[宇宙 JS API](https://cosmicjs.com) 发出简单的 AJAX 请求，以便检索、更新和删除宇宙 JS 桶中的数据。让我们开始吧。

# TL；速度三角形定位法(dead reckoning)

[下载 GitHub 回购。](https://github.com/cosmicjs/angular-emoji-game)
[查看演示。](https://cosmicjs.com/apps/angular-emoji-game)

# 开始使用:

首先，让我们创建一个新目录来构建我们的项目，并创建一个 package.json 文件。

```
mkdir emoji-appemoji-app$ touch package.json
```

现在，在您的 package.json 中，复制并粘贴以下代码:

```
//emoji-app/package.json
{
  "name": "emoji-app",
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
}/
```

其次，我们来做一个 bower.json 文件。

```
emoji-app$ touch bower.json
```

现在，在您的 bower.json 中，复制并粘贴以下代码:

```
//emoji-app/bower.json
{
  "name": "emoji-app",
  "description": "Emoji App",
  "version": "0.0.0",
  "homepage": "https://github.com/kutsaniuk/emoji-app",
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
    "angular-emoji-popup": "",
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
emoji-app$ touch app-server.js//events-app/app-server.js
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
  console.log('Emoji App listening on ' + app.get('port'))
})
```

# 我们正在安装什么，为什么:

1.  我们将使用 AngularJS 框架来构建单页面应用程序
2.  我们正在为创建多视图安装 angular-ui-router。
3.  我们将使用 gulp 把所有的 js 和 css 文件构建成一个文件。

# 构建我们的应用程序:

现在我们将构建我们的文件结构，这样我们就可以组织我们的 angular 模块和 js 文件。我们的表情符号应用程序目录应该是这样的:

```
emoji-app
|----app
|       |----auth
|                 |----auth.ctrl.js
|                 |----auth.service.js
|       |----config
|                 |----config.js
|       |----watch
|                 |----profile
|                           |----watch.profile.ctrl.js
|                           |----watch.profile.mdl.js
|                 |----watch.ctrl.js
|                 |----watch.mdl.js
|                 |----watch.service.js
|       |----admin
|                 |----authors
|                           |----add
|                                   |----admin.authors.add.ctrl.js
|                                   |----admin.authors.add.mdl.js
|                           |----edit
|                                   |----admin.authors.edit.ctrl.js
|                                   |----admin.authors.edit.mdl.js
|                           |----admin.authors.ctrl.js
|                           |----admin.authors.mdl.js
|                           |----admin.authors.service.js
|                 |----quotes
|                           |----add
|                                   |----admin.quotes.add.ctrl.js
|                                   |----admin.quotes.add.mdl.js
|                           |----edit
|                                   |----admin.quotes.edit.ctrl.js
|                                   |----admin.quotes.edit.mdl.js
|                           |----admin.quotes.ctrl.js
|                           |----admin.quotes.mdl.js
|                           |----admin.quotes.service.js
|                 |----admin.ctrl.js
|                 |----admin.mdl.js
|       |----author
|                 |----author.ctrl.js
|                 |----author.mdl.js
|       |----emoji
|                 |----emoji.ctrl.js
|                 |----emoji.mdl.js
|                 |----emoji.service.js
|       |----user
|                 |----user.service.js
|       |----main.mdl.js
|----dist
|       |----css
|       |----img
|       |----js
|----css
|----views
|----resources
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
    <meta name="author" content=""> <title>Emoji App</title> <!-- bower:css -->
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
//emoji-app/gulpfile.js
'use strict';var gulp = require('gulp'),
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
            'ngEmoticons',
            'ngSanitize',
            'ngTouch', 'emoji',
            'admin',
            'author', 'config'
        ])
        .config(config)
        .run(run); config.$inject = ['$stateProvider', '$urlRouterProvider', 'cfpLoadingBarProvider', 'NotificationProvider'];
    function config($stateProvider, $urlRouterProvider, cfpLoadingBarProvider, NotificationProvider) {
        cfpLoadingBarProvider.includeSpinner = false; NotificationProvider.setOptions({
            startTop: 25,
            startRight: 25,
            verticalSpacing: 20,
            horizontalSpacing: 20,
            positionX: 'right',
            positionY: 'bottom'
        }); $urlRouterProvider.otherwise(function ($injector) {
            var $state = $injector.get("$state");
            var $location = $injector.get("$location");
            var crAcl = $injector.get("crAcl"); var state = ""; switch (crAcl.getRole()) {
                case 'ROLE_ADMIN':
                    state = 'admin.authors';
                    break;
                default : state = 'main.emoji';
            } if (state) $state.go(state);
            else $location.path('/');
        }); $stateProvider
            .state('main', {
                url: '/',
                abstract: true,
                templateUrl: '../views/main.html',
                // controller: 'CartCtrl as cart',
                data: {
                    is_granted: ['ROLE_GUEST']
                }
            })
            .state('blog', {
                url: '/blog',
                templateUrl: '../blog.html'
            })
            .state('auth', {
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
            });
    } run.$inject = ['$rootScope', '$cookieStore', '$state', 'crAcl'];
    function run($rootScope, $cookieStore, $state, crAcl) {
        // keep user logged in after page refresh
        $rootScope.globals = $cookieStore.get('globals') || {}; crAcl
            .setInheritanceRoles({
                'ROLE_ADMIN': ['ROLE_ADMIN', 'ROLE_GUEST'],
                'ROLE_GUEST': ['ROLE_GUEST']
            }); crAcl
            .setRedirect('main.emoji'); if ($rootScope.globals.currentUser) {
            crAcl.setRole($rootScope.globals.currentUser.metadata.role);
            // $state.go('admin.watches');
        }
        else {
            crAcl.setRole();
        } }
    angular.module('main')
        .directive('compile', ['$compile', function ($compile) {
            return function (scope, element, attrs) {
                scope.$watch(
                    function (scope) {
                        // watch the 'compile' expression for changes
                        return scope.$eval(attrs.compile);
                    },
                    function (value) {
                        element.html(value); $compile(element.contents())(scope);
                    }
                );
            };
        }]);
})();
```

现在，我们将设置我们的授权控制器。将以下代码复制并粘贴到 auth.ctrl.js 文件中:

```
(function () {
    'use strict'; angular
        .module('main')
        .controller('AuthCtrl', AuthCtrl); function AuthCtrl(crAcl, $state, AuthService, Flash, $log) {
        var vm = this; vm.login = login; vm.loginForm = null; vm.credentials = {};
        vm.user = {}; function login(credentials) {
            function success(response) {
                function success(response) {
                    if (response.data.status !== 'empty') {
                        var currentUser = response.data.objects[0]; crAcl.setRole(currentUser.metadata.role);
                        AuthService.setCredentials(currentUser);
                        $state.go('admin.authors');
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
3.  我们创建了身份验证控制器来检查凭证。

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

为从单词中获取表情符号创建表情符号服务，复制并粘贴以下代码:

```
(function () {
    'use strict'; angular
        .module('main')
        .service('EmojiService', function ($http) { var that = this; that.Quote = function (text, author, slug) {
                this.text = text;
                this.author = author;
                this.slug = slug;
            }; that.Emoji = function (code, position) {
                this.code = code;
                this.position = position;
            }; that.Container = function (type, item) {
                this.type = type;
                this.item = item;
            }; that.Random = {
                randNumOld: 0,
                getRandomInt: function (min, max) {
                    var randNum = Math.floor(Math.random() * (max - min + 1)) + min;
                    if (randNum == this.randNumOld) return this.getRandomInt(min, max);
                    this.randNumOld = randNum; return randNum;
                }
            }; this.getEmojisFromWords = function (quote) {
                var words = [];
                var word = ''; quote += " "; for (var i = 0; i < quote.length; i++) {
                    if (quote[i] !== ' ') word += quote[i];
                    else {
                        words.push(word);
                        word = '';
                    }
                } for (var j = 0; j < words.length; j++) {
                    if (getMeAnEmoji(words[j]) !== "") words[j] = ":" + getMeAnEmoji(words[j]) + ":";
                } console.log(words);
                return words;
            }; this.getEmoji = function (quote, emojis, containers, checkAnswers) {
                var i = 0;
                var position = 0;
                var result;
                var word = '';
                var isEmoji = new RegExp(":");
                var _quote = quote; while (i < quote.text.length) {
                    if (isEmoji.test(quote.text[i])) {
                        result = ( /\:(\w+)\:/.exec(quote.text) )[1];
                        emojis.push(new that.Emoji(":" + result + ":", i));
                        containers.push(new that.Container('emoji', []));
                        checkAnswers.push(new that.Emoji(":" + result + ":", position)); quote.text[i] = quote.text[i].replace(':' + result + ':', '_emoji_'); position++;
                    }
                    else {
                        containers.push(new that.Container('word', quote.text[i]));
                    }
                    word = '';
                    i++;
                } $http.get('/resources/emojis.json')
                    .success(function (data) {
                        for (var j = 0; j < 3; j++) {
                            emojis.push(new that.Emoji(":" + data[parseInt(Math.random() * data.length)] + ":"));
                        }
                    }); return _quote;
            }; });
})();
```

为获取报价和检查结果创建表情符号控制器，复制并粘贴以下代码:

```
(function () {
    'use strict'; angular
        .module('main')
        .controller('EmojiCtrl', EmojiCtrl); function EmojiCtrl($scope, DEFAULT_IMAGE, EmojiService, AdminQuotesService, $log) {
        var vm = this; vm.checkAnswers = [];
        vm.quote = {};
        vm.quotes = [];
        vm.emojis = [];
        vm.containers = [];
        vm.win = false; vm.DEFAULT_IMAGE = DEFAULT_IMAGE; var random = EmojiService.Random;
        var getEmojisFromWords = EmojiService.getEmojisFromWords;
        var getEmoji = EmojiService.getEmoji;
        var Quote = EmojiService.Quote;
        var Emoji = EmojiService.Emoji; var getRandomQuote = _getRandomQuote;
        var getUserAnswers = _getUserAnswers; vm.getUserAnswerLength = getUserAnswerLength;
        vm.nextQuote = nextQuote;
        vm.checkAnswer = checkAnswer;
        vm.getUserAnswerLength = getUserAnswerLength; $scope.getOptionsContainer = getOptionsContainer; function _getRandomQuote(quotes) {
            var _random = random.getRandomInt(0, quotes.length - 1);
            console.log(quotes[_random]);
            return quotes[_random].slug;
        } function _getUserAnswers(containers) {
            var userAnswers = [];
            var position = 0; for (var i = 0; i < containers.length; i++) { if (!Array.isArray(containers[i].item)) continue;
                userAnswers.push(new Emoji(containers[i].item[0].code, position));
                position++; } return userAnswers;
        } function getUserAnswerLength() {
            var userAnswersLength = 0; for (var k = 0; k < vm.containers.length; k++) {
                if (!Array.isArray(vm.containers[k].item)) continue;
                if (vm.containers[k].item.length !== 0) userAnswersLength += 1;
            }
            return userAnswersLength;
        } function nextQuote() {
            getQuote(getRandomQuote(vm.quotes));
        } function checkAnswer() {
            var result = [];
            var position = 0; function in_array(value, array) {
                for (var i = 0; i < array.length; i++) {
                    if (array[i] == value) return true;
                }
                return false;
            } if (vm.getUserAnswerLength() === vm.checkAnswers.length) {
                var userAnswers = getUserAnswers(vm.containers);
                for (var j = 0; j < userAnswers.length; j++) {
                    vm.checkAnswers[j].code === userAnswers[j].code ? result.push(true) : result.push(false);
                } console.log(result); for (var i = 0; i < vm.containers.length; i++) { if (!Array.isArray(vm.containers[i].item)) continue;
                    if (!result[position]) {
                        vm.emojis[vm.containers[i].item[0].jqyoui_pos] = new Emoji(vm.containers[i].item[0].code, i);
                        vm.containers[i].item.splice(0, 1);
                    }
                    position++; } console.log(vm.emojis);
                !in_array(false, result) ? vm.win = true : vm.win = false;
            }
        } function getOptionsContainer(container) {
            return {
                accept: function (dragEl) {
                    if (container.length >= 1) {
                        return false;
                    } else {
                        return true;
                    }
                }
            }
        } function getQuotes() {
            function success(response) {
                response.data.objects.forEach(function (quote) {
                    vm.quotes.push(
                        new Quote(
                            getEmojisFromWords(quote.metadata.text),
                            quote.metadata.author,
                            quote.slug
                        )
                    );
                });
                $log.info(vm.quotes); getQuote(getRandomQuote(vm.quotes)); } function failed(response) {
                $log.error(response);
            } AdminQuotesService
                .getQuotes(true)
                .then(success, failed);
        } function getQuote(slug) {
            function success(response) {
                vm.quote = new Quote(
                    getEmojisFromWords(response.data.object.metadata.text),
                    response.data.object.metadata.author,
                    response.data.object.slug
                ); vm.emojis = [];
                vm.containers = [];
                vm.checkAnswers = [];
                vm.win = false; getEmoji(
                    vm.quote,
                    vm.emojis,
                    vm.containers,
                    vm.checkAnswers
                ); vm.emojis.sort(function (a, b) {
                    return a.code.charCodeAt(1) - b.code.charCodeAt(2)
                }); vm.emojis.reverse();
                console.log('emojis', vm.emojis);
            } function failed(response) {
                $log.error(response);
            } AdminQuotesService
                .getQuoteBySlug(slug)
                .then(success, failed);
        } getQuotes(); $('.emoji').draggable(); // FOR TouchScreen
    }
})();
```

创建表情模块，复制并粘贴下面的代码:

```
(function () {
    'use strict'; angular
        .module('emoji', [])
        .config(config); config.$inject = ['$stateProvider', '$urlRouterProvider'];
    function config($stateProvider, $urlRouterProvider) { $stateProvider
            .state('main.emoji', {
                url: '',
                templateUrl: '../views/emoji/emoji.html',
                controller: 'EmojiCtrl as emojiCtrl'
            });
    }
})();
```

# 这是怎么回事:

1.  我们为从单词中获取表情符号创建了表情符号服务。
2.  我们为拖放表情符号、获取报价和检查结果创建了表情符号控制器。
3.  我们创建了表情模块。

创建用于获取作者信息的作者控制器，复制并粘贴以下代码:

```
(function () {
    'use strict'; angular
        .module('main')
        .controller('AuthorCtrl', AuthorCtrl); function AuthorCtrl($stateParams, $scope, DEFAULT_IMAGE, AdminAuthorsService, Flash, $log) {
        var vm = this; vm.author = {}; vm.DEFAULT_IMAGE = DEFAULT_IMAGE; getAuthor($stateParams.slug); function getAuthor(slug) {
            function success(response) {
                response.data.object.metadata.born = new Date(response.data.object.metafields[2].value);
                response.data.object.metadata.died = new Date(response.data.object.metafields[3].value);
                vm.author = response.data.object;
            } function failed(response) {
                $log.error(response);
            } AdminAuthorsService
                .getAuthorBySlug(slug)
                .then(success, failed);
        }
    }
})();
```

创建作者模块，复制并粘贴下面的代码:

```
(function () {
    'use strict'; angular
        .module('author', [])
        .config(config); config.$inject = ['$stateProvider', '$urlRouterProvider'];
    function config($stateProvider, $urlRouterProvider) { $stateProvider
            .state('main.author', {
                url: 'author/:slug',
                templateUrl: '../views/author/author.html',
                controller: 'AuthorCtrl as vm',
                data: {
                    is_granted: ['ROLE_GUEST']
                }
            });
    }
})();
```

# 这是怎么回事:

1.  我们可以看到关于作者的信息。

# 结论:

我们能够使用我们的动作和调度程序函数来消费 Cosmic JS API。我希望你和我一样喜欢这个教程，如果你有任何问题[在 Twitter 上联系我们](https://twitter.com/cosmic_js)和[加入我们的 Slack 社区](https://cosmicjs.com/community)。