# 如何自动将 npm 软件包发布到注册表

> 原文：<https://medium.com/hackernoon/how-to-automate-publishing-npm-packages-to-the-registry-8ba03d72d65d>

![](img/3477cffccb55e86f228bd66cf519c6ab.png)

**这篇文章将向你展示如何正确地配置 npm 模块，并使用 Buddy 来自动测试和发布你的包**

# 本指南的目标

本指南将向您展示如何:

*   创建 npm 包
*   [在注册表中发布](https://hackernoon.com/tagged/publish)包
*   [多亏了 Buddy，自动测试和发布 npm 包](https://hackernoon.com/tagged/automate)

# 要求

你需要做的第一件事是安装以下东西:

*   [**GIT**](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
*   [**节点和 npm**](https://docs.npmjs.com/getting-started/installing-node)

# 创建 npm 包

首先创建一个文件夹并初始化 Git 存储库，这样您的包在版本控制下仍然是安全的:

```
mkdir my_first_npm_module
cd my_first_npm_module
git init
```

接下来，添加`package.json`，它将包含您的模块的细节。您可以通过运行以下命令轻松实现这一点:

```
npm init
```

您将被要求提供关于您的包裹的信息:

*   **名字**
*   **版本**
*   **主值**(通常设置为`index.js`)

一旦完成，检查`package.json`的内容。它应该是这样的:

```
{
 "name": "buddy-demo-package",
 "version": "1.0.3",
 "description": "", "main": "index.js",
 "scripts": {
  "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

在`index.js`中，函数应该作为`exports`对象的属性提供。这里有一个例子:

```
exports.printMsg = function() {    
    console.log("My first package message"); 
}
```

一旦一切就绪，请确保将您的文件提交给 repo…

# 喜欢你读的吗？[此处跟随全文](https://buddy.works/guides/how-automate-npm-publishing?utm_source=medium&utm_medium=post&utm_campaign=how-to-automate-publishing-npm-packages-to-the-registry&utm_content=link)。

![](img/237fecb458d889482966108f09dae68f.png)[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客们下午的开始。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)