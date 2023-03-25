# 在基于虚拟机的环境中安装 Apache Hadoop

> 原文：<https://medium.com/hackernoon/installing-apache-hadoop-in-virtual-machine-based-environments-c6441a5dfb52>

![](img/270b66b8ed59a82ff3312541458d8680.png)

[大数据](https://hackernoon.com/tagged/big-data)不仅仅指机器学习、人工智能、统计学和[编程](https://hackernoon.com/tagged/programming)。它还指使事情发生的计算基础设施和软件框架。无论任何技术的积极和消极方面如何，Apache Hadoop 都是大数据分析的艺术状态。它的几个特点之一是依靠现成的计算机，连接在一个网络环境中，配备非常少的硬件。对于 Hadoop 来说，不可靠、崩溃、重启和故障并不是真正的问题，前提是大量节点已经启动并运行，并且管理员已经保证通过复制实现大量冗余。

# 安装 Apache Hadoop。

如果想法是最早启动 Apache Hadoop，下载二进制会节省很多时间。一个好的开始是[官网](https://hadoop.apache.org/#Download+Hadoop)。将二进制文件解压缩到选择的目录中。我们正在 */opt/hadoop* 中安装 Hadoop

```
wget http://apache.belnet.be/hadoop/core/hadoop-2.6.0/hadoop-2.6.0.tar.gz sudo tar xvf hadoop-2.6.0.tar.gz -C /opt/ cd /opt sudo mv hadoop-2.6.0/ hadoop
```

允许您的用户(在本例中为 *frag* )访问 *hadoop* 目录

```
sudo chown -R frag hadoop
```

让你的 Hadoop 用户(如果有的话)知道东西在哪里。在这种情况下, *java* 已经安装在 JAVA_HOME env 变量指示的路径下。在将更改应用到~/之前，请检查您的。bashrc

```
export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64 export HADOOP_HOME=/usr/local/hadoop export PATH=$HADOOP_HOME/bin:$PATH
```

我在启动 Hadoop 时发现的一个问题是关于 JAVA_HOME 的。显然，还需要将 JAVA_HOME 添加到/opt/Hadoop/etc/Hadoop/Hadoop-env . sh，如下所示

```
# The java implementation to use. #export JAVA_HOME=${JAVA_HOME} export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
```

此时，从控制台启动的命令应该返回一些子命令的运行和使用说明。如果失败了，不要继续，因为已经有问题了。

# 配置 Hadoop

现在，我们配置 Hadoop 将存储其数据文件、网络设置和其他一些配置选项的目录，这些配置选项在以后可能会有用。在我的例子中， *frag* 是用户，我决定将本地文件系统存储在路径 */usr/local/hadoop/tmp* 中，因此，我创建了这个目录

```
sudo mkdir -p /usr/local/hadoop/tmp
```

并像以前一样设置权限

```
sudo chown frag /usr/local/hadoop/tmp/
```

这样做与您的用户名和所有其他用户谁被允许访问也限制位的安全策略

```
sudo chmod 750 /usr/local/hadoop/tmp/
```

现在最重要的部分来了，配置 Hadoop 平台的一些基础文件。

核心网站. xml

```
cd /opt/hadoop/etc/hadoop/ 
sudo vi core-site.xml 
sudo cp mapred-site.xml.template mapred-site.xml <configuration> 
<property> <name>hadoop.tmp.dir</name> <value>/usr/local/hadoop/tmp</value> <description>A base for other temporary directories.</description> </property> 
<property> <name>fs.default.name</name> <value>hdfs://localhost:9000</value> 
<description>The name of the default file system. A URI whose scheme and authority determine the FileSystem implementation. The uri's scheme determines the config property (fs.SCHEME.impl) naming the FileSystem implementation class. The uri's authority is used to determine the host, port, etc. for a filesystem.
</description> 
</property> 
</configuration>
```

mapred-site.xml

```
sudo vi mapred-site.xml <configuration> <property> 
<name>mapred.job.tracker</name> <value>localhost:9001</value> <description>The host and port that the MapReduce job tracker runs at. If "local", then jobs are run in-process as a single map and reduce task. 
</description> </property> </configuration>
```

hdfs-site.xml

```
sudo vi hdfs-site.xml <configuration> <property> <name>dfs.replication</name> <value>2</value> <description>Default block replication. The actual number of replications can be specified when the file is created. The default is used if replication is not specified in create time. </description> </property> </configuration>
```

复制值决定了 RDD 数据块应该在节点间复制的次数。对于测试环境来说，2 已经足够了。但是，在生产中，应该考虑更高的复制值，以提高整个集群的可靠性。

## 格式化 HDFS 文件系统

通常，新的文件系统必须格式化。这也适用于 HDFS。下面的命令完成了这项工作。

```
$ /opt/hadoop/bin/hadoop namenode -format
```

这不应该在群集运行时进行，否则所有数据都会被突然删除。该开动机器了。从主节点

```
/opt/hadoop/sbin/start-dfs.sh
```

此时，一些 *ssh* 连接由主节点向从节点执行(这些是伪节点，因为它们运行在同一台机器上，只是为了本教程的目的)。 *ssh* 将要求对 DataNode、SecondaryNameNode 和 NameNode 进行三次身份验证(顺序相反)。对于生产系统来说，这并不理想。因此，应该为将要使用 Hadoop 的用户提供一个 *ssh 密钥*。为了配置 ssh 不要每次都询问密码，在属于集群的所有机器中输入这个

```
cat .ssh/id_rsa.pub | ssh b@B 'cat >> .ssh/authorized_keys2' chmod 700 .ssh chmod 640 .ssh/authorized_keys2
```

## 摆弄文件系统

为了在 Hadoop 中创建虚拟文件系统，应该创建一个类似于传统目录树的目录树。在本例中，我将创建 *hadoop/data* 路径。

```
$hadoop fs -mkdir /hadoop $hadoop fs -mkdir /hadoop/data
```

事实上，hadoop 根目录列表返回了我所期望的内容。

```
$ hadoop fs -ls /hadoop Found 1 items drwxr-xr-x - frag supergroup 0 2015-04-20 17:22 /hadoop/data
```

好像管用！为了使本地文件在 Hadoop 基础架构中可见，必须将它从本地文件系统(或任何位置)复制到 HDFS。在这种情况下， *localfile.txt* 被复制到 HDFS 的根目录下，作为 *file.txt* 。

```
$ bin/hadoop dfs -copyFromLocal ~/localfile.txt  /file.txt
```

## 检查

java 实用程序 *jps* 给出了正在运行的 Hadoop 进程的打印结果。它应该会返回类似这样的结果

```
$ jps 28130 DataNode 28323 SecondaryNameNode 27968 NameNode 28471 Jps
```

好像管用！

## 监视

一旦 Hadoop 启动并运行，就可以将浏览器指向主节点(在我的例子中是*vlab 1*)[*http://vlab 1:50070*](http://vlab1:50070)，并从主节点 *vlab1* 监控所有资源和基础架构的完整状态。在[*http://vlab 1:8042*](http://vlab1:8042)也有一个 HDFS NameNode web 接口，其中 *vlab1* 也是主节点的主机名。

停止 Hadoop

为了停止集群

```
/opt/hadoop/sbin/stop-dfs.sh
```

主节点从每个从节点中停止 Hadoop。但是不需要 ssh 密码。

## 虚拟化技术

在家里，我没有成千上万的物理机器来测试这一点。不过，我确实有虚拟机，想要多少就有多少(直到计算机内存完全耗尽)。在 VirtualBox 中设置了一个 Ubuntu 虚拟机，并按照上面的说明安装和配置了所有的东西之后，我只需克隆它就可以在几分钟内获得三个正在运行的虚拟机。该是我享受我的虚拟 Hadoop 集群的时候了。

Hadooping 快乐！

*原载于 2015 年 9 月 7 日*[*【worldofpiggy.com】*](http://worldofpiggy.com/2015/09/07/installing-apache-hadoop-in-virtual-machine-based-environments/)*。*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)