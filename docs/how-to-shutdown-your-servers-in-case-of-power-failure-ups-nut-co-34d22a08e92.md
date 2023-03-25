# 如何在停电时关闭服务器——UPS、NUT & Co

> 原文：<https://medium.com/hackernoon/how-to-shutdown-your-servers-in-case-of-power-failure-ups-nut-co-34d22a08e92>

![](img/a16f40a4231187bc77b2278b756c35cd.png)

我们学校总是停电。有时它们只持续几秒钟，有时持续几个小时。为了确保我们的 IT 基础设施不会受到影响，我们为我们的服务器和网络设备安装了 UPS。这些有助于弥补短时间停电(长达 1 小时)或电流波动。对于任何长时间的停电，当 UPS 达到临界电池电量时，我们会自动关闭我们的服务器。为此，我们使用 NUT(网络 UPS 工具)。

我们的设置由以下组件组成:

*   [Raspberry Pi](https://amzn.to/2HOnPde) 作为 UPS 服务器或主服务器
*   [Liebert GXT4 UPS](https://amzn.to/2MMgQjR)
*   作为客户端或从属服务器

# 在树莓皮上设置螺母

对于树莓派，我们使用标准的 [Raspian Stretch Lite 图像](https://www.raspberrypi.org/downloads/raspbian/)。注意，从 2016 年开始，SSH 就没有默认启用了。这里的记录了重新激活 SSH 的不同方式[。重新激活 SSH 后，您现在可以登录树莓 Pi(树莓 Pi 的 IP 可以通过 *avahi-browse -ar* 从同一网络中的计算机上找到，或者您可以连接显示器和键盘)。我们以后会需要这个 IP，所以给树莓派一个静态 IP 是有意义的。](https://www.raspberrypi.org/documentation/remote-access/ssh/)

接下来，我们安装 NUT 服务器

```
sudo apt-get install nut-client nut-server usbutils
```

如果 UPS 通过 USB 连接(如我们的情况)，您可以使用 *lsusb* 检查它是否被检测到。这是 Raspberry Pi 的输出结果:

```
$ lsusb
Bus 001 Device 004: ID 10af: 0000 Liebert Corp. UPS
Bus 001 Device 003: ID 0424: ec00 Standard Microsystems Corp. SMSC9512/9514 Fast Ethernet Adapter
Bus 001 Device 002: ID 0424:9514 Standard Microsystems Corp. 
Bus 001 Device 001: ID 1d6b: 0002 Linux Foundation 2.0 root hub
```

在第一行中，您可以看到我们的 Liebert UPS 得到了认可。

现在我们必须在文件 */etc/nut/ups.conf* 中配置我们的 UPS。对于我们的 UPS，条目如下所示:

```
[gxt4]
  driver = usbhid-ups
  port = car
  productid = 0000
  desc = “Emerson Liebert GXT 4”
```

在网络 UPS 工具网站上，您可以找到所有受支持的 UPS 的列表，以及哪个驱动程序工作得最好。

接下来，我们要测试设置是否正确，UPS 守护程序是否可以与 UPS 通信。为此，我们从 **upsd** 开始

```
sudo upsdrvctl start
```

成功的输出应该如下所示:

```
Network UPS Tools — UPS driver controller 2.7.2
Network UPS Tools — Generic HID driver 0.38 (2.7.2)
USB communication driver 0.32
Using subdriver: Belkin HID 0.16
```

NUT 包含一个小程序，用于测试与 UPS 的通信。称为 **upsc** ，输出所有可读的 UPS 参数。

```
$ upsc gxt4
Init SSL without certificate database
battery.charge: 100
battery.charge. low: 20
battery.charge. warning: 0
battery.type: PbAc
battery.voltage: 0.0
battery.voltage. nominal: 0.0
device.mfr: Emerson Network Power
device.model: Liebert GXT4
device.serial: 1607000060AFC23
device.type: ups
driver.name: usbhid-ups
driver.parameter. pollfreq: 30
driver.parameter. pollinterval: 2
driver.parameter. port: auto
driver.parameter. productid: 0000
driver.version: 2.7.2
driver.version. data: Belkin HID 0.16
driver.version. internal: 0.38
ups.mfr: Emerson Network Power
ups.model: Liebert GXT4
ups.productid: 0000
ups.serial: 1607000060AFC23
ups.status: OL CHRG
ups.vendorid: 10af
```

现在我们的 UPS 安装成功了。接下来，我们设置了 NUT 服务器、UPS monitor **upsmon** 和另外两个用于主服务器和从服务器的用户。

# 配置 NUT 服务器

在 */etc/nut/nut/nut.conf* 中我们指定树莓派是我们的主人:

```
MODE=netserver
```

UPS 守护程序( **upsd** )正在 NUT 服务器上运行。它负责与 UPS 的(物理)连接。各个客户端/从机使用 **upsmon** 来访问 UPS 的状态。通过这种方式，多个客户端可以始终知道 UPS 的状态，并且在必要时，当 UPS 提供“电池电量低”状态时启动关机。

为了让我们的 NUT 服务器可以从网络上访问，必须将下面几行添加到 */etc/nut/upsd.conf* (请调整 IP):

```
LISTEN 127.0.0.0.1
LISTEN 192.168.1.2 #IP of the Raspberry Pis
```

接下来，我们定义客户端可以用来登录到主服务器的用户(在文件 */etc/nut/upsd.user* 中):

```
[upsmaster]
  password = secret
  upsmon master[upremote]
  password = secret
  upsmon slave
```

为了让 **upsmon** 监控 UPS，我们现在需要在 */etc/nut/upsmon.conf* 中添加一个最终条目:

```
MONITOR gxt4@localhost 1 upsmaster secret master
```

为了知道所有服务是否都正常运行，您可以使用以下命令检查 Raspberry Pi 上的服务器和本地客户端的状态(因为当没有更多电源时它应该关闭):

```
$ sudo systemctl status nut-server
$ sudo systemctl status user-client
```

现在我们的主机已经设置好了，可以配置客户机或从机了。

# 设置客户端/从属设备(例如服务器、其他计算机)

首先，必须在客户机上安装必要的软件包:

```
sudo apt-get install nut-client
```

这次我们只需要编辑两个文件。在 */etc/nut/nut/nut.conf* 中我们设置了模式:

```
MODE=netclient
```

然后我们需要告诉 **upsmon** 要监控哪个 UPS(在 */etc/nut/upsmon.conf* 中):

```
MONITOR gxt4@192.168.1.2 upsremote secret slave
```

必须调整 IP 和密码。还请注意结尾的奴隶！最后，重新启动相应的服务，并检查一切都在运行:

```
$ sudo systemctl restart user-client
$ sudo systemctl status user-client
```

恭喜你！现在一切都应该设置好了，但是如何测试它是否真的工作呢？

# 测试关机

以下命令触发关机信号(就像断电时一样):

```
$ sudo upsmon -c fsd
```

# 结论

NUT 是另一个伟大的开源项目，它允许非常灵活的定制。[这个 PDF](http://rogerprice.org/NUT/ConfigExamples.A5.pdf) 包含了更多(复杂)的应用场景。当我写这篇文章的时候，我们又停电了，我们所有的服务器都关闭了:)

这篇文章最初发表在 openschoolsolutions.org 的[上。*在推特上关注*](https://openschoolsolutions.org/shutdown-servers-case-power-failure%e2%80%8a-%e2%80%8aups-nut-co/)[*@ OpenSchoolZ*](https://twitter.com/OpenSchoolZ)*。*