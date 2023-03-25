# 使用私有 github repo 作为 helm chart repo (https 访问)

> 原文：<https://medium.com/hackernoon/using-a-private-github-repo-as-helm-chart-repo-https-access-95629b2af27c>

Helm 是 Kubernetes 的一个很好的包管理器。如果你正在使用 k8s，特别是在复杂的设置中，helm 可以帮助你创建一个良好的发布过程。

在复杂的设置中，有时你需要一个私有的 helm 库来存放你的包。

这就是你如何使用 github 回购，公共或私人，作为掌舵回购。

# 在 github 中创建舵图报告

这很容易。创建一个 repo，要添加包，请执行以下命令

```
$ helm package $YOUR_CHART_PATH/ # to build the tgz file and copy it here
$ helm repo index . # create or update the index.yaml for repo
$ git add .
$ git commit -m 'New chart version'
$ git push
```

# 访问您的回购

目前 helm 只理解用于存储库服务器的 http(s)。以下技巧展示了如何在不使用 gh-pages 的情况下，在私有或公共 github repo 中实现这一点。

你可能知道 github 有一个原始视图。因此，只需使用以下代码:

```
$ helm repo add sample '[https://raw.githubusercontent.com/kmzfs/helm-repo-in-github/master/'](https://raw.githubusercontent.com/kmzfs/helm-repo-in-github/master/')
$ helm repo update
$ helm search aerospike
NAME             VERSION DESCRIPTION
sample/aerospike 0.1.2   A Helm chart for Aerospike in Kubernetes
```

如果你想保持回购的私密性，你可以创建一个“个人访问令牌”，并像这样使用它:

```
$ helm repo add sample '[https://MY_PRIVATE_TOKEN@raw.githubusercontent.com/kmzfs/helm-repo-in-github/master/'](https://MY_PRIVATE_TOKEN@raw.githubusercontent.com/kmzfs/helm-repo-in-github/master/')
```

只是要小心谁在创建令牌以及它的访问级别是什么。

 [## kmzfs/helm-repo-in-github

### 这是一个如何在 github 中设置 helm repo 的例子

github.com](https://github.com/kmzfs/helm-repo-in-github)