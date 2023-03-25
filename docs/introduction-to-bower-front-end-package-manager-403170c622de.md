# Bower(前端包管理器)简介

> 原文：<https://medium.com/hackernoon/introduction-to-bower-front-end-package-manager-403170c622de>

如果你正在创建一个网站，你可能需要不同的框架来执行特定的任务，例如 [Bootstrap](https://hackernoon.com/tagged/bootstrap) 来使你的网站响应，jQuery 来使你的网站交互&等等。

要管理这些东西，你可以使用 bower。Bower 让你下载这个框架，更新这个框架，而不用去特定的框架网站。

Bower 是 Twitter 建立的。

Bower 是一个节点模块&要安装 bower，您需要有 npm & node。

> 我已经创建了一个相同的视频跟随下面的链接观看
> [鲍尔初学者教程](https://www.youtube.com/watch?v=QaCnPr-ECuQ)

*要安装 bower，请在您的终端中运行此命令*

```
npm install -g bower
```

这将在您的系统上全局安装 bower &您可以在您的[系统](https://hackernoon.com/tagged/system)的任何地方使用 bower 命令。

## 如何使用 bower 安装软件包

*用 bower 安装包超级简单。每次你想安装新的软件包时运行这个命令。*

```
bower install package-name
```

*假设我想在项目中安装 bootstrap。我必须运行这个命令*

```
bower install bootstrap
```

& Bower 将为我做所有艰苦的工作。是不是很酷:)

*不仅如此，您还可以使用此命令安装特定版本*

```
bower install bootstrap#4.0
```

## 创建 bower.json 文件

在做任何事情之前，我建议创建一个 bower json 文件，它将跟踪你安装的依赖项。

*要创建 bower.json 文件，运行此命令*

```
bower init
```

你可以回答它问的任何问题，或者直接按回车键。

现在，如果您转到项目目录，您会看到一个 bower.json 文件。

*当你安装一些依赖项时，你可以使用这个命令*

```
bower install bootstrap --save
```

*如果使用— save bower 将跟踪依赖关系。*

## 更新包

*如果你想更新我们所有的软件包，这很简单，只要运行这个命令*

```
bower update
```

## 如何卸载依赖关系？

*运行该命令*

```
bower uninstall bootstrap --save
```

## 利益

如果你在一个团队中工作，你不必给出 bower 生成的完整的组件文件夹，只需提供 bower.json 文件&告诉你的同事运行 bower install。它将自动安装 bower json 文件中列出的所有依赖项。

如果你想知道更多，你可以运行这个命令

```
bower -h
```

*如果你想查看你已经安装的依赖列表，使用这个命令*

```
bower list
```

*使用这个命令*知道你必须在脚本或链接标签中给出的路径

```
bower list --path
```

> 注意:您需要将 bower 生成的 bower_components 文件夹包含在 to 中。gitignore(当您生成 bower json 时，它会要求将 bower_components 包含到。gitignore 简单说是)。这将告诉 git 忽略 bower_component 文件。其他开发者只需要 bower json 文件。他们只需运行 bower install 将所有依赖项安装到他们的系统中。