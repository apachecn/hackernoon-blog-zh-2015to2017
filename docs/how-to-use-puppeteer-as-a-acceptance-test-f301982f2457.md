# 如何使用木偶师作为验收测试

> 原文：<https://medium.com/hackernoon/how-to-use-puppeteer-as-a-acceptance-test-f301982f2457>

你们可能会混淆木偶师是一个浏览器自动化。`assert`的东西没有 API。我们如何将它用作验收测试工具？。

我们可以将它与任何 javascript 测试工具集成。因此，他们将有能力做浏览器自动化和测试。在本文中，我们将使用 jest 作为测试工具。
首先，我们将安装`Puppeteer`和`Jest`

```
yarn init
yarn add puppeteer jest
```

将此代码添加到`package.json`

```
"scripts": {
  "test": "jest"
}
```

然后我们将使用木偶师打开 [Pronto 工具](http://prontotools.io)网站，检查标题是否等于`Pronto Tools`。然后，检查屏幕中央的文本是否相等

```
Tools for Your Growing Business
```

是时候编码了。让我们创建文件名`test.js`，并将下面的代码放到这个文件中

```
const puppeteer = require('puppeteer');describe('Open ProntoTools Website', () => {
  var browser, page;
  var url = '[https://prontotools.io'](https://prontotools.io')beforeEach (async () => {
    browser = await puppeteer.launch({ headless: false });
    page = await browser.newPage();
  })afterEach (() => {
    browser.close()
  })test('Title == Pronto Tools', async () => {
    await page.goto(url);
    const title = await page.title();
    expect(title).toBe("Pronto Tools");
  });test("Center == Tools for Your Growing Business", async () => {
    await page.goto(url);
    const center = await page.$eval('h2.font-34.uppercase > strong', e => e.innerHTML);
    expect(center).toBe("Tools for Your Growing Business");
  });
})
```

在第一种情况下，我们通过打开网站检查标题，并检查标题标签是否相等`Pronto Tools`

第二种情况，我们将打开网站并检查我们感兴趣的选择器的 innerHTML

在每个测试用例中，我们将在运行测试用例之前打开浏览器，在`beforeEach`和`afterEach`之后完成测试用例之后关闭浏览器

最后，我们可以使用`yarn test`命令运行我们的测试套件并得到结果

![](img/5b40b94797610e1d90d59865bee45336.png)

这是使用`Puppeteer`和`Jest`进行浏览器自动化测试的简单方法