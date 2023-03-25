# 作为具有公钥基础设施后端的证书颁发机构保管库

> 原文：<https://medium.com/hackernoon/vault-as-ca-with-pki-backend-bbcfc315f06f>

我将展示如何在 pki 框架内运行您自己的 CA，并能够生成私钥和签名证书。我们将使用 **vault** 进行此操作，因为这是完成此操作的最快方法。

## 下载并运行保管库:

确保您从[https://www.vaultproject.io/downloads.html](https://www.vaultproject.io/downloads.html)获得它，或者您根据自己的喜好手工建造它。

对于本教程，我们将在开发模式下运行它，但是如果您在生产中运行它，请确保您会做得更好。

```
vault server -dev
```

![](img/f6d0a9526fb0a1a24ca7aad252293580.png)

很简单，需要注意的一点是，您需要导出和 env 变量，以使 vault 知道要使用哪个 vault 服务器:

```
export VAULT_ADDR='[http://127.0.0.1:8200'](http://127.0.0.1:8200')
```

## 创建公钥基础设施后端

Vault 有可插入的后端，所以我们需要安装我们想要使用的后端，原因如下:

```
vault mount pki
vault mount-tune -max-lease-ttl=87600h pki
```

![](img/77ff6b991078cfe4dd178afe9c91a0f6.png)

## 生成根证书

我们将直接从根证书颁发，而不使用中间证书，所以让我们创建根证书，CA 将使用:

```
vault write pki/root/generate/internal common_name=internal.com ttl=87600h
```

![](img/d418632beae8adb4d858fc523db2bfbe.png)

密钥和证书将存储在后端

## 为 CA 配置 CRL:

```
vault write pki/config/urls issuing_certificates="[http://127.0.0.1:8200/v1/pki/ca](http://127.0.0.1:8200/v1/pki/ca)" crl_distribution_points="[http://127.0.0.1:8200/v1/pki/crl](http://127.0.0.1:8200/v1/pki/crl)"
```

![](img/bb724fc5cfc13a05bb47e692bbeb688b.png)

## 创建角色

我们将创建一个类似策略的角色，允许我们生成证书/密钥或凭据:

```
vault write pki/roles/jerrycom allowed_domains="jerry.com" allow_subdomains="true" max_ttl="72h"
```

![](img/3a7fc31ca0ee6e46b572e2ed07f87994.png)

因此，该策略允许我为 jerry.com 域生成凭据，并允许创建子域。

## **发行 crt 和私钥**

最后，我们可以通过 tls 创建我们需要在不同服务中使用的凭据

```
vault write pki/issue/jerrycom common_name=blah.jerry.com
```

![](img/9ee77ef57d9063b03d57ea6170682379.png)

从上面的命令你会得到一把钥匙和一个阴极射线管，

```
-----BEGIN CERTIFICATE-----
```

和

```
-----BEGIN RSA PRIVATE KEY-----
```

如果您想验证这些是否匹配，因为 crt 已用私钥签名，请将 crt 保存在一个文件中，并将密钥保存在另一个文件中，然后运行:

对于密钥:

```
openssl rsa -noout -modulus -in vaultkey | md5sum
```

![](img/08a32df4260dcf6e466255f64b57d0eb.png)

对于阴极射线管:

```
openssl x509 -noout -modulus -in vaultcrt | md5sum
```

![](img/d1d08a05b4a17cfcc7dd32d3fad5ebc0.png)

例如，您也可以将它们加载到 nginx 中，并用浏览器进行测试。

谢谢你。