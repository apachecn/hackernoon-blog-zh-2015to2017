# 弹性豆茎和 Django 的 5 个问题

> 原文：<https://medium.com/hackernoon/5-gotchas-with-elastic-beanstalk-and-django-177896723d01>

![](img/70cd5bb800272ea13370f6622b7d5c55.png)

5 Gotchas with Elastic Beanstalk and Django

在[哈希丁](https://hashedin.com/)，我们通常在 AWS Elastic Beanstalk 上部署基于 Django 的应用程序。虽然 EB 很棒，但它也有一些边缘案例。如果您正在部署一个 Django 应用程序，下面是您应该知道的一些事情。

旁白:如果你正在开始一个新项目，这个项目是为 elastic beanstalk 设计的， [Django 项目模板](https://github.com/hashedin/django-project-template)可以简化配置。

# 陷阱#1:自动扩展组运行状况检查并不像您想象的那样工作

Elastic Beanstalk 允许您配置健康检查 URL。弹性负载平衡器使用此健康检查 URL 来确定实例是否健康。但是，[自动秤组不使用这种健康检查](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environmentconfig-autoscaling-healthchecktype.html)。

因此，如果实例健康检查由于某种原因失败，弹性负载平衡器会将其标记为不健康，并将其从负载平衡器中删除。但是，自动缩放组仍然认为实例是健康的，并且不会重新启动实例。

Elastic Beanstalk 保持这种方式，让您有机会通过 ssh 进入机器，找出问题所在。如果自动缩放组立即终止机器，您将没有该选项。

修复方法是将自动扩展组配置为使用基于弹性负载平衡器的运行状况检查。将以下内容添加到。ebextensions 将解决这个问题。

```
Resources:
   AWSEBAutoScalingGroup:
     Type: "AWS::AutoScaling::AutoScalingGroup"
     Properties:
       HealthCheckType: "ELB"
       HealthCheckGracePeriod: "600"
```

学分:

1.  [EB 部署人员的技巧和诀窍](https://github.com/ThoughtWorksStudios/eb_deployer/wiki/Elastic-Beanstalk-Tips-and-Tricks)
2.  [配置多码头集装箱弹性豆茎](http://www.onegeek.com.au/articles/lessons-learned-configuring-a-ha-multi-docker-container-elastic-beanstalk)

# 问题 2:定制日志不能与弹性 Beanstalk 一起工作

默认情况下，wsgi 帐户对当前工作目录没有写权限，因此您的日志文件将不起作用。[根据 Beanstalk 的文档](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.logging.html)，窍门是将日志文件写在`/opt/python/log directory`下。

然而，这并不总是像预期的那样工作。当 django 在该目录中创建日志文件时，日志文件归 root 所有，因此 django 不能写入该文件。

诀窍是运行一个小脚本作为。ebextensions 来修复这个问题。在`.ebextensions/logging.config`中增加以下内容:

```
commands:
01_change_permissions:
command: chmod g+s /opt/python/log
02_change_owner:
command: chown root:wsgi /opt/python/log
```

通过这一更改，您现在可以将自定义日志文件写入该目录。另外，当您使用 elastic beanstalk 控制台或 eb 工具获取日志时，您的自定义日志文件也将被下载。

# 问题 3:弹性负载平衡器运行状况检查不设置主机头

Django 的`ALLOWED_HOSTS`设置要求您将允许的主机名列入白名单。问题是，弹性负载平衡器健康检查在发出请求时不会设置主机名。相反，它直接连接到实例的私有 ip 地址，因此主机头是实例的私有 ip 地址。

这个问题有几个不太理想的解决方案

**终止 apache** 上的健康检查—例如，通过将健康检查 url 设置为 apache 提供的静态文件。这种方法的问题是，如果 Django 不工作，health check 不会报告失败

**使用基于 TCP/IP 的健康检查** —这只是检查端口 80 是否打开。这有同样的问题——如果 Django 不工作，健康检查不会报告失败

**Set ALLOWED _ HOSTS =[' *]**—这将完全禁用主机检查，从而引发安全问题。此外，如果您搞乱了 DNS，您可以非常容易地发送 QA 流量到生产。

稍微好一点的解决方案是检测服务器的内部 ip 地址，并在启动时将其添加到 ALLOWED_HOSTS 中。不过，可靠地做到这一点有点复杂。假设您的 EB 环境是 linux，下面是一个方便的脚本:

```
def is_ec2_linux():
    """Detect if we are running on an EC2 Linux Instance
       See [http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/identify_ec2_instances.html](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/identify_ec2_instances.html)
    """
    if os.path.isfile("/sys/hypervisor/uuid"):
        with open("/sys/hypervisor/uuid") as f:
            uuid = f.read()
            return uuid.startswith("ec2")
    return Falsedef get_linux_ec2_private_ip():
    """Get the private IP Address of the machine if running on an EC2 linux server"""
    import urllib2
    if not is_ec2_linux():
        return None
    try:
        response = urllib2.urlopen('[http://169.254.169.254/latest/meta-data/local-ipv4'](http://169.254.169.254/latest/meta-data/local-ipv4'))
        return response.read()
    except:
        return None
    finally:
        if response:
    response.close()# ElasticBeanstalk healthcheck sends requests with host header = internal ip
# So we detect if we are in elastic beanstalk, 
# and add the instances private ip address
private_ip = get_linux_ec2_private_ip()
if private_ip:
    ALLOWED_HOSTS.append(private_ip)
```

根据您的情况，这可能比您所关心的工作更多—在这种情况下，您可以简单地将 ALLOWED_HOSTS 设置为['*']。

# 问题 4:EB 上的 Apache 服务器没有针对性能进行配置

出于性能原因，您希望压缩文本文件，通常使用 gzip。在内部，elastic beanstalk for python 使用 Apache web 服务器，但是它没有配置为 gzip 内容。

这很容易通过[添加另一个配置文件](http://www.tonmoygoswami.com/2013/05/how-to-enable-gzip-on-amazon-elastic.html)来解决。

此外，如果您正在对静态文件进行版本控制，您可能希望设置强缓存头。默认情况下，Apache 不设置这些头。[该配置文件将缓存控制头设置为任何嵌入了版本号的静态文件](https://github.com/hashedin/django-project-template/blob/master/%7B%7Bcookiecutter.github_repository_name%7D%7D/.ebextensions/enable_cache_headers.conf)。

# 问题 5:当您终止环境时，Elastic Beanstalk 将删除 RDS 数据库

因此，请始终独立创建 RDS 实例。将数据库参数设置为环境变量。在 django 设置中，使用 [dj_database_url](https://github.com/kennethreitz/dj-database-url) 从环境变量中解析数据库凭证。

*原载于 2017 年 7 月 25 日*[*【hashedin.com*](https://hashedin.com/blog/5-gotchas-with-elastic-beanstalk-and-django/)*。*