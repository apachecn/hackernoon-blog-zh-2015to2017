# 为 Rails URLs 定制的 slugs 做得很好

> 原文：<https://medium.com/hackernoon/using-custom-slugs-for-rails-urls-500eb3f58f3c>

我在网上看到了很多这样的例子，我花了一段时间才弄清楚，所以我决定记录下来。

我在两个案例中使用了这种方法:

1.  虚假网址，例如试图在`/u/amingilani`打开用户的个人资料
2.  混淆的网址，例如试图使[网址](https://hackernoon.com/tagged/url)难以猜测，如`/transactions/601585f7–0f4a-41e8-bd04-b2eb24262fb4`

两种情况的区别仅在于后者中的废料是随机产生的。