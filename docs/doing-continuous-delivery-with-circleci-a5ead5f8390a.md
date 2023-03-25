# 用 CircleCI 连续交货

> 原文：<https://medium.com/hackernoon/doing-continuous-delivery-with-circleci-a5ead5f8390a>

在本帖中，我们解释了如何将 CI/CD 环境 [CircleCI](https://circleci.com/) 与 Mesosphere 的数据中心操作系统(DCOS)一起使用。我们使用 DCOS 作为我们的测试环境，它可以无缝地工作。我们的目标是在 DCOS 部署我们的容器作为马拉松应用程序，我们使用 CircleCI 进行持续集成。CircleCI 创建 Docker 映像并将其推入私有的 [Docker 注册表](https://mesosphere.com/blog/2015/10/14/docker-registries-the-good-the-bad-the-ugly/)。

我们正在做的手动任务之一是在代码更改后手动更新马拉松应用程序。然而，事实证明我们也可以通过 CircleCI 来自动化这个过程。

因此，首先，我们假设您正在 Amazon Web Services 上运行您的 DCOS，因为我们将使用 Amazon S3 来存储我们的 JSON 和 shell 脚本。反过来，CircleCI 将在运行时下载并执行它们。

# 设置

第一步是在`circleci.yml`文件的 dependencies 部分添加以下几行:

依赖关系:

```
Pre:
    - pip install awscli
    - aws s3 cp s3://aaa-tools/scripts/marathon.json /home/ubuntu/dcos/;
    - aws s3 cp s3://aaa-tools/scripts/put.sh /home/ubuntu/dcos/; chmod a+x /home/ubuntu/dcos/put.sh
    - mkdir -p /home/ubuntu/dcos
```

确保用您自己的 S3 存储桶名称和文件名替换 S3 存储桶路径。我们使用以下约定:

*   `aaa-tools`是 S3 斗
*   `marathon.json`是马拉松 app 规范文件
*   `put.sh`是通过 HTTP API 更新 Marathon 的 shell 脚本

我们的`marathon.json`文件如下:

```
{
    "id": "aaa-notifier",
    "cpus": 0.4,
    "mem": 256,
    "uris": ["hdfs://namenode1.hdfs.mesos:50071/artifact_store/.dockercfg"],
    "ports": [],
    "instances": 1,
    "container": {
        "type": "DOCKER",
        "docker": {
            "image": "docker-registry-000-staging:443/aaa/aaa-notifier:latest",
            "network": "BRIDGE",
            "portMappings": [{
                "containerPort": 0,
                "hostPort": 0,
                "servicePort": 0,
                "protocol": "tcp"
            }],
            "forcePullImage": true
        }
    }
}
```

…和`put.sh`将包含:

```
#!/usr/bin/env sh
MARATHON=http://master-001-dcos4.aaa.net:8080
MYAPP=aaa-notifier

cd /home/ubuntu/dcos
for file in *.json
do
  echo "Installing $file..."
  curl -X PUT "$MARATHON/v2/apps/$MYAPP" -d @"$file" -H "Content-type: application/json"
  echo ""
```

现在，将上面的两个文件(`marathon.json`和`put.sh`)复制到 S3 桶中。然后，在 docker 部分的`circle.yml`文件的最后添加以下代码片段:

- bash /home/ubuntu/dcos/post.sh

CircleCI 触发构建后，它会自动更新马拉松应用程序，您就大功告成了！

## **如何验证？**

我们可以通过使用版本号来验证；构建成功后，我们将在 CircleCI 中得到以下消息:

```
{"version":"2015-11-04T05:56:03.996Z","deploymentId":"9772b7a4-8870-479d-
```

然后，在马拉松用户界面中检查应用程序版本:

2015 年 11 月 04 日 05 时 56 分 03.996 秒

这应该与你从 CircleCI 得到的一致。

在本文中，我将带您了解如何使用 DCOS 设置 CircleCI，以及如何自动化整个 CI/CD 流程。我希望它对你有用，如果你有任何建议或问题，请在下面留下评论。

注:-原帖在此。并且可能是太旧的部署方式。

[https://meso sphere . com/blog/continuous-delivery-with-circle ci/](https://mesosphere.com/blog/continuous-delivery-with-circleci/)