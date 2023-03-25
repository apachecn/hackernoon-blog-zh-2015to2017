# 使用 Travic CI 将 webpack 应用程序持续部署到多个环境中

> 原文：<https://medium.com/hackernoon/continuous-deployment-of-a-webpack-app-to-multiple-environments-using-travic-ci-d2c6f22eac50>

# TL；速度三角形定位法(dead reckoning)

我使用 [GitHub](https://github.com/) 和 [Travis CI](https://travis-ci.org/) 在 [Firebase](https://firebase.google.com/) 上进行 [Jest](https://facebook.github.io/jest/) 测试，设置了一个 [webpack](https://webpack.github.io/) 应用的持续部署。部署到开发环境是在每次提交到主机时完成的。部署到 [PROD](https://hackernoon.com/tagged/prod) 环境是在每个标签上完成的。

点击查看代码[。](https://github.com/bartw/multi_env_webpack_travis_app)

# 如何？

在代码中，我使用了一个默认值:

```
var text = __CONFIG__ || “default”;
```

该值可以在 [webpack](https://hackernoon.com/tagged/webpack) 配置文件中被覆盖:

```
plugins: [
  new webpack.DefinePlugin({ 
    __CONFIG__: JSON.stringify("override")
  })
]
```

在我的。我定义了多个 Firebase 项目:

```
{
  "projects": {
    "prod": "prod-multi-webpack-travis",
    "dev": "dev-multi-webpack-travis"
  }
}
```

这允许我通过执行以下命令为每个项目设置配置项:

```
firebase use dev
firebase login:ci
travis encrypt "1/xxx"
```

在. travis.yml 文件中，我定义了多个部署:

```
deploy:
  - provider: firebase
    project: dev
    token:
      secure: "encrypted stuff"
    skip_cleanup: true
    on:
      branch: "master"
  - provider: firebase
    project: prod
    token:
      secure: "encrypted stuff"
    skip_cleanup: true
    on:
      tags: true
```

现在，我还可以使用 Travis 在我的 webpack 配置文件中定义的环境变量:

```
plugins: [
  new webpack.DefinePlugin({ 
    __CONFIG__: JSON.stringify(
      process.env.TRAVIS_TAG ? "override on tag" : "override"
    )
  })
]
```

如果我推送 GitHub，就会触发开发部署，如果我更新版本，就会触发开发和生产部署:

```
npm version patch
git push --follow-tags
```

为了在使用环境文件的文件上进行 Jest 测试，我在 package.json 文件中添加了一些 Jest 配置:

```
"jest": {
  "globals": {
    "__CONFIG__": null
  }
}
```

# 结论

我花了很长时间才完成这个工作，所以我决定写这篇博文。这种设置使得为每个环境配置不同的配置变得非常容易。

缺点是每次改变环境时都需要重新捆绑代码。