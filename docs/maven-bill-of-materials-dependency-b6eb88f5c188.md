# Maven“物料清单”依赖性

> 原文：<https://medium.com/hackernoon/maven-bill-of-materials-dependency-b6eb88f5c188>

[![](img/24325228c537a09e28d6e4d8a7d100b8.png)](https://coinmonks.com)

**Click to Find blockchain Jobs**

`[Find blockchain jobs using Coinmonks jobs portal](https://coinmonks.com)`

`[***Get published on Coinmonks***](https://medium.com/coinmonks/contribute/home)`

使用 Maven 时，可能会意外地混合不同版本的 Spring JARs。例如，你可能会发现一个第三方库，或者另一个 Spring 项目，把一个过渡性的[依赖项](https://hackernoon.com/tagged/dependency)拉进了一个旧版本。如果您忘记自己显式声明一个直接依赖项，就会出现各种意想不到的问题。

为了克服这些问题，Maven 支持“物料清单”(BOM)依赖的概念。您可以在您的`dependencyManagement`部分中导入`spring-framework-bom`,以确保所有的 spring 依赖项(直接的和可传递的)都是相同的版本。

```
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-framework-bom</artifactId>
            <version>4.3.7.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

使用 [BOM](https://hackernoon.com/tagged/bom) 的一个额外好处是，当依赖 Spring 框架构件时，您不再需要指定`<version>`属性:

```
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
    </dependency>
<dependencies>
```

注意:这只是简单地从[复制粘贴到这里](https://docs.spring.io/spring/docs/4.0.x/spring-framework-reference/html/overview.html)，但是如果你还不知道的话，你确实学到了一个新东西:)

![](img/cf576e25f53be5874a20833a89a756f2.png)

[**Create invoices and Get paid in Crypto**](https://cryptofi.co)