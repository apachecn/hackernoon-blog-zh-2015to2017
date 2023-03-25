# 在 Gitlab 上设置 CI/CD(分步指南)—第 1 部分

> 原文：<https://medium.com/hackernoon/setting-up-ci-cd-on-gitlab-step-by-step-guide-part-1-826385728223>

![](img/1050dcd24b4b51deec5f1d8d86857681.png)

Source: [http://jeroenhildering.com/2016/04/07/continuous-integration-for-net-applications-using-jenkins/](http://jeroenhildering.com/2016/04/07/continuous-integration-for-net-applications-using-jenkins/)

我们大多数人都同意 C *持续集成(CI)* 、C *持续交付(CD)* 、云基础设施、测试自动化和配置管理构成了开发运维的基础。根据项目的规模，CI/CD 可能很难建立和实施。然而，这绝对是必要的，原因如下:

在一个敏捷的环境中，需求会随着时间的推移而快速发展，为了发布没有 bug 的特性，拥有一套自动化测试和持续集成代码是最重要的。拥有一个合适的 CI/CD 环境为您提供了*信心*来试验、实现新特性并快速推出更新。

事实上，有了适当的 CI，可以更早地发现错误，开发人员可以更快地审查代码，因为对存储库中代码的更改通常一天要合并几次，这样就可以不断地进行验证。

![](img/9e8337518891b30ed7e9cc8642a79d82.png)

You sure you want to do without CI/CD?

通过阅读本文，您将具备能够在不到一小时的时间内**在 [**Gitlab**](https://gitlab.com/) 上设置 CI/CD 环境**并将 **NodeJS** 项目部署到 [**Heroku**](http://heroku.com) 的基础知识。当然，我们不会深入讨论如何编写单元测试，这可以在另一篇文章中进一步探讨。

# 步骤 1:设置 Gitlab 帐户

注册一个帐户并创建一个新项目。命名您的项目，并选择它是私有的还是公共的。

![](img/0e57bcc00ed7c2b19ad90d4f6accb547.png)

当您第一次创建项目时，您需要向其中添加 ssh 密钥。

# 步骤 2:添加 SSH 密钥

要创建一对 SSH 密钥，请执行以下步骤:

```
ssh-keygen -t rsa
```

然后输入保存 SSH 密钥的文件。

```
Enter file in which to save the key (/home/demo/.ssh/id_rsa):
```

最后，您可以选择是否有密码短语。完成后，复制公钥并粘贴到 gitlab 中。

```
cat ~/.ssh/id_rsa_gitlab.pub | pbcopy
```

![](img/f989a32a923b05ae8898be065daaf4cf.png)

点按“添加密钥”后，使用以下命令将密钥储存在您的钥匙串中:

```
ssh-add  ~/.ssh/id_rsa_gitlab
```

将 SSH 配置为总是使用钥匙串。如果您在中还没有一个`config`文件。`ssh`，创建一个`~/.ssh/config`文件。换句话说，在您主目录的`.ssh`目录中，创建一个名为`config`的文件。

在那个`.ssh/config`文件中，添加以下几行:

```
Host gitlab.com
  UseKeychain yes
  AddKeysToAgent yes
  IdentityFile ~/.ssh/id_rsa_gitlab
```

> 来源:[https://apple . stack exchange . com/questions/48502/how-can-I-permanently-add-my-ssh-private-key-to-keychain-so-it-is-automatically](https://apple.stackexchange.com/questions/48502/how-can-i-permanently-add-my-ssh-private-key-to-keychain-so-it-is-automatically)

# 步骤 3:设置试运行和生产环境

![](img/3162b03ea6be20c288fcf38583ac5d39.png)

在 Heroku 中，创建一个管道来保存临时和生产应用程序。

在管道内部，创建暂存和生产应用程序。注意，如果分期 app 的名称后面能追加'分期'两个字，以明确区分两个 app 就好了。

![](img/9b351be58e9c98a8938d7d861c7672d0.png)

设置应用程序后，它看起来会像这样。

![](img/86818445892f6094a17502d95a561919.png)

# 步骤 4:设置您的测试

在 app 的根目录下，创建一个名为`tests`的文件夹，在文件夹中添加一个测试文件。我呢，既用 [**柴**](http://chaijs.com/) (断言库)，又用 [**摩卡**](https://mochajs.org/) (JS 框架)。如果您愿意，可以使用其他断言库，如 Jasmine 等。

```
npm install mocha chai --save-dev
```

向 package.json 添加一个脚本，这样下次就可以简单地运行`npm test`了。

```
"test": ./node_modules/.bin/_mocha --recursive ./tests/*.test.js
```

在`tests`中添加一个名为`me.test.js`的文件

```
describe(‘me’, () => { 
  it(‘is awesome’, () => {
   expect(...).to.be.... }) 
});
```

正如我前面提到的，我们不会深入讨论如何编写测试。

# 步骤 5:设置您的 CI 环境(就像最后！)

要在 Gitlab 上设置 CI 环境，在您的存储库的根目录中添加一个`.gitlab-ci.yml`。

该文件包含如何构建项目的定义，Gitlab Runner(您将在稍后设置)将在您的存储库中搜索该文件，并在 Runner 的环境中执行它。

这就是我如何设置我的`.gitlab-ci.yml`。您可能希望根据您的项目进行调整。但是当然，在你把你的项目推送到 Gitlab 之前，最好先通过 [YAML 验证器](http://www.yamllint.com/)来运行它，以确保它是有效的，否则，它会导致错误。我将解释我的 YAML 文件的各个组成部分。

```
image: node:6.10.3 stages: - ver - init - tests - deploy ver: stage: ver script: - node --version - whoamiinit: stage: init script: - npm cache clean - rm -rf node-modules - npm installrun_tests: stage: tests script: - npm test deploy_staging: stage: deploy script: - git remote add heroku https://heroku:$HEROKU_API_KEY@git.heroku.com/text-giving-staging.git - git push heroku master - echo "Deployed to staging server" environment: name: staging url: https://text-giving-staging.herokuapp.com/ only: - master deploy_production: stage: deploy script: - git remote add heroku https://heroku:$HEROKU_API_KEY@git.heroku.com/text-giving.git - git push heroku master - echo "Deployed to production server" environment: name: production url: https://text-giving.herokuapp.com/ when: manual only: - master
```

**image** :允许您指定在构建期间想要使用的 NodeJS 的某个版本

**阶段**:定义构建阶段。在这种情况下，我们有`ver`、`init`、`tests`和`deploy`，但是您可以将阶段的名称更改为您认为适合您项目的名称。

**stage ver:** `node —-version`允许我们检查正在使用的 NodeJS 的版本，并且`whoami`显示用户是否有权限，这使得在出现问题时更容易调试。

**stage init:** 我们清理 npm 缓存，移除节点模块，安装它，然后运行我们之前编写的测试。

**阶段测试:**我们想要运行测试以确保所有的测试都通过。

**stage deploy** :我们将使用 Heroku API 密钥向存储库添加一个远程。我们可以从 Heroku 的**账户设置**页面中检索 Heroku API 密钥。

![](img/9c2413a3cc4366925efc90405da44014.png)

然后，进入 Gitlab 设置> CI/CD 管道>秘密变量，添加 HEROKU_API_KEY 作为秘密变量。

![](img/37cd1f0d7a1270355f2bcde4074469f9.png)![](img/e960b5e0564642ac2ac5d079d8ac158c.png)

然后，我们可以在我们的 YAML 文件中将 Heroku API 键称为`$HEROKU_API_KEY`。

请注意，我们有两个部署阶段—一个用于**准备**，一个用于**生产**。我们还通过在 YAML 文件中添加`when: manual`将它设置为手动推入生产。因此，如果一切顺利，它会将`master`分支自动推送到`staging`，如果一切顺利，我们需要手动推送到`production`。

# 步骤 6:安装 Gitlab 运行程序

如前所述，在 GitLab 中，跑步者执行您在`.gitlab-ci.yml`中定义的任务。因为我用的是 MacOS，所以我会按照这里的说明:[https://docs.gitlab.com/runner/install/osx.html](https://docs.gitlab.com/runner/install/osx.html)。这应该很容易理解。

# **第七步:注册 Gitlab Runner**

接下来，按照这里的说明注册 git lab Runner:[https://docs.gitlab.com/runner/register/index.html](https://docs.gitlab.com/runner/register/index.html)。同样，它应该很容易理解。对于 runner executor，我选择在最容易配置的 **shell** 上运行它(如果您愿意，可以选择其他选项)。注册后，使用以下命令安装并启动服务:

```
gitlab-runner install
gitlab-runner start
```

您可以使用以下命令检查运行程序是否正在运行:

```
gitlab-runner status
gitlab-runner: Service is running!
```

现在这还不算太糟，不是吗？

在本文的第 2 部分(请继续关注)中，我将向您展示如何在 Telegram 上为您的 CI/CD 管道设置一个通知程序，然后将存储库推送到 Gitlab，并观察 CI/CD 管道在您配置的不同阶段中运行，最终手动将其推向生产。

如果你对我有任何反馈，请随时通过**hello@imstacy.com**联系我，或者对本文发表评论。

或者，如果这篇文章对你有所帮助，请拿出你的爱，点击小💚请在[https://github.com/stacygohyunsi](https://github.com/stacygohyunsi/)上关注我，了解我接下来要做的事情！