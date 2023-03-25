# 如何为 Angular(2+)创建库并从头发布到 npm。

> 原文：<https://medium.com/hackernoon/how-to-create-library-in-angular-2-and-publish-to-npm-from-scratch-f2b1272d6266>

![](img/256e4ff818f1bf60705d26b6510d5a31.png)

[http://streetwill.co/posts/240-on-the-table](http://streetwill.co/posts/240-on-the-table)

我更新了 make 库以兼容 AOT。您可以在“*让您的库兼容 AOT”一节中查看更新*

在这篇文章中，我为 angular 2 创建了一个库，但是你可以将这个方法应用到任何稍加修改的框架上。

如果你已经在前端领域工作了一段时间，你会发现有一天你必须做和以前一样的工作。我打赌你(过去)会复制你的旧代码并粘贴到新项目中(或者你会重新从头开始写，因为你的旧代码太乱了)。别担心，几乎每个人都这样做过，包括我。

有一天，我为一个项目创建了一个表格界面(带有分页功能)。但是在另一个项目中也有表格 ui。我不得不复制并粘贴到新项目。问题是，当我在我的表中发现错误时，我必须修复每个项目中具有相同表的所有表组件。太可怕了，太无聊了。复制和粘贴有很多问题，我相信你已经知道了。

所以有一天我想，为什么我不做一张表发布到 npm 呢？这真的很棒，因为我只需要在一个地方改变我的代码，只需要在每个项目中更新我的表库(用`npm update mylovelytable`)，我可以很容易地用`npm install mylovelytable`安装它或改变表库代码，而不用担心它会破坏你的旧项目，因为它不会自动更新，除非你通过运行`npm update mylovelytable`来更新它。

唯一的(也是最大的)问题是我不知道如何去做:/

所以这篇文章是我通过阅读许多文章，看其他库代码(例如@angular/material)试错，在 angular 中创建了库。

*感谢* [resolve.modules](https://medium.com/u/5ae4b2205cba#resolve-modules) 中指定的目录中查找文件。它的默认设置是 *node_modules* 文件夹*。*

您也可以使用相对路径和绝对路径。

```
import plus2num from './idoaboutmathcalculate';
//or
import plus2num from 'absolute/path/idoaboutmathcalculate';
```

你可以注意到没有文件扩展名。默认为`.js`，可以在模块捆绑器配置文件中设置默认文件扩展名。

# 让我们写一些真正的代码吧！

*好的，我已经理解了一些关于模块的基本概念，但是它与创建库有什么关系呢？*

无论你做什么库，都是关于模块的——独立的功能，可重用，只做一件事，而且做得很好。如果你能创建你的模块，把你的代码推到 npm 或者别的什么上并不难。

你还在吗？很好。让我们最终创建我们可爱的图书馆。

我要做的库叫做“空文本”。这个库包括`emptytext`指令，用于在元素或组件中没有文本时显示“empty”，用于改变消息(默认为“empty”)。首先让我们设置项目和一些配置。

这是文件夹结构。

```
/lib — your source code
   empty-text.directive.ts
   empty-text.module.ts
   empty-text.service.ts
   empty-text.ts/tools — node files for utility work (I’ll explain later)
   cleanup.js
   copy-package.js
   removecode.js/dist — your module that user will get when ‘npm install’package.json
rollup.config.esm.js
rollup.config.umd.js
tsconfig.json
```

**对于本帖中的代码，你可以在这里** 找到[](https://github.com/Elecweb/emptytext/tree/master)

**首先，让我们创建一个 package.json 文件，并按照提示进行操作。**

```
npm init
```

**在这篇文章中，我将使用 typescript 编写代码(它不要求)。**

```
npm install -g typescript
```

**并为 typescript 编译器设置一些配置。我创建了名为`tsconfig.json`的配置文件。**

```
{
   “compilerOptions”: {
      “target”: “es5”,
      “module”: “es2015”,
      “sourceMap”:   true,
      “moduleResolution”: “node”,
      “emitDecoratorMetadata”: true,
      “experimentalDecorators”: true,
      “declaration”: true,
      “outDir”: “./dist”,
      “lib”: [“es2015”, “dom”]
   },
   “files”: [“./lib/empty-text.ts”]}
```

*   **`target`:编译代码的 javascript(ECMAScript)版本。**
*   **`module`:编译代码的模块格式。有几个选项，例如，UMD，ES2015，AMD。**
*   **`sourcemap`:告诉 typescript 编译器生成 [sourcemap](https://www.html5rocks.com/en/tutorials/developertools/sourcemaps/) 。**
*   **`module resolution`:模块是如何被解析的(从`import`的什么地方看你的依赖文件)，有两种方式，节点或者经典。[了解更多详情](https://www.typescriptlang.org/docs/handbook/module-resolution.html)。**
*   **`emitDecoratorMetadata`和`experimentalDecorators`:angular 需要设置为 true(他们用，比如@Component 和@Directive。**
*   **`declaration`:生成对应的. d.ts 文件([定义文件](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html))。**
*   **`lib`:内置 API 声明(定义文件)，可以选择包含在项目中。**
*   **`files`:你想用`tsc`编译你的类型脚本文件的地方**

**现在我可以用**

```
tsc -p tsconfig.json // or just tsc
```

**在这个库中，我需要“角/芯”**

```
npm install @angular/core --save
```

**现在不要太担心如何编译我们的代码，我稍后会解释。**

# **让我们编写库代码**

**我不会解释太多代码，它很容易理解，不是这篇文章的重点。**

**这是指令。`lib/empty-text.directive.ts`**

**这里是设置消息的服务，`lib/empty-text.service.ts`**

**这是用户要导入的模块。`lib/empty-text.directive.ts`**

**创建条目文件以导出库中所有其他文件，`empty-text.ts`**

```
export * from "./empty-text.module";export * from "./empty-text.directive";export * from "./empty-text.service";
```

**就是这样。这个库很小，只有 4 个文件。**

# **让我们为 bundler 设置配置**

**您需要将文件中的代码捆绑到一个文件中，该文件的格式是 UMD(UMD 是**通用模块定义，**它可以用于任何模块格式)和 ES2015。**

**我们提供 ES2015 的原因是，如果用户使用 Rollup 或 Webpack，他们可以从[树摇动](https://webpack.js.org/guides/tree-shaking/)中获益。**

**没有关于你应该如何在 Angular 中为用户提供代码的文档(如果有人知道，请在下面评论)。**

**所以我看着 `*@angular/material*` *他们如何建立他们的文件，并在我的库中使用他们的格式。***

**首先，你需要模块捆绑器。可以是 Webpack，Rollup 什么的。在这篇文章中，我将使用 Rollup。**

```
npm install --global rollup
```

**你需要捆绑两种格式的代码，UMD 和 ES2015，所以有两个配置文件汇总。**

**不同的是`format`属性分别为 UMD 和 ES2015 设置了`umd`和`es`。**

**由于 Rollup 的配置文件是普通的 javascript 文件，您只能导入和更改`format`属性，因此您不需要手动复制其他配置属性。**

**我将首先为`umd`设置配置文件。**

**这是配置文件，`rollup.config.umd.js`**

*   **`input`:您要捆绑的条目文件的位置。**
*   **`output`:捆绑文件配置，包含以下内容:**
*   **`output.format`:您捆绑文件的模块格式。**
*   **`output.name`:变量名，代表你的`iife` / `umd`包。**
*   **`output.sourcemap`:设置为 true，这样汇总将提供 sourcemap。(在这种情况下没有必要，因为我不会为了简单而缩减代码。)**
*   **`output.file`:要写入的文件。**
*   **`external`:排除捆绑代码中的依赖代码，在这种情况下，我假设使用这个库的用户已经安装了`@angular/core`**
*   **`onwarn`:拦截警告信息的功能。我忽略了两个不必要的警告(`THIS_IS_UNDEFINED`和`MISSING_GLOBAL_NAME`)。**
*   **`plugins`:汇总中使用的插件。接下来我将解释我使用什么插件以及为什么**

**我们需要在 Rollup 中使用插件来制造魔法快乐。**

*   **汇总插件节点解析**
*   **汇总-插件-常见**
*   **汇总-插件-角度**
*   **汇总插件类型脚本**
*   **节点-sass**

**我将解释这些插件是做什么。但首先，让我们安装这些插件。**

```
npm install --save-dev rollup-plugin-node-resolve rollup-plugin-commonjs rollup-plugin-angular rollup-plugin-typescript node-sass
```

**让我们看看`plugins`一节**

***摘自* `rollup.config.umd.js`**

*   **`angular`:如果你没有为组件内联模板和样式，你需要`rollup-plugin-angular`来解析要包含在组件中的外部模板和样式。**
*   **`node-sass`:在组件中包含样式之前，将 sass 解析为 css。我们在`angular`插件中使用它**

```
if(scss){ css = sass.renderSync({ data: scss }).css.toString();}else{ css = '';}return css;
```

***示例中的库没有任何组件，但我想展示如何为提供组件的库进行配置。如果你的库不提供组件，你可以跳过* `rollup-plugin-angular`和`node-sass`配置部分。**

*   **`typescript`:将类型脚本移植到普通 javascript。**
*   **`resolve`:使用[节点解析算法](https://nodejs.org/api/modules.html#modules_all_together)定位模块。**
*   **`commonjs` : Rollup 只能理解模块格式 ES2015，但是你可能安装的一些库是 commonjs，所以你需要这个插件以便 Rollup 能理解 commonjs 模块。**

**对于 ES2015 模块配置文件，我们不必手动复制。只需从`rollup.config.umd.js`导入配置并覆盖一些配置。**

```
import config from './rollup.config.umd.js';import {nameLibrary,PATH_DIST} from './config-library.js'config.output.format = "es";
config.output.file = PATH_DIST+nameLibrary+".esm.js"export default config;
```

**我将`output.format`替换为“es ”,将`output.file`替换为捆绑文件的名称。**

**你可能已经注意到了，我从`config-library.js`导入了一些变量，这些变量是为捆绑文件的名称和目标路径定义的，这样我们就不必复制它了。**

***config-library.js***

```
export const nameLibrary = "empty-text";export const PATH_SRC = "lib/";export const PATH_DIST = "dist/";
```

**现在，您可以使用以下命令捆绑已定义的配置:**

```
rollup -c rollup.config.umd.js //umd
rollup -c rollup.config.esm.js //es2015
```

# **为用户清除不必要的文件。**

**当我们打包时，会有一些不必要的文件。ts 文件(因为我们将为每种模块格式提供一个捆绑 javascript 文件)**

**我将使用 node 来处理删除不必要的文件，并将`package.json`文件复制并粘贴到`dist`文件夹中(我将很快解释原因)。**

**在`tools`文件夹中，我创建了`copy-package.js`，用于将根文件夹中的`package.json`复制到`dist`文件夹中。**

```
const fs = require('fs');let resizable = fs.readFileSync('package.json').toString();fs.writeFileSync('dist/package.json', resizable);
```

**和`cleanup-package.js`，用于删除`script`部分和`devDependencies`。**

```
const fs = require('fs');const packageJson = JSON.parse(fs.readFileSync('./dist/package.json').toString());delete packageJson.devDependencies;delete packageJson.scripts;fs.writeFileSync('./dist/package.json', JSON.stringify(packageJson, null, 2));
```

**最后一个文件是`removecode.js`，用于删除从 Typescript 编译器发出的不必要的 javscript 文件。我们只是希望 IDE 的定义类型脚本(例如，自动完成)提供给用户。**

```
const del = require('del');del(['dist/!(*.umd.js|*.esm.js|*.d.ts|*.umd.js.map|*.esm.js.map)']).then(paths => { console.log('Files and folders that would be deleted:\n', paths.join('\n'));});
```

**它会删除除捆绑文件(*.umd.js，*.esm.js)、definition typscript ( *.d.ts)和 sourcemap ( *.umd.js.map，*.esm.js.map)之外的所有文件。**

**我使用`del`库来删除文件。**

**从 npm 安装`del`。**

```
npm install --save-dev del
```

**现在，您可以使用`node`命令运行我们在本节中定义的脚本。例如，如果您想将 package.json 复制并粘贴到`dist`文件夹，只需在您喜欢的命令行中运行`node tools/copy-package.js`即可。**

**我们需要运行许多命令。1)我们需要移除`dist`文件夹来清除文件。2)用`rollup` (2 个命令用于 umd 和 ES2015)捆绑文件。3)运行节点脚本(3 个命令用于复制 package.json，清除 package.json 中的脚本和 devdependencies，删除不必要的代码)。**

**如果我们只运行一个命令来为我们完成这些任务，那就更好了。**

# **设置 npm `script` 以便轻松构建**

**最后你可以建图书馆了！最后一个任务设置在`package.json`中`script`，这样你就不必输入很多命令来构建我们的代码。**

```
..."scripts": { "copy": "node tools/copy-package.js && node tools/cleanup.js", "bundle": "rimraf dist && rollup -c rollup.config.umd.js && rollup -c rollup.config.esm.js && tsc", "postbundle": "node tools/removecode.js", "build": "npm run bundle && npm run copy"},
```

**现在你只需要写一个命令**

```
npm run build
```

# **为模块解析设置一些配置**

**当用户使用`import`我们的库文件夹时，它会在`package.json` 的属性中寻找我们提供的文件。[更多细节](http://blog.mgechev.com/2017/01/21/distributing-an-angular-library-aot-ngc-types/)——在*分配组件*部分**

> **将`*package.json*`的`*main*`属性的值设置为指向 ES5 UMD 包。**
> 
> **将`*module*`属性的值设置为指向库的`*esm*`版本的入口文件。`*module*`是`*package.json*`中的一个字段，rollup 和 webpack 2 等捆绑器希望在其中找到对 ES2015 版本代码的引用。注意，一些旧版本的捆绑器使用`*jsnext:main*`而不是`*module*`，所以我们需要设置这两个属性。**
> 
> **…**

***package.json***

```
"main": "empty-text.umd.js","jsnext:main": "empty-text.esm.js","module": "empty-text.esm.js","types": "empty-text.d.ts",
```

# **使您的库与 AOT 兼容[更新！(26/07/2017)]**

**因为 angular 2 的库需要与 AOT 兼容。你需要运行`ngc`来发射`.metadata.json`到你的包旁边。**

**首次安装`@angular/compiler-cli`:**

```
npm install --save-dev @angular/compiler-cli
```

**然后更新`scripts`部分的`package.json`:**

```
"scripts": {
   ..., "bundle": "rimraf dist && rollup -c rollup.config.umd.js && rollup -c rollup.config.esm.js && tsc && ngc", //<--- add ngc
   ...},
```

**最后一件事是在`tools/removecode.js`中更新，因为没有删除从`ngc`命令发出的`.metadata.json`文件**

```
del(['dist/!(*.umd.js|*.esm.js|*.d.ts|*.umd.js.map|*.esm.js.map|*.metadata.json)']).then(paths => { console.log('Files and folders that would be deleted:\n', paths.join('\n'));});
```

# **让我们建造并向世界发布~**

**现在，您可以使用命令`npm run build`构建文件，该命令将运行`bundle`和`copy`脚本。注意`postbundle`，它将在`bundle`完成后运行。**

**运行`build`脚本后，我们将得到`dist`文件夹中的文件**

```
empty-text.d.ts
empty-text.directive.d.ts
empty-text.esm.js
empty-text.esm.js.map
empty-text.module.d.ts
empty-text.service.d.ts
empty-text.umd.js
empty-text.umd.js.map
```

**剩下的唯一一项任务是将其发布到 npm。**

**我们可以通过命令`npm run publish`向 npm 发布库代码。**

**但是等等！如果我现在发布我的库，用户会得到不必要的文件，比如我们的`lib`和`tools`文件夹。我们应该摆脱他们。**

**有两种方法可以做到这一点。**

*   **创建[。忽略不必要的文件进行发布。](https://docs.npmjs.com/misc/developers#keeping-files-out-of-your-package)**
*   **用`package.json`发布到`dist`文件夹。**

**在这篇文章中，我将使用第二种方法。这就是为什么我需要将`package.json`复制到`dist`文件夹。**

**我们不需要手动复制和粘贴，因为`copy-package.js`会为您完成这项工作。**

**从那里导航到`dist`文件夹和`npm publish`。所以安装我们库的用户只能从`dist`文件夹中获得文件。**

***PS。浏览文件夹并运行命令很无聊，但是我找不到从根目录下的文件夹发布的方法。如果你有什么建议，请在评论中告诉我。***

****更新****

**Maxime Lafarie 建议我在剧本部分加入`“publish”: “cd ./dist && npm publish”`。所以我们只需要运行`npm run publish`。**

**咳..这是一条漫长的路。你真是个好读者。现在你可以向全世界发布你的图书馆了！。**

**用户只需像这样对他们的模块使用`npm install emptytext`和`import` 。**

**`import { EmptyTextModule } from ‘emptytext’;`**

**人们现在可以用甜蜜的指令(或者将来的你。相信我，你会感谢你自己的。**

**如果你有任何问题，或一些建议(真的适用！).请在下面评论。**

**真的是长帖。休息一下，喝杯咖啡。**

**[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)****[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)****[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个大家庭的一员。我们现在[正在接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**

**![](img/be0ca55ba73a573dce11effb2ee80d56.png)**