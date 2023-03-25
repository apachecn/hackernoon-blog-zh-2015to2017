# 如何构建 AngularJS 事件应用程序

> 原文：<https://medium.com/hackernoon/how-to-build-an-angularjs-events-app-8b2d7e87052d>

![](img/048645f5b36bc54bae4e99960eeca8af.png)

在本教程中，我将向你展示如何使用一点 Node、Angular JS 和 [Cosmic JS](https://cosmicjs.com) 创建一个“事件”应用程序。为了理解如何使用 Restful APIs，本教程将展示如何向 Cosmic JS API 发出简单的 AJAX 请求，以便检索、更新和删除我们的 Cosmic JS Bucket 中的数据。让我们开始吧。

# TL；速度三角形定位法(dead reckoning)

[下载 GitHub 回购。](https://github.com/cosmicjs/events-app)
[查看试玩。](https://cosmicjs.com/apps/events-app)

# 开始使用:

首先，让我们创建一个新目录来构建我们的项目，并创建一个 package.json 文件。

```
mkdir events-appevents-app$ touch package.json
```

现在，在您的 package.json 中，复制并粘贴以下代码:

```
//events-app/package.json
{
  "name": "events-app",
  "version": "1.0.0",
  "main": "app-server.js",
  "engines": {
    "node": "4.1.2",
    "npm": "3.5.2"
  },
  "description": "",
  "dependencies": {
    "bower": "^1.7.7",
    "http-server": "^0.9.0",
    "gulp": "^3.9.1",
    "gulp-autoprefixer": "^3.1.0",
    "gulp-concat": "^2.6.0",
    "gulp-concat-css": "^2.2.0",
    "gulp-minify-css": "^1.2.4",
    "gulp-webserver": "^0.9.1",
    "wiredep": "^3.0.0",
    "express": "^4.13.3"
  },
  "scripts": {
    "postinstall": "bower install",
    "start": "npm run production",
    "production": "node app-server.js"
  },
  "author": "",
  "license": "ISC"
}
```

其次，我们来做一个 bower.json 文件。

```
events-app$ touch bower.json
```

现在，在您的 bower.json 中，复制并粘贴以下代码:

```
//events-app/bower.json
{
  "name": "events-app",
  "description": "Events App",
  "version": "0.0.0",
  "homepage": "https://github.com/cosmicjs/events-app",
  "license": "MIT",
  "private": true,
  "dependencies": {
    "angular": "~1.4.x",
    "angular-mocks": "~1.4.x",
    "angular-bootstrap": "~1.1.x",
    "angular-cookies": "~1.4.x",
    "angular-route": "~1.4.x",
    "angular-ui-router": "0.2.x",
    "angular-resource": "1.4.x",
    "angular-animate": "~1.4.x",
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
    "angular-nvd3": "*",
    "infinity-angular-chosen": "^0.2.0",
    "angular-flash-alert": "^2.4.0",
    "components-font-awesome": "^4.7.0",
    "textAngular": "^1.5.16",
    "angular-loading-bar": "^0.9.0"
  },
  "resolutions": {
    "angular": "~1.4.x"
  },
  "devDependencies": {
    "cr-acl": "^0.5.0"
  }
}
```

配置应用服务器:

```
events-app$ touch app-server.js//events-app/app-server.js
var express = require('express')
var app = express()
app.set('port', process.env.PORT || 3000)
app.use(express.static(__dirname))
var http = require('http').Server(app)
// Route
app.get('/', (req, res) => {
  res.sendFile(__dirname + '/index.html')
})
http.listen(app.get('port'), () => {
  console.log('Wedding Site listening on ' + app.get('port'))
})
```

# 我们正在安装什么，为什么:

1.  我们将使用 AngularJS 框架构建一个单页面应用程序。
2.  我们正在为创建多视图安装 angular-ui-router。
3.  我们将使用 gulp 把所有的 js 和 css 文件构建成一个文件。

# 构建我们的应用程序:

现在我们将构建我们的文件结构，这样我们就可以组织我们的 angular 模块和 js 文件。我们的 events-app 目录应该是这样的:

```
events-app
|----app
|       |----auth
|                 |----auth.ctrl.js
|                 |----auth.service.js
|       |----config
|                 |----config.js
|       |----event
|                 |----add
|                           |----event.add.ctrl.js
|                           |----event.add.mdl.js
|                 |----feed
|                           |----event.feed.ctrl.js
|                           |----event.feed.mdl.js
|                 |----profile
|                           |----event.profile.ctrl.js
|                           |----event.profile.mdl.js
|                 |----event.ctrl.js
|                 |----event.mdl.js
|                 |----event.service.js
|       |----user
|                 |----profile
|                           |----user.profile.ctrl.js
|                           |----user.profile.mdl.js
|                 |----settings
|                           |----user.settings.ctrl.js
|                           |----user.settings.mdl.js
|                 |----user.ctrl.js
|                 |----user.mdl.js
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
    <meta name="author" content=""> <title>Events App</title> <!-- bower:css -->
    <!-- endbower --> <!-- Bootstrap Core CSS -->
    <link href="bower_components/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet"> <!-- Custom CSS --> <link href="dist/css/main.min.css" rel="stylesheet"> <!--[if lt IE 9]>
    <script src="https://oss.maxcdn.com/libs/html5shiv/3.7.0/html5shiv.js"></script>
    <script src="https://oss.maxcdn.com/libs/respond.js/1.4.2/respond.min.js"></script>
    <![endif]--></head>
<body><div ui-view></div><!-- bower:js -->
<!-- endbower --><script src="dist/js/main.js"></script>
</body>
</html>
```

在这里，我们将把我们的“根”视图作为目标，稍后放置我们的角度模块。位于我们的 dist 目录中的 [main.js](https://cosmicjs.com/blog/main.js) 文件是我们的 [gulpfile.js](https://cosmicjs.com/blog/gulpfile.js) 文件在捆绑了我们所有的 angular 模块之后将会吐出来的。现在，设置我们的 [gulpfile.js](https://cosmicjs.com/blog/gulpfile.js) 文件来捆绑我们所有的 js 文件，并将这个捆绑文件导出到我们的 dist 目录。将以下代码复制到您的 [gulpfile.js](https://cosmicjs.com/blog/gulpfile.js) 文件中:

```
//events-app/gulpfile.js
'use strict';var gulp = require('gulp'),
    webserver = require('gulp-webserver'),
    minifyCSS = require('gulp-minify-css'),
    concatCss = require('gulp-concat-css'),
    concat = require('gulp-concat'),
    wiredep = require('wiredep').stream,
    autoprefixer = require('gulp-autoprefixer');gulp.task('css', function () {
  return gulp.src('css/**/*.css')
    .pipe(minifyCSS())
    .pipe(concat('main.min.css'))
    .pipe(autoprefixer())
    .pipe(gulp.dest('dist/css'));
});gulp.task('js', function() {
  return gulp.src('app/**/**/*.js')
    .pipe(concat('main.js'))
    .pipe(gulp.dest('dist/js/'));
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

之后，我们可以创建 config.js。将以下代码复制并粘贴到您的 app/config/config.js 文件中:

```
(function () {
    'use strict'; var app = angular
                .module('main'); app.constant('BUCKET_SLUG', 'your-bucket-slug');
    app.constant('URL', 'https://api.cosmicjs.com/v1/');
    app.constant('MEDIA_URL', 'https://api.cosmicjs.com/v1/your-bucket-slug/media');
    app.constant('READ_KEY', 'your-read-key');
    app.constant('WRITE_KEY', 'your-write-key');
    app.constant('DEFAULT_EVENT_IMAGE', 'url-image');})();
```

之后，我们可以创建我们的主模块。将以下代码复制并粘贴到 app/main.mdl.js 文件中:

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
            'textAngular',
            'flow',
            'angular-loading-bar', 'event',
            'user'
        ])
        .config(config)
        .run(run); config.$inject = ['$stateProvider', '$urlRouterProvider', 'cfpLoadingBarProvider', 'WRITE_KEY'];
    function config($stateProvider, $urlRouterProvider, cfpLoadingBarProvider, WRITE_KEY) {
        cfpLoadingBarProvider.includeSpinner = false; $urlRouterProvider.otherwise(function ($injector) {
            var $state = $injector.get("$state");
            var $location = $injector.get("$location");
            var crAcl = $injector.get("crAcl"); var state = ""; switch (crAcl.getRole()) {
                case 'ROLE_USER':
                    state = 'main.event.feed';
                    break;
            } if (state) $state.go(state);
            else $location.path('/login');
        }); $stateProvider
            .state('main', {
                url: '/',
                abstract: true,
                templateUrl: '../views/main.html',
                controller: 'UserCtrl as global',
                data: {
                    is_granted: ['ROLE_USER']
                }
            })
            .state('auth', {
                url: '/login',
                templateUrl: '../views/auth/login.html',
                controller: 'AuthCtrl as auth',
                onEnter: ['AuthService', function(AuthService) {
                    AuthService.clearCredentials();
                }],
                data: {
                    is_granted: ['ROLE_GUEST']
                }
            });
    } run.$inject = ['$rootScope', '$cookieStore', '$http', 'crAcl', 'AuthService'];
    function run($rootScope, $cookieStore, $http, crAcl, AuthService) { $rootScope.globals = $cookieStore.get('globals') || {};
        $http.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest'; crAcl
            .setInheritanceRoles({
                'ROLE_SUPER_ADMIN': ['ROLE_SUPER_ADMIN', 'ROLE_GUEST'],
                'ROLE_USER': ['ROLE_USER', 'ROLE_GUEST'],
                'ROLE_GUEST': ['ROLE_GUEST']
            }); crAcl
            .setRedirect('auth'); if ($rootScope.globals.currentUser) { crAcl.setRole($rootScope.globals.currentUser.metadata.role);
        }
        else {
            crAcl.setRole();
        } }})();
```

现在，我们将设置我们的授权控制器。将以下代码复制并粘贴到您的 [auth.ctrl.js](https://cosmicjs.com/blog/auth.ctrl.js) 文件中:

```
(function () {
    'use strict'; angular
        .module('main')
        .controller('AuthCtrl', AuthCtrl); function AuthCtrl(crAcl, $state, AuthService, Flash, $log) {
        var vm = this; vm.login = login;
        vm.register = register; vm.showRegisterForm = false; vm.loginForm = null;
        vm.registerForm = null; vm.credentials = {};
        vm.user = {}; function login(credentials) {
            function success(response) {
                function success(response) {
                    if (response.data.status !== 'empty') {
                        var currentUser = response.data.objects[0]; crAcl.setRole(currentUser.metadata.role);
                        AuthService.setCredentials(currentUser);
                        $state.go('main.event.feed');
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
                $log.info(response); var currentUser = response.data.object.metafields; Flash.create('success', 'You have successfully signed up!');
                vm.credentials = {
                    username: currentUser[0].value,
                    password: currentUser[3].value
                };
                vm.showRegisterForm = false;
            } function failed(response) {
                $log.error(response);
            } if (vm.registerForm.$valid)
                AuthService
                    .register(credentials)
                    .then(success, failed);
        } }
})();
```

现在，我们将在 app/auth/auth.service.js 中创建我们的身份验证服务:

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
                        metafield_key: 'username',
                        metafield_value_has: credentials.username,
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
            authService.register = function (user) { return $http.post(URL + BUCKET_SLUG + '/add-object', {
                    title: user.full_name,
                    type_slug: 'users',
                    slug: user.username,
                    metafields: [
                        {
                            key: "username",
                            type: "text",
                            value: user.username
                        },
                        {
                            key: "email",
                            type: "text",
                            value: user.email
                        },
                        {
                            key: "full_name",
                            type: "text",
                            value: user.full_name
                        },
                        {
                            key: "password",
                            type: "text",
                            value: user.password
                        },
                        {
                            key: "image",
                            type: "file",
                            value: "3b2180f0-2c40-11e7-85ac-e98751218524-1493421969_male.png"
                        },
                        {
                            key: "role",
                            type: "radio-buttons",
                            options: [
                                {
                                    value: "ROLE_USER"
                                },
                                {
                                    value: "ROLE_SUPER_ADMIN"
                                }
                            ],
                            value: "ROLE_USER"
                        }
                    ], write_key: WRITE_KEY
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

创建用户控制器以获取当前用户并注销，复制并粘贴以下代码:

```
(function () {
    'use strict'; angular
        .module('main')
        .controller('UserCtrl', UserCtrl); function UserCtrl($rootScope, $scope, $state, AuthService, Flash, $log) {
        var vm = this; vm.currentUser = $rootScope.globals.currentUser.metadata; vm.logout = logout; function logout() {
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

接下来，我们将创建创建用户模块:

```
(function () {
    'use strict'; angular
        .module('user', [
            'user.profile',
            'user.settings'
        ])
        .config(config); config.$inject = ['$stateProvider', '$urlRouterProvider'];
    function config($stateProvider, $urlRouterProvider) { $stateProvider
            .state('main.user', {
                url: 'user',
                abstract: true,
                data: {
                    is_granted: ['ROLE_USER']
                }
            });
    }
})();
```

# 这是怎么回事:

1.  我们为对我们的 Cosmic JS API 的异步调用创建了用户服务。
2.  我们为获取当前用户和注销创建了用户控制器。

接下来，我们将创建事件服务，从 Cosmic JS API 获取、更新、添加和删除事件:

```
(function () {
    'use strict'; angular
        .module('main')
        .service('EventService', function ($http,
                                          $cookieStore,
                                          $q,
                                          $rootScope,
                                          URL, BUCKET_SLUG, READ_KEY, WRITE_KEY, MEDIA_URL) { $http.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest'; this.getEvents = function () {
                return $http.get(URL + BUCKET_SLUG + '/object-type/events', {
                    params: {
                        limit: 100,
                        read_key: READ_KEY
                    }
                });
            };
            this.getEventsByUsername = function (username, ignoreLoadingBar) {
                return $http.get(URL + BUCKET_SLUG + '/object-type/events/search',
                    {
                        ignoreLoadingBar: ignoreLoadingBar,
                        params: {
                            metafield_key: 'user',
                            metafield_object_slug: username,
                            limit: 10,
                            read_key: READ_KEY
                        }
                    }
                );
            };
            this.getEventById = function (slug) {
                return $http.get(URL + BUCKET_SLUG + '/object/' + slug, {
                    params: {
                        read_key: READ_KEY
                    }
                });
            };
            this.updateEvent = function (event) {
                event.write_key = WRITE_KEY; return $http.put(URL + BUCKET_SLUG + '/edit-object', event);
            };
            this.removeEvent = function (slug) {
                return $http.delete(URL + BUCKET_SLUG + '/' + slug, {
                    ignoreLoadingBar: true,
                    headers:{
                        'Content-Type': 'application/json'
                    },
                    data: {
                        write_key: WRITE_KEY
                    }
                });
            };
            this.createEvent = function (event) {
                event.write_key = WRITE_KEY; var beginDate = new Date(event.metafields[1].value);
                var endDate = new Date(event.metafields[2].value); event.metafields[1].value = beginDate.getFullYear() + '-' + (beginDate.getMonth() + 1) + '-' + beginDate.getDate();
                event.metafields[2].value = endDate.getFullYear() + '-' + (beginDate.getMonth() + 1) + '-' + endDate.getDate(); event.slug = event.title;
                event.type_slug = 'events'; event.metafields[4] = {
                    key: "user",
                    type: "object",
                    object_type: "users",
                    value: $rootScope.globals.currentUser._id
                };
                return $http.post(URL + BUCKET_SLUG + '/add-object', event);
            };
            this.upload = function (file) {
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

我们的事件控制器将获取所有事件并移除事件:

```
(function () {
    'use strict'; angular
        .module('main')
        .controller('EventCtrl', EventCtrl); function EventCtrl(EventService, Notification, $log, $rootScope, DEFAULT_EVENT_IMAGE) {
        var vm = this; vm.getEvents = getEvents;
        vm.removeEvent = removeEvent;
        vm.DEFAULT_EVENT_IMAGE = DEFAULT_EVENT_IMAGE; function getEvents(username) {
            function success(response) {
                $log.info(response); vm.events = response.data.objects;
            } function failed(response) {
                $log.error(response);
            }
            console.log(username); EventService
                .getEventsByUsername(username)
                .then(success, failed);
        } function removeEvent(slug) {
            function success(response) {
                $log.info(response); getEvents($rootScope.globals.currentUser.metadata.username); Notification.success('Deleted');
            } function failed(response) {
                Notification.error(response.data.message); $log.error(response);
            } EventService
                .removeEvent(slug)
                .then(success, failed);
        }
    }
})();
```

我们的事件模块将呈现我们的事件视图:

```
(function () {
    'use strict'; angular
        .module('event', [
            'event.profile',
            'event.feed',
            'event.add'
        ])
        .config(config); config.$inject = ['$stateProvider', '$urlRouterProvider'];
    function config($stateProvider, $urlRouterProvider) { $stateProvider
            .state('main.event', {
                url: 'events',
                views: {
                    '': {
                        templateUrl: '../views/event/events.html',
                        controller: 'EventCtrl as vm'
                    }
                },
                data: {
                    is_granted: ['ROLE_USER']
                }
            });
    }})();
```

# 这是怎么回事:

1.  我们为对我们的 Cosmic JS API 的异步调用创建了事件服务。我们可以创建、更新、删除和获取事件。
2.  我们创建了事件控制器来获取所有事件和移除事件。
3.  我们创建了一个事件模块来呈现我们的视图。

现在让我们为添加事件创建事件添加控制器:

```
(function () {
    'use strict'; angular
        .module('main')
        .controller('EventAddCtrl', EventAddCtrl); function EventAddCtrl(EventService, Notification, $state, $log, $scope, MEDIA_URL, DEFAULT_EVENT_IMAGE, $timeout) {
        var vm = this; vm.createEvent = createEvent;
        vm.cancelUpload = cancelUpload;
        vm.upload = upload; vm.dateBeginPicker = false;
        vm.dateEndPicker = false;
        vm.contentEditor = true;
        vm.uploadProgress = 0; vm.event = {
            title: null,
            slug: null,
            content: null,
            metafields: [
                {
                    key: "image",
                    type: "file",
                    value: null
                },
                {
                    key: "date_begin",
                    type: "date",
                    value: null
                },
                {
                    key: "date_end",
                    type: "date",
                    value: null
                },
                {
                    key: "type",
                    type: "select-dropdown",
                    options: [
                        {
                            key: "social",
                            value: "Social"
                        },
                        {
                            key: "fun",
                            value: "Fun"
                        }
                    ],
                    value: "Social"
                }
            ]
        }; $timeout(function() {
            vm.event.metafields[1].value = new Date();
            vm.event.metafields[2].value = new Date();
        }, 100); vm.flow = {};
        vm.background = {
            'background-image': 'url(' + DEFAULT_EVENT_IMAGE + ')'
        }; vm.flowConfig = {
            target: MEDIA_URL,
            singleFile: true
        }; function createEvent() {
            if (vm.flow.files[0])
                upload();
            else
                _createEvent(vm.event);
        } function _createEvent(event) {
            function success(response) {
                $log.info(response); Notification.success(
                    {
                        message: 'Created',
                        delay: 800,
                        replaceMessage: true
                    }
                ); $state.go('main.event');
            } function failed(response) {
                Notification.error(
                    {
                        message: response.data.error,
                        delay: 4000,
                        replaceMessage: true
                    }
                ); $log.error(response);
            } EventService
                .createEvent(event)
                .then(success, failed);
        } function cancelUpload() {
            vm.flow.cancel();
            vm.background = {
                'background-image': 'url(' + DEFAULT_EVENT_IMAGE.url + ')'
            };
        } $scope.$watch('vm.flow.files[0].file.name', function () {
            if (!vm.flow.files[0]) {
                return ;
            }
            var fileReader = new FileReader();
            fileReader.readAsDataURL(vm.flow.files[0].file);
            fileReader.onload = function (event) {
                $scope.$apply(function () {
                    vm.background = {
                        'background-image': 'url(' + event.target.result + ')'
                    };
                });
            };
        }); function upload() {
            EventService
                .upload(vm.flow.files[0].file)
                .then(function(response){ vm.event.metafields[0].value = response.media.name; createEvent(vm.event); vm.flow.cancel();
                    vm.uploadProgress = 0; }, function(){
                    console.log('failed :(');
                }, function(progress){
                    vm.uploadProgress = progress;
                }); } }
})();
```

接下来，我们创建事件模块:

```
(function () {
    'use strict'; angular
        .module('event.add', [])
        .config(config); config.$inject = ['$stateProvider', '$urlRouterProvider'];
    function config($stateProvider, $urlRouterProvider) { $stateProvider
            .state('main.event.add', {
                url: '/add',
                views: {
                    '@main': {
                        templateUrl: '../views/event/event.profile.html',
                        controller: 'EventAddCtrl as vm'
                    }
                },
                data: {
                    is_granted: ['ROLE_USER']
                }
            });
    }})();
```

# 这是怎么回事:

1.  我们可以添加事件。
2.  我们可以上传图像。

# 结论:

我们能够通过 Cosmic JS API 创建一个非常复杂的应用程序来管理用户、会话、添加/编辑事件。我希望你和我一样喜欢这个教程，如果你有任何问题[在 Twitter 上联系我们](https://twitter.com/cosmic_js)和[加入我们的 Slack 社区](https://cosmicjs.com/community)。

> 这篇文章最初出现在[的宇宙 JS 博客](https://cosmicjs.com/blog/how-to-build-an-angularjs-events-app)上。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)