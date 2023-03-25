# 构建 AWS 无服务器应用程序时必须学习的 5 个命令

> 原文：<https://medium.com/hackernoon/5-commands-you-must-learn-when-building-aws-serverless-applications-989a78506cd1>

![](img/e75aee38b6a76f32dfe9a1696ea6a23c.png)

Photo courtesy of [MustangJoe](https://pixabay.com/en/users/MustangJoe-2162920/) on Pixabay

本周是[重塑](https://reinvent.awsevents.com/)，最大的[云计算技术](https://hackernoon.com/tagged/tech)会议。我将在那里了解最新的创新和使用案例。我今年的主要兴趣是围绕一种流行的建筑基础设施风格，这种风格被称为“[无服务器](https://hackernoon.com/tagged/serverless)”。它简单、快速，而且比需要直接管理主机的传统基础架构更便宜。

随着我对这个新模型的使用越来越成熟，我已经学会了如何从命令行控制这些服务。AWS 控制台对于开始使用可能是有用的，但对于日常基础设施管理来说不是一种有效的方式。在下面的博客中，我将分享我学到的五个最强大的命令，每个软件开发人员都应该知道。

# 1-检查用户或角色凭据

与传统的基于网络的安全性相比，无服务器模式依赖于基于凭证的安全性。这意味着学习身份和访问管理(IAM)服务，以及如何检查针对不同实体的策略。这包括您将用于执行 CLI 的凭据。这是开始的最好地方。

```
# check the current aws credentials used, similar to whoami
aws iam get-user# check the user policies that are attached to the user name
aws iam list-attached-user-policies — user-name myawsusername# check the group policies that are attached to the user name
aws iam list-groups-for-user --user-name myawsusername# check for access keys granted to a user name
aws iam list-access-keys --user-name myawsusername# check for roles within an account
aws iam list-roles
```

在 AWS 中构建安全的应用程序只需要授权绝对需要的内容，仅此而已。根据最小特权原则，经常检查已经授予的权限是非常必要的。

# 2-执行 Lambda 函数

让我们从基础开始。自动化测试用例对于构建健壮的应用程序至关重要。每个函数都可以从命令行调用，事件数据从本地文件传入。下面是这样做的步骤。

```
# read request data into a local variable
request=$(<request.json)# invoke the function with the data, and write to local file
aws lambda invoke --function-name myFunction --payload "$request" response.json# read response file into local variable then print to the console
responseOutput=$(<response.json)
echo $responseOutput
```

一旦你有了这个模式，自动化测试就被激活了。这为运行许多测试场景的验证奠定了基础。该脚本的几行附加代码可以在输出文件和预期结果之间进行比较。像所有的测试自动化一样，这需要前期投资。回报是巨大的，在构建健壮的应用程序时，这是必须的。

# 3—在本地主机和 S3 之间移动文件

存储用于很多事情，包括媒体文件、二进制文件、测试数据等。简单存储服务(S3)非常耐用，并且成本非常低，因为定价只要求为消耗的内容付费。S3 是基于数据块的存储(如连接到 EC2 主机的 EBS 卷)的绝佳替代方案。

为了有效地使用 S3，需要把它想象成一个弹性驱动器——只是一个从未安装到机器上的驱动器。管理该服务的命令是健壮的，并且模拟了您已经熟悉的大多数相同的 Linux 存储命令。

```
# copy a local file to a s3 bucket and folder
aws s3 cp foo.json s3://mybucket/myfolder# retrieve a local file from a s3 bucket
aws s3 cp s3://mybucket/myfolder foo.json# list all buckets within an account
aws s3 ls# list all of the objects and folder within a bucket
aws s3 ls s3://mybucket# test removal (aka dry run) of an object from a s3 bucket
aws s3 rm s3://mybucket/myfolder/foo.json --dryrun# remove an object from a S3 bucket
aws s3 rm s3://mybucket/myfolder/foo.json
```

[这里的](http://docs.aws.amazon.com/cli/latest/reference/index.html#cli-aws)是继续学习的官方参考指南，包括可以使用查询过滤对象的可选命令。

# 4-更新现有的 Lambda 函数

既然您可以从命令行执行 Lambda 函数，并且知道如何移动数据，接下来就是自动化部署管道。这可以通过创建一个构建包来完成，在 S3 进行(使用上面的#3)，然后将它部署到运行时环境中。下面是这样做所需的命令。

```
# first create a zip file containing all elements in the package
# note directory/ is what includes libraries in the zip
zip -r myfunction.zip lambda.js directory/ package.json# then copy the zip file to S3
aws s3 cp myFunction.zip s3://mybucket/myfolder# finally deploy the package to the runtime environment
aws lambda update-function-code --function-name myFunction --s3-bucket mybucket --s3-key myfunction.zip
```

一旦您准备好了，添加一些自动化测试用例来验证部署是否成功。这是自动化部署管道的基础。

# 5-创建新的 Lambda 函数

当服务是细粒度的时，基于功能的架构工作得最好。供应过程的自动化实现了创建新功能的一致性，因为该功能的许多属性是相同的(即，语言、执行角色)。

```
# first create a zip file containing all elements in the package
# note directory/ is what includes libraries in the zip
zip -r myfunction.zip lambda.js directory/ package.json# create a new function based on the parameters and zip package
aws lambda create-function --function-name newFunction --runtime nodejs6.10 --role arn:aws:iam::1234567890:role/lex_execution --handler lambda --zip-file "fileb://myfunction.zip"# note: runtime options include nodejs6.10, java8, python2.7
```

这个命令有[许多不同的可选参数](http://docs.aws.amazon.com/cli/latest/reference/lambda/create-function.html)，也有使用[云形成模板](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/quickref-lambda.html)做同样工作的选项。学习这个命令可以使创建新函数变得像更新现有函数一样容易，甚至可能使现有函数过载。

# 结论

使用这些命令简化了无服务器架构的管理，使您可以专注于编写代码，而不是管理基础架构！