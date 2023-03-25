# 今天带 TypeScript 的 ES6 模块

> 原文：<https://medium.com/hackernoon/es6-modules-today-with-typescript-22969cd360f0>

![](img/d48c1d3e516d7eef14a2ee3baa74d263.png)

Two work horses represent both ESM and CJS builds

2017 年的今天，很多常青树浏览器都支持开箱即用的 ES6 模块。一些浏览器将它隐藏在一个标志后面，包括 Node.js。但是用同一个 npm 包支持新旧环境可能吗？是啊！

> *编者按:ES6 模块有时被称为 ES2015 模块，或 ESM，或* `*module*` *脚本，有时甚至被扩展名* `*.mjs*` *读作“迈克尔杰克逊脚本”。我们都在谈论同一件事，所以如果你听到不同的术语，不要混淆。*

我不会讨论使用 ES6 模块的优点或者为什么 TypeScript 很棒，因为已经有很多博客文章详细描述了这些。相反，我将着重于向 npm 发布一个用 TypeScript 编写但部署为`.mjs` (ESM)和`.js` (CommonJS)的包，这样任何消费者都可以使用您的包！

# 入门指南

第一步是设置您的`tsconfig.json`文件，这样 TypeScript 将使用最新最棒的 JavaScript 特性，如下所示:

```
{
  "compilerOptions": {
    "module": "es2015",
    "target": "ES2017",
    "rootDir": "src",
    "outDir": "dist",
    "sourceMap": false,
    "strict": true
  }
}
```

显然，我们希望模块是`es2015`，因为它在本文的标题中，所以我们必须在某个时候解决它！让我们瞄准`es2017`，这样我们就可以像 JS 忍者一样使用`async`和`await`关键字。您可以将您的`rootDir`和`outDir`命名为您想要的任何名称，但是在 JS Land 中使用`dist`进行输出是一个惯例。源地图是可选的，但是我喜欢在需要的时候关闭它们。严格模式也是可选的，但是如果以后需要的话，从严格开始变得宽松一点更容易。我强烈建议启用它，因为默认情况下它是禁用的。

# 连接起来

现在我们可以讨论`package.json`文件了。这里有一个名为 [copee](https://github.com/styfle/copee) 的包的例子:

```
{ 
  "name": "copee",
  "version": "1.0.0",
  "description": "Copy text from browser to clipboard...natively!",
  "repository": "styfle/copee",
  "files": [ "dist" ],
  "main": "dist/copee",
  "types": "dist/copee.d.ts",
  "scripts": {
    "mjs": "tsc -d && mv dist/copee.js dist/copee.mjs",
    "cjs": "tsc -m commonjs",
    "build": "npm run mjs && npm run cjs"
  },
  "devDependencies": {
    "typescript": "^2.5.3"
  }
}
```

前四行定义了包`name`、`version`、`description`和 GitHub `repository`，它们是自我解释的。

接下来，我们定义`files`，我们简单地将它定义为一个单独的文件夹`dist`。这些是将发布到 npm 的文件。

你的包的入口点被定义为`main`，这就是神奇发生的地方。请注意，没有像预期的那样有文件扩展名(比如`.js`)。这将允许 Node 根据用户导入软件包的方式选择文件——是旧的 CJS 还是新的 ESM。

接下来是`types`，这对于想要通过 TypeScript 导入的用户来说是必需的。如果你正在用 TypeScript 编写你的包，你应该明确地包含`types`以便你的 TS 用户获得类型安全！说真的，这样做是对的。

现在有趣的部分来了:`scripts`。这些是您可以通过`npm run thenameofthescriptgoeshere`运行的构建步骤。第一个构建步骤`mjs`使用 TypeScript 编译器(`tsc`)使用我们之前定义的`tsconfig.json`文件，加上一个发出我们的`.d.ts`类型定义的`-d`标志来构建我们的代码。还要注意将输出文件从`.js`移动到`.mjs`的`mv`命令。这是我们的 ESM 输出。

我们的下一个脚本，`cjs`使用 TypeScript 编译器(`tsc`)构建相同的源代码，但是将输出作为 CommonJS 模块发出。这是 Node.js 的模块系统，由`browserify`、`webpack`等理解。

最后，我们有`devDependencies`是你的构建工具。在这种情况下，我们所需要的是包含上面使用的`tsc`命令的`typescript`。

# 节点使用

我将向您展示如何编写一个导入上述包的消费者。如果您已经经常使用 Node，请跳到下一节了解 ESM 的用法。

首先，安装 [copee](https://www.npmjs.com/package/copee) 包:

```
npm install --save copee
```

然后，用以下内容创建一个`index.js`文件:

```
const { toClipboard } = require('copee'); console.log('CJS: We found a ', typeof toClipboard);
```

新程序可以这样执行:

```
node index.js
```

# 节点 ESM 使用情况

我将向您展示如何编写一个导入上面的 [copee](https://www.npmjs.com/package/copee) 包的消费者。

安装 [copee](https://www.npmjs.com/package/copee) 后，创建一个`index.mjs`文件。您必须使用迈克尔杰克逊脚本扩展(。mjs)。

```
import { toClipboard } from 'copee';
console.log('ESM: We found a ', typeof toClipboard);
```

新程序可以这样执行:

```
node --experimental-modules index.mjs
```

# 浏览器 ESM 使用

节点的使用并不那么引人注目，因为从一开始就有模块，但是 ESM 的美妙之处在于，在节点模块中执行的相同代码将在浏览器中不变地运行！是的，是真的！请尽情欣赏下面这段优雅的代码片段:

```
<script type="module">
  import {
    toClipboard
  } from 'https://cdn.jsdelivr.net/npm/copee/dist/copee.mjs'; $('#btn').on('click', () => {
    const win = toClipboard('Wow, "copee" works!');
      if (win) {
        // it worked, check your clipboard!
      }
  });
</script>
```

我们为`module`提供了一个新的脚本类型，我们使用 [jsDelivr](https://www.jsdelivr.com/) 在 CDN 上自动托管我们的代码。这使得编写一个单独的导入行并在全世界的浏览器中使用 [copee](https://www.npmjs.com/package/copee) 包变得很容易！

# 传统浏览器

你会说，传统浏览器呢？不是所有人都支持 ESM？这可以通过捆绑 UMD 和 T1 来解决。安装完`rollup`后，将它添加到你的`package.json`文件的`scripts`部分。

```
{
  "umd": "rollup -i dist/copee.mjs -o dist/copee.umd.js -f umd -n copee"
}
```

您可以在同一页面上包含 ESM 和 UMD 版本，而不会发生冲突。请参见下面的片段:

```
<script nomodule src="https://cdn.jsdelivr.net/npm/copee/dist/copee.umd.js"></script> <script type="module">
  import {
    toClipboard
  } from 'https://cdn.jsdelivr.net/npm/copee/dist/copee.mjs';
</script>
```

通过使用`nomodule`属性，您告诉新浏览器忽略 UMD 脚本。通过使用`type=module`,你是在告诉老浏览器忽略 ESM。现在大家都赢了！

您可以在[演示页面](https://styfle.github.io/copee/)上看到该解决方案的工作演示。

此外，请查看 [GitHub repo](https://github.com/styfle/copee) 了解更多细节，当然还有工作源代码！

*原载于 2017 年 10 月 16 日*[*www.ceriously.com*](https://www.ceriously.com/blog/post.php?id=2017-10-16-es6-modules-today-with-typescript.md)*。*