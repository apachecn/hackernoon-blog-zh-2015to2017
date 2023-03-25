# angular-cli 和 docker/kubernetes

> 原文：<https://medium.com/hackernoon/angular-cli-and-docker-kubernetes-12bd30b345b>

如果你只是想为 angular2 应用程序创建一个容器，这里有一些步骤可以帮助你:

如果你没有 angular2 应用程序，用 angular-cli 创建它，然后用 aot 构建它

```
ng build --prod --aot
```

它将创建*区* [目录](https://hackernoon.com/tagged/directory)

**创建 *default.conf* nginx 文件:**

它将有缓存和捕捉所有位置到您的索引(如单页应用程序所预期的)

```
server {
  listen 80;

  charset utf-8;

  sendfile on;

  root /usr/share/nginx/html;

  ##
  # Gzip Settings
  ##
  gzip on;
  gzip_http_version 1.1;
  gzip_disable      "MSIE [1-6]\.";
  gzip_min_length   1100;
  gzip_vary         on;
  gzip_proxied      expired no-cache no-store private auth;
  gzip_types        text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript;
  gzip_comp_level   9;

  #Caches static assets
  location ~ ^/(assets|bower_components|scripts|styles|views) {
    expires     31d;
    add_header  Cache-Control public;
  }

  #Caches Bundles created by angular cli
  location ~* \.(?:bundle.js|bundle.css)**$** {
    expires 1M;
    access_log off;
    add_header Cache-Control "public";
  }

  ##
  # Main file index.html sending not found locations to the main
  ##
  location / {
    expires -1;
    add_header Pragma "no-cache";
    add_header Cache-Control "no-store, no-cache, must-revalidate, post-check=0, pre-check=0";

    try_files **$**uri **$**uri/ /index.html = 404;
  }
}
```

**创建一个 Dockerfile 文件来建立你的形象:**

```
#alpine image
**FROM** nginx:alpine
#create the server and location configuration
**COPY** default.conf **/**etc**/**nginx**/**conf.d**/**default.conf
#copies the build app to the default location
**COPY** dist **/**usr**/**share**/**nginx**/**html
```

如果你尝试运行 **docker build** 这将花费很长时间…因为 [docker](https://hackernoon.com/tagged/docker) 发送了很多文件来构建 de 容器，所以你必须创建一个**。只发送所需文件的 dockerignore**

```
*
!dist
!default.conf
```

那好多了！现在，如果您想使用 kubernetes，只需创建一个具有所需属性的 yaml:

```
**apiVersion:** v1
**kind:** Service
**metadata:
  name:** webapp
  **labels:
    run:** webapp
**spec:
  type:** NodePort
  **ports:** - **port:** 80
    **targetPort:** 80
    **protocol:** TCP
    **name:** http
  - **port:** 443
    **protocol:** TCP
    **name:** https
  **selector:
    run:** webapp
---
**apiVersion:** extensions/v1beta1
**kind:** Deployment
**metadata:
  name:** webapp
**spec:
  replicas:** 1
  **template:
    metadata:
      labels:
        run:** webapp
    **spec:
      containers:** - **name:** webapp
        **image:** [username]/[image]:[version]
        **ports:** - **containerPort:** 80
        - **containerPort:** 443
      **imagePullSecrets:** - **name:** myregistrykey
```

希望有帮助..

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！