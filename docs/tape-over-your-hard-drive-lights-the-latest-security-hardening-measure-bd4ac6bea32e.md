# 硬盘灯上的胶带:最新的安全加固措施

> 原文：<https://medium.com/hackernoon/tape-over-your-hard-drive-lights-the-latest-security-hardening-measure-bd4ac6bea32e>

![](img/57b7037ef57ef19e0a35183c1459c2ed.png)

我的这片最初[出现在 2017 年 3 月 Tripwire 的安全状态](https://www.tripwire.com/state-of-security/security-data-protection/tape-hard-drive-lights-latest-security-hardening-measure/)博客上。尽情享受吧！

当硬盘包含超级敏感的数据时，[像我这样的网络安全专家通常会建议不要把硬盘放在任何有 TCP/IP 协议栈的电脑中。有多种方法可以保护联网计算机免受攻击，如防火墙、IPS 设备、防病毒软件和操作系统配置。对于大多数个人和专业计算需求来说，这已经是足够好的](https://hackernoon.com/tagged/cybersecurity)[安全性](https://hackernoon.com/tagged/security)。

即使你采取了许多安全防范措施，攻击的可能性还是存在的，但是大多数计算机都需要使用互联网。但是，当攻击者真的想要你的数据时，比如在金融行业或情报部门，最好的办法是让你最敏感的数据存储没有任何网络连接。我们称这种技术为气隙技术。

现在，气隙可能已经不够了。人们能看到你硬盘上闪烁的灯吗？

本古里安大学的 Mordechai Guri 发现了一种攻击者从气隙机器中获取数据的新方法。Guri 和他的研究团队开发了一种在用户层面运行的恶意软件，旨在通过 LED 灯传输硬盘数据。攻击者面临的挑战是让恶意软件出现在一台有气隙的机器上。但是对于攻击者来说，使用可移动介质，如 u 盘或光盘，也是可能的。[社会工程](https://www.tripwire.com/state-of-security/security-data-protection/cyber-security/eitest-corners-chrome-users-social-engineering-delivers-fleercivet-trojan/)你的方式物理访问目标机器，宾果！

古丽的团队多年来一直专注于气隙电机的安全性。他们开发的技术改进了以前从空气隙计算机获取数据的漏洞，因为它更隐蔽，恶意软件不需要内核级权限。

以下是攻击者如何实施 Guri 的攻击。攻击者将恶意软件放在某种可移动介质上。然后他们获得对目标机器的物理访问权。如果 USB 端口没有被禁止挂载文件系统，或者光驱没有被禁止读取光盘，有一个简单的方法。他们不需要通过管理员、权限升级或破解管理员密码，因为恶意软件是用户级的。

一旦恶意软件进入目标机器，接下来就是下一步。一个摄像头可以看到目标机器的硬盘指示灯。(古丽的团队在他们的研究中使用了一架无人机附带的相机。)安装在相机上的软件解释光信号，或者从相机中取出的视频文件由同类软件读取。那么攻击者就有了他们想要的数据。

当 LED 灯以 4000 赫兹闪烁时，人眼无法察觉。Guri 的团队能够在几分钟到几秒钟之间传输 4096 位加密密钥，这取决于相机的接收质量。哇！该技术传输数据相对较慢，但一些最敏感的数据(如密钥)非常小。

因此，要加强对古丽的利用，你可以这样做:

*   以通常的方式锁定对数据中心的物理访问。这降低了可移动介质将恶意软件传输到空气隙硬盘的可能性。
*   禁用并非绝对需要启用的 USB 端口、光驱和硬盘连接。
*   确保您的服务器室没有窗户。
*   或者，最简单的方法是在 led 上贴上不透明胶带。

从亚马逊可以买到[一卷胶带，价格低至 68 美分。4.52 美元的运费寄到我的邮寄地址，总共不到 5 美元。多么实惠的网络安全措施！](https://www.amazon.com/Duck-Duct-Tape-1-88in-Black/dp/B000BOKNC0/ref=sr_1_1?s=industrial&ie=UTF8&qid=1489172133&sr=1-1)

但是，从气隙机器中提取数据的创新和隐蔽技术越来越多，这种风险不容忽视。坦白地说，在 Guri 的研究公开之前，我甚至没有想到 HDD LED 灯可能是一个漏洞。如今，信息安全研究人员必须跳出框框思考问题！

如果你喜欢我的作品，并希望支持我，[请考虑捐赠给我的 Patreon。](https://www.patreon.com/kim_crawley)