# 从 0 到 100%的覆盖率非常快

> 原文：<https://medium.com/hackernoon/from-0-to-100-coverage-real-quick-d71dda7069e5>

![](img/56069b0d414755f1a7a29e3585a9e7c0.png)

Jest — A JS testing framework used by Facebook

## 我的体验单元测试[首映](http://pedsmoreira.github.io/premiere)

[我最近发布了关于 Premiere](https://hackernoon.com/consuming-restful-apis-with-premiere-ae712d1bc935#.xxhq0vkez) 的文章，这是一个 Javascript 库，我发布它是为了方便使用 RESTful APIs，我的目标之一是使这个库可靠，达到 100%的测试覆盖率，或者至少接近这个水平。

要这样做，我的第一个选择是选择如何去做；于是我开始阅读流行的摩卡/柴/西农+伊斯坦布尔进行测试覆盖。虽然我设置了基本的测试环境和脚本，但我的经历非常痛苦；使用 [Typescript](http://www.typescriptlang.org/) 使得测试更加困难。

> 如果你习惯了 Karma，Jasmine，Mocha/Chai/Sinon 并且和他们在一起很开心，那也不错。这篇文章是关于我到目前为止对这些技术的体验。

在经历了大量的忙乱、花费的时间和导入的包之后，测试`watcher`和`coverage`仍然不能为我工作。为了做简单的测试，我不得不从许多库中导入我的代码，有些来自`chai`，有些来自`sinon`，有些来自`sinon-chai`，这看起来非常困难和混乱；此外，我正在处理打字稿，需要所有的打字工作。到最后，我非常沮丧，这一切都偏离了我的主要关注点，那就是写测试，而不是做配置。

在做一些研究时，我发现了 Jest，一个由脸书创建和使用的测试框架。起初我以为它是为 React 项目设计的，但是通过查看 Jest 主页，我开始注意到它是多么强大和容易安装。更好的是，有一个[不错的软件包](https://github.com/kulshekhar/ts-jest)可以轻松处理打字稿。

设置 Jest 像微风一样容易，它有一个很好的观察者，测试覆盖面开箱即用。整个流程感觉非常流畅，一切就绪使它变得容易多了，文档齐全的 api 也非常有用。

一旦一切就绪，接下来的事情就是仔细检查每个功能，确保所有功能都被覆盖，以获得**期望的 100%❤**。基本功能、`describe`、`it`和`expect`在大多数情况下已经足够，剩下的由对`Promise`的支持来完成。为了帮助隔离每个测试，用`jest.fn()`创建模拟也很有帮助。

让我们看一些代码，所以这听起来不像是脸书的免费营销。以下代码写在[类型脚本](http://www.typescriptlang.org/)中，引用 [Premiere](http://pedsmoreira.github.io/premiere) 的[缓存类](https://github.com/pedsmoreira/premiere/blob/master/lib/premiere/Cache.ts)。

首先，让我们导入主类。我遵循的模式是将`.spec.ts`放在与“原始”文件相同的文件夹中。

```
**import** Cache **from** './Cache';
```

现在让我们设置我们的`describe`块，并为我们的测试准备一些变量，这样我们的生活会更轻松。注意，我们使用的是类型为`any`的变量，所以除了缓存之外，我们不需要导入更多的东西，目的是让我们的`spec`更加独立。

```
describe('Cache', () => {
    **let** cache: Cache;
    **let** model: **any**;
    **let** list: **any**[];
    **let** promise: **any** = 'promise instance';
```

现在，在每次测试之前，我们将初始化我们的变量。

```
beforeEach(() => {
    cache = **new** Cache(**null**);
    model = {key: jest.fn().mockReturnValue('id')};
    list = [model];
});
```

为了掌握它是如何工作的，让我们来看几个测试。

```
it('should set api on construct', () => {
    cache = **new** Cache('api' as **any**);
    expect(cache.api).toBe('api');
});it('should resolve key from object', () => {
    expect(Cache.*resolveKey*(model)).toBe('id');
});it('should set list', () => {
    cache.setList('list', list);
    expect(cache.lists['list']).toBe(list);
});
```

如果您希望查看整个文件，请参考[https://github . com/pedsmoreira/premiere/blob/master/lib/premiere/cache . spec . ts](https://github.com/pedsmoreira/premiere/blob/master/lib/premiere/Cache.spec.ts)

# 配置

Jest 配置非常简单。只是把这些部分添加到了 [package.json](https://github.com/pedsmoreira/premiere/blob/master/package.json) 中。

脚本:

```
“test”: “jest — coverage — no-cache ${1}”,
“test-watch”: “jest ${1} — watch”,
```

开发依赖项:

```
"@types/jest": "^16.0.2",
"jest": "^18.0.0",
"ts-jest": "^18.0.0",
```

最后是“jest”块。

> 这一部分是必要的，因为项目使用 Typescript。不要害怕，这主要是样板文件，被 [ts-jest](https://github.com/kulshekhar/ts-jest) 很好地记录了下来。

```
"jest": {
  "moduleFileExtensions": [
    "ts",
    "js"
  ],
  "transform": {
    "\\.(ts)$": "<rootDir>/node_modules/ts-jest/preprocessor.js"
  },
  "testRegex": "lib/.*\\.spec.(ts|js)$",
  "testResultsProcessor": "<rootDir>/node_modules/ts-jest/coverageprocessor.js",
  "globals": {
    "__TS_CONFIG__": {
      "target": "es6",
      "module": "commonjs",
      "moduleResolution": "node"
    }
  }
}
```

完整的 package.json 可从[https://github . com/pedsmoreira/premiere/blob/master/package . JSON](https://github.com/pedsmoreira/premiere/blob/master/package.json)获得

所以，如果你有机会，不管有没有，试一试笑话，你可能会给自己带来惊喜。感觉事情“刚刚好”的感觉非常好\o/

希望你喜欢:)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)