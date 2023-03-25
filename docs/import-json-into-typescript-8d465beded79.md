# 如何将 json 导入 TypeScript

> 原文：<https://medium.com/hackernoon/import-json-into-typescript-8d465beded79>

在 ES6/ES2015 中，您可以在代码中导入 json 文件。举个例子，

鉴于您有这个`example.json``文件:

```
{
    "name": "testing"
}
```

您可以像这样在 ES6/ES2015 中导入它。

```
// ES6/ES2015
// app.jsimport * as data from './example.json';const word = data.name;console.log(word); // output 'testing'
```

然而，在[的 Typescript](https://hackernoon.com/tagged/typescript) 中，同样的代码会抛出错误:

```
Cannot find module 'example.json'
```

## 【更新】解决方案:Typescript 2.9 支持 JSON 导入！

如果您使用的是 Typescript 版，则不需要遵循解决方案 2。你可以这样做:

在您的“tsconfig.json”文件中，在编译器选项下，添加这两行:

```
{
  "compilerOptions": {
    "resolveJsonModule": true,
    "esModuleInterop": true  
  }
}
```

然后可以这样导入 [json](https://hackernoon.com/tagged/json) :

```
// Typescript
// app.tsimport data from './example.json';const word = (<any>data).name;console.log(word); // output 'testing'
```

很简单！

## 解决方案:使用通配符模块名

在 Typescript **version 2 +** 中，我们可以在模块名中使用[通配符。在您的 TS 定义文件中，例如`typings.d.ts`，您可以添加这一行:](http://www.typescriptlang.org/docs/release-notes/typescript-2.0.html#wildcard-character-in-module-names)

```
declare module "*.json" {
    const value: any;
    export default value;
}
```

然后，您的代码将像魅力一样工作！

```
// Typescript
// app.tsimport * as data from './example.json';const word = (<any>data).name;console.log(word); // output 'testing'
```

此处示例代码:[https://github.com/chybie/ts-json](https://github.com/chybie/ts-json)

这个解决方案也适用于 Angular project。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！