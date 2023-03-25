# 如何做好旁站项目

> 原文：<https://medium.com/hackernoon/how-to-build-a-good-side-project-5ae8ecf3a6d8>

作者: [**Git 提交指南**](https://medium.com/u/85a8e65ba563#type) 。

我们不想在每次发布后都手动部署我们的应用程序。我们必须自动化这项任务，就像我们为每个拉请求自动运行测试一样。由于这是一个附带项目，你可能不想浪费时间写测试，但如果你想在招聘人员面前为你的项目感到自豪，你应该这样做。对于 Bitbucket，我们使用 [**CircleCI**](https://circleci.com/) (这是免费的，否则你可以为 GitHub 使用 [Travis](https://travis-ci.com/) )。这是一个在线服务，它将连接到您的存储库并自动运行配置文件中定义的任务。

例如:

```
*# Javascript Node CircleCI 2.0 configuration file*
*#*
*# Check* [*https://circleci.com/docs/2.0/language-javascript/*](https://circleci.com/docs/2.0/language-javascript/) *for more details*
*#*
version: 2
jobs:
  build:
    docker:
      *# specify the version you desire here*
      - image: circleci/node:7.10 *# Specify service dependencies here if necessary*
      *# CircleCI maintains a library of pre-built images*
      *# documented at* [*https://circleci.com/docs/2.0/circleci-images/*](https://circleci.com/docs/2.0/circleci-images/)
      *# - image: circleci/mongo:3.4.4* working_directory: ~/freatle branches:
      only:
        - master steps:
      - checkout *# Download and cache dependencies*
      - restore_cache:
          keys:
          - v1-dependencies-{{ checksum "package.json" }}
          *# fallback to using the latest cache if no exact match is found*
          - v1-dependencies- - run: sudo npm install -g surge
      - run: npm install - save_cache:
          paths:
            - node_modules
          key: v1-dependencies-{{ checksum "package.json" }} - run: npm run build
      - run: surge ./dist --domain [https://freatle.surge.sh](https://freatle.surge.sh) *# run tests!*
      - run: npm test
```

多亏了这个文件，每次我们在 master 上发出一个 pull-request，CircleCI 就会运行测试，当一个 pull-request 被合并时，它就会将我们的应用程序部署到 Surge。

# 结论

如果你想快速有效地做一个副业，请遵循以下步骤:

1.  想想谷歌文档和特雷罗上的整个项目
2.  使用 Git 和拉请求工作流
3.  使用 CircleCI 或 Travis 自动化您的部署和测试
4.  选择您最喜欢的前端技术
5.  如果不需要，不要浪费时间构建后端，使用 BaaS(后端即服务)
6.  在像 Surge 或 Now 这样的免费在线解决方案上托管您的前端应用程序

如果您遵循这些要点，您将拥有一个稳定的、可伸缩的前端应用程序，它是免费构建的，并且具有非常快的设置时间(现在我们可以在一天内从头创建一个应用程序)。如果你遵循这一点，你会为拥有一个比你申请的大多数公司都干净的项目而自豪。

我们重复一遍，这是我们的个人意见，你可能认为浪费时间在设置 CircleCI 和为一个附带项目编写测试上是愚蠢的，你可能认为如果你独自工作，实现持续集成是没有用的，但我们不这样认为。我们认为，应该像创建一个完美的应用程序来运行一个初创公司一样创建一个辅助项目，即使今天没有人会看到你的代码，即使今天没有人使用你的应用程序，你也不知道明天会怎么样。