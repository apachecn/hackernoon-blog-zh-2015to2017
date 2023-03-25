# 利用 Medium 的 CDN 改进 Feeds 的页面加载时间

> 原文：<https://medium.com/hackernoon/hacking-around-with-mediums-cdn-for-improving-pageload-times-for-feeds-233c71d21f8f>

![](img/e4cabf9b4239b88c4821881fc5f33439.png)

*Photo atop piece is adapted from “*[*Tortoise shell 8*](https://www.flickr.com/photos/7193008@N02/1443793915/in/photolist-3czPCx-89kfzb-2UF2JA-92P3d5-9VoVqP-6kPcyC-23iqPx-9q8n1f-8VUsmx-TeqMG-2UF2Ey-S9RtJq-5AzhND-qsNqTg-7YqV6s-55YEtj-qsFZPG-Qiie2p-5g2G8A-5fXmLB-4G8mT6-4G8mXi-6mHZo6-8ZhTWo-3972dm-4DVurK-55UvMB-3ftGR4-5hhbCB-7T8oFD-55YJ85-csqWa-6jZAHZ-c6eyrE-CJumwW-9Vbawy-SFM1cL-4GqXdd-Paazg7-9orKfY-5TR9h8-8Nok58-gzApTa-5AzJbW-7xyFbP-5kYu7j-53yvf4-5BsZ7Z-7yzmrK-2k3Su6)*” by* [*Georgia Reading*](https://www.flickr.com/photos/7193008@N02/) *(Flickr, Creative Commons).*

早些时候，我决定要在我网站的底部显示我的出版物中的文章。方便的是，我需要完成的大部分信息都存在于我的出版物提要中。

对于许多媒体出版物，提要可以这样找到:

```
[https://medium.com/feed/[your](https://medium.com/feed/web-design-web-developer-magazine/)…
```