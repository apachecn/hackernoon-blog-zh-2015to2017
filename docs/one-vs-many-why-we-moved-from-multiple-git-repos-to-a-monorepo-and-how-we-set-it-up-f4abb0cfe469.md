# 一对多——为什么我们从多个 git 回购转移到一个 monorepo，以及我们如何设置它

> 原文：<https://medium.com/hackernoon/one-vs-many-why-we-moved-from-multiple-git-repos-to-a-monorepo-and-how-we-set-it-up-f4abb0cfe469>

## 管理源代码如何变得透明

**访问**[**【https://www.mikenikles.com】**](https://www.mikenikles.com)**获取我的最新博文。**

*这篇博文是我分享我们从部署在 AWS 上的单片应用程序(每个都有自己的源代码库)迁移到部署在 Google 云平台上的分布式服务架构(所有源代码都托管在 monorepo 中)的系列文章的一部分。*

*   *第一部分:* [*一个 monorepo，GitHub 流和自动化 FTW*](https://hackernoon.com/a-monorepo-github-flow-and-automation-ftw-c41a2d9c48bb)
*   **第 2 部分(本文):“一对多——为什么我们从多个 git 回购转移到一个 monorepo，以及我们如何设置它”**
*   **第三部分:*[](/@mikenikles/a-mostly-automated-release-process-63bb9516985a)*
*   ****第四部分:* [我们对软件开发一致性的方法](/@mikenikles/our-approach-to-software-development-consistency-d101995bb843)***
*   ****第 5 部分(即将推出):“本地调试微服务”****

# **多个存储库意味着多种一切**

**让我们列出一些我们需要用存储库来管理的东西:**

*   **属国**
*   **测试配置**
*   **拉式请求模板**
*   **拉式请求/标签**
*   **埃斯林特**
*   **较美丽**
*   **部署和发布脚本**

**对于某些事情，比如管理依赖性，像 [Greenkeeper](https://greenkeeper.io/) 这样的服务可能会有所帮助。然而，如果一个依赖项发布了一个新的主要版本，您必须手动地将它应用到所有的存储库并运行测试。**

**很明显，我们都不喜欢这些维护任务，我们宁愿花时间让我们的市场研究聊天机器人对我们的客户更有价值。**

# **属国**

## **莱尔纳**

**我们的代码主要是用 Javascript 写的，这就带我们来看看 [Lerna](https://lerna.js.org/) 。**

> **Lerna 是一个用多个包管理 JavaScript 项目的工具。**

**我们决定更进一步。我们不仅管理我们的 npm 包，还将 Lerna 配置为管理我们的服务，这些服务位于同一个 monorepo 中。**

**我们的 monorepo 目录结构如下:**

```
**.
├── lerna.json
├── package.json
├── packages
└── services**
```

**`lerna.json`文件非常简单:**

```
**{
  "lerna": "2.4.0",
  "npmClient": "yarn",
  "useWorkspaces": true, // See "Yarn Workspaces" below
  "packages": ["packages/*", "services/*"],
  "version": "independent"
}**
```

**通过这种配置，我们的服务可以依赖于包，Lerna 负责对它们进行符号链接。例如，我们可以在`services/service-a`目录下运行`yarn add package-z`，并正确地学习符号链接`package-z`。不再与`yarn link`打交道。**

## **NPM 范围的包**

**对勒纳来说，`packages/*`和`services/*`被认为是包。大多数 lerna 命令支持`--scope` [标志](https://github.com/lerna/lerna#--scope-glob)，但只有在`package.json`文件中遵循严格的`name`属性命名约定时，这种情况才会发生。**

**我们决定通过使用不同的[范围的包](https://docs.npmjs.com/misc/scope)将包和服务分开。由于`packages/*`被部署到 NPM，他们使用公司默认范围(例如`@my-company`)。`services/*`中的服务使用`@my-company-services`范围。包和服务进一步以`web-*` vs `svr-*`为前缀，以区分不同类型的包和服务。**

## **纱线工作空间**

**Lerna 非常擅长管理相互依赖关系，运行 npm 脚本，甚至跨所有包或其子集运行任意命令。**

**但是，默认情况下，每个包和服务都有自己的`node_modules`文件夹。这是一个很大的重复…**

**给我们 Yarn 的好心人发布了“工作空间”,并友好地在博客上介绍了如何与 Lerna 一起使用它:[https://yarnpkg.com/blog/2017/08/02/introducing-workspaces/](https://yarnpkg.com/blog/2017/08/02/introducing-workspaces/)**

**除了`lerna.json`中的`"useWorkspaces": true`之外，您还必须将`"workspaces": ["packages/*", "services/*"]`添加到您的根`package.json`文件中。就是这样。**

**现在当你运行`yarn`和`lerna bootstrap`时，你的根`node_modules`文件夹包含了几乎所有你需要的 npm 包。这既节省了时间又节省了磁盘空间。下面展示了不使用 Yarn 工作空间和在我们的 monorepo 中使用它之间的区别。这些统计数据是基于 Lerna 管理的 20 个包，在 2016 年的 MacBook Pro 上运行。**

****无纱线工作区****

```
**+-----------------+--------+
|     Command     |  Time  |
+-----------------+--------+
| yarn install    | 13.23s |
| lerna bootstrap | 72.33s |
+-----------------+--------+**
```

**这将向磁盘添加 96，112 个文件，总容量为 666.4mb。**

****带纱线工作区****

```
**+-----------------+--------+
|     Command     |  Time  |
+-----------------+--------+
| yarn install    | 17.26s |
| lerna bootstrap | 3.85s  |
+-----------------+--------+**
```

**这将向磁盘添加 32，008 个文件，总容量为 267.1mb。**

## **结论**

**多等 4 秒钟来安装根包是值得的。通过在持续集成服务器上进行一点缓存，事情看起来会更好，但是我想得太多了。**

# **测试配置**

**我们使用 [Jest](http://facebook.github.io/jest/) ，但是决定让 Lerna 管理测试运行程序实例。(仅供参考，Jest 附带了一个[多项目运行器](https://facebook.github.io/jest/blog/2017/05/06/jest-20-delightful-testing-multi-project-runner.html)，可能对您的用例有用。)**

**在我们的例子中，我们喜欢 Lerna 提供的`--scope`标志，以便只在某些目录中运行命令。
更重要的是，我们有各种各样的包和服务，有些可以在 Node.js 中使用，有些可以在浏览器中使用，有些是同构的。**

**为了适应这种情况，我们有以下 Jest 配置设置:**

```
**.
├── jest.config.js
├── packages
│   ├── iso-package
│   │   ├── jest.config.js
│   ├── svr-package
│   │   └── jest.config.js
│   └── web-package
│       └── jest.config.js
├── services
│   ├── svr-service
│   │   └── jest.config.js
│   ├── web-service
│       └── jest.config.js
└── tests-setup
    ├── polyfill.js
    └── setup.js**
```

**根级`jest.config.js`包含我们应用于所有包和服务的基本 Jest 配置。它看起来像这样:**

```
**// jest.config.js
module.exports = {
  collectCoverageFrom: ['**/*.js'],
  resetMocks: true,
  verbose: true
}**
```

## **Web 包和服务**

**一个`web-*`包或服务在其根目录中使用下面的`jest.config.js`:**

```
**// packages/web-*/jest.config.js or services/web-*/jest.config.js
const jestBase = require('../../jest.config.js')
module.exports = {
  ...jestBase,
  coverageThreshold: {
    global: {
      statements: 100,
      branches: 100,
      functions: 100,
      lines: 100
    }
  },
  browser: true,
  setupFiles: [
    '<rootDir>/../../tests-setup/polyfill.js',
    '<rootDir>/../../tests-setup/setup.js'
  ]
}**
```

## **Node.js /同构包和服务**

**`iso-*`或`svr-*`包或服务在其根目录中使用以下`jest.config.js`:**

```
**const jestBase = require('../../jest.config.js')
module.exports = {
  ...jestBase,
  coverageThreshold: {
    global: {
      statements: 100,
      branches: 100,
      functions: 100,
      lines: 100
    }
  },
  testEnvironment: 'node'
}**
```

**注意我们是如何在每个包/服务级别上配置`coverageThreshold`的？这允许各个团队设置自己的阈值。管理每个包/服务要比在 monorepo 根级别简单得多。**

## **测试执行**

**根文件`package.json`包含一个`"test": "lerna exec yarn test"`脚本。每个包和服务都有自己的`test`脚本，该脚本简单地调用 Jest: `"test: jest"`。这种模式也适用于`test:coverage`。**

**我们现在可以使用 Lerna 的[标志](https://github.com/lerna/lerna#flags)做各种各样的好事:**

*   **对所有服务进行测试:`yarn test --scope @my-company-services/*`。**
*   **对所有 web 包运行测试覆盖率:`yarn test:coverage --scope @my-company/web-*`。**
*   **对`@my-company/iso-package`包以及依赖于它的所有包和服务运行测试:`yarn test --scope @my-company/iso-package --include-filtered-dependencies`。**

****为什么使用** `**lerna exec**` **来执行一个 npm 脚本，而** `**lerna run**` **正是这样做的？****

**从我们遇到的情况来看，`lerna run`吞掉了 npm 脚本的输出。使用`--stream`标志，我们得到了输出，但它既没有格式化，也没有彩色控制台输出。**

## **结论**

**虽然我可以想象 Jest 的多项目运行器比我们的解决方案更有性能，但我们喜欢 Lerna 的强大标志，并决定放弃 Jest 的方法。随着越来越多的测试加入 monorepo，这种情况很可能会改变。(如果有人有什么想法，我很乐意与之交流)**

# **埃斯林特&更漂亮**

**没有必要特别考虑。只需将您的配置文件添加到存储库根目录，它就可以像预期的那样工作了。**

# **拉式请求、模板和标签**

**拉取请求模板在`.github/PULL_REQUEST_TEMPLATE.md`文件中配置一次。它适用于所有软件包和服务。**

**与多个存储库相比，在 monorepo 中管理拉请求需要更多的思考。在撰写本文时，我们还没有决定如何处理这个问题。初步讨论的一些注意事项包括:**

*   **为每个包装/服务使用独特颜色的标签(尽管我们会很快用完可区分的颜色)**
*   **对新特性使用绿色标签，对错误使用红色标签。为每个包/服务创建绿色和红色标签，并将包/服务名称添加为标签名称。**

# **结论**

**团队很快就意识到了单一回购的好处。在此之前，我们使用`yarn link`来处理一个我们用来与后端 API 集成的小型 SDK。这是可行的，如果你小心谨慎，不要像我们在当地发展那样与 Docker 打交道。尽管如此，这仍然是从事 SDK 的每个开发人员的精神负担。**

**配置好一切需要时间，我不打算粉饰这一点。感谢一个令人惊讶和好奇的团队，他们在整个过渡期表现出了耐心，我们现在可以花更多的时间来构建软件，而不是维护源代码库。谢谢大家！**