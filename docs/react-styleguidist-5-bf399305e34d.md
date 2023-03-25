# 反应样式 guidist 5

> 原文：<https://medium.com/hackernoon/react-styleguidist-5-bf399305e34d>

这是 React Styleguidist 最大的更新，有 300 次提交和四个月的工作。它包含了大量的重新思考和重写。但是大部分的改变是为了使初始配置更容易。

🍕非常感谢[尤里·舍甫琴柯](https://github.com/n1313)、 [webpackConfig](https://medium.com/u/5829a67c85f9#webpackconfig) 选项:

```
module.exports = {
  webpackConfig: {
    module: {
      loaders: [
        // Babel loader, will use your project’s .babelrc
        {
          test: /\.jsx?$/,
          exclude: /node_modules/,
          loader: 'babel-loader',
        },
        // Other loaders that is needed for your components
        {
          test: /\.css$/,
          loader: 'style-loader!css-loader?modules',
        },
      ],
    },
  },
};
```

您可以重用现有的 webpack 配置:

```
module.exports = {
  webpackConfig: require('./configs/webpack.js')
};
```

或者 Styleguidist 会尝试在你的项目根目录中找到`webpack.config.js`并使用它。

我们还为您的网络包加载器取消了一个臭名昭著的`include`或`exclude`选项要求。

## 其他变化

还有更多的新选项、小功能和错误修复——见[发布说明](https://github.com/styleguidist/react-styleguidist/releases/tag/v5.0.0)。

## 一些潜在的变化

*   我们已经将我们的测试从 [AVA](https://github.com/avajs/ava) 迁移到 [Jest](https://facebook.github.io/jest/) 并且增加了很多新的测试。
*   我们使用 AST 在 webpack 加载器中生成代码，而不是字符串连接。
*   为了更好的隔离(感谢 jss-isolate)和更容易的主题化，我们使用 [JSS](http://cssinjs.org/) 代替 CSS 模块进行样式化。

[*加入我们的 Gitter chat*](https://gitter.im/styleguidist/styleguidist) *如果您有任何问题或在 Twitter 上关注我*[](https://twitter.com/iamsapegin)**获取最新的 Styleguidist 新闻。**

*[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)**[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)**[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*

*![](img/be0ca55ba73a573dce11effb2ee80d56.png)*