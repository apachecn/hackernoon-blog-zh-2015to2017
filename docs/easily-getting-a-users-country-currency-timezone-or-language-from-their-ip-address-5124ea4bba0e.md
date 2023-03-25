# 从用户的 IP 地址中轻松获取用户的国家、货币、时区或语言

> 原文：<https://medium.com/hackernoon/easily-getting-a-users-country-currency-timezone-or-language-from-their-ip-address-5124ea4bba0e>

![](img/bd10b77a3b519a96cd021284ca9aecef.png)

在过去的许多项目中，当我不得不使用 Geo IP [API](https://hackernoon.com/tagged/api) 服务来查找用户的 IP 地址时，通常是出于几个原因之一——获取用户的国家(用于地理锁定某些功能，或默认到网站的某些部分或类别)，将用户创建的帐户默认为某种货币，让他们的时区适当地保留电子邮件或通知的时间，或设置默认语言。(对于最后一种情况，通常与接受语言 HTTP 头一起使用)

现在，您可以使用[用户国家 API](https://usercountry.com) 服务在一个步骤中完成所有这些工作，而不是必须找到一个 Geo IP API 服务来进行 IP 查找，然后寻找另一个服务或数据库来进行货币或语言查找。

## 简单 REST API

API 调用本身只是一个 HTTP GET 请求。事实上，你甚至可以在你自己的浏览器上测试它，方法是:[https://usercountry.com/v1.0/json/173.194.192.101](https://usercountry.com/v1.0/json/173.194.192.101)

您将看到一个 JSON 格式的响应，看起来就像主页上的示例。这已经包含了您想要的该 IP 用户的关键数据——货币、国家的官方语言和时区信息。还有一些额外的东西，如大陆，更精确的地区数据(甚至邮政编码)。

下面是一些主要语言的示例代码，展示了从用户的 IP 地址获取默认货币是多么容易！您可以很容易地修改它们，以获得它们的时区、国家语言或邮政编码。

# Node.js 中的示例代码(使用请求包)

从 npm 安装`request`库:

```
npm install request
```

应用代码:

```
import request from 'request';request('http://usercountry.com/v1.0/json/173.194.192.101',
  (err, response, body) => {
    const result = JSON.parse(body);
    result.currency.code;
  }
);
```

# jQuery 中的示例

```
$.ajax({
  url: 'http://usercountry.com/v1.0/json/173.194.192.101',
  dataType: 'json',
  success: function(result) {
    result.currency.code;
  }
});
```

# PHP 示例代码(使用 curl)

```
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'http://usercountry.com/v1.0/json/173.194.192.101');
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);
$response = curl_exec($ch);
curl_close($ch);
$result = json_decode($response, true);
$result['currency']['code'];
```

# Ruby 中的示例代码

```
require 'net/http'
require 'json'url = 'http://usercountry.com/v1.0/json/173.194.192.101'
uri = URI(url)
response = Net::HTTP.get(uri)
result = JSON.parse(response)
result['currency']['code']
```

# Python 中的示例代码

```
import requestsr = requests.get('http://usercountry.com/v1.0/json/173.194.192.101')
result = r.json()
result['currency']['code']
```

很简单，对吧？

请注意，在以上所有示例中，您应该添加更多的错误检查，比如检查请求是否成功，以及检查响应对象的`status`字段，以确保在使用结果之前，它显示来自用户国家 API 调用的`success`。

编码快乐！