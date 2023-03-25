# 鲍尔有什么新鲜事

> 原文：<https://medium.com/hackernoon/whats-new-in-bower-6b00816bd6b>

bower 团队一直在努力工作，在项目中获得新功能，并为用户和开发者关闭 bug。截至 2016 年 4 月 5 日，bower 版本为 1.7.9。

我最喜欢的一个选项是在每次下载时启用`— save ',如这里所述 [1040](https://github.com/bower/bower/issues/1040) ，通常当我添加一个 bower 依赖项时，我希望它保存在 bower.json 上，很少我会下载一个没有项目的包。

向后兼容性很重要，所以默认情况下这是禁用的，要启用它，请将选项“保存”和/或“精确保存”添加到。bowerrc 配置文件。

```
{
  "save": true,
  "save-exact": true,
}
```

使用 save-exact，版本将保存到您的 bower.json 中，如下所示:

```
{
  "angular": "^1.5.3", // version 1.5.3 or newer
  "bootstrap": "3.0.0" // always use the exact version 3.0.0
}
```

本月新增的所有功能包括:

# 1.7.9–2016–04–05

*   显示无效 bower.json 字段的警告
*   更新鲍尔-json
*   对包名的验证不太严格(允许空格、斜线和“@”)

# 1.7.8–2016–04–04

*   在非交互会话中不要询问 git 凭证，修复了# [956](https://github.com/bower/bower/issues/956) # [1009](https://github.com/bower/bower/issues/1009)
*   使用编程 api 防止吞咽异常，修复# [2187](https://github.com/bower/bower/issues/2187)
*   将 graceful-fs 在所有依赖关系中更新到 4.x，修复 nodejs/node# [5213](https://github.com/bower/bower/issues/5213)
*   使用 cwd 解析可插拔解析器并回退到全局模块，修复# [1919](https://github.com/bower/bower/issues/1919)
*   将把手升级到 4.0.5，关闭# [2195](https://github.com/bower/bower/issues/2195)
*   替换已定义脚本中的所有%字符，而不仅仅是第一个字符，修复# [2174](https://github.com/bower/bower/issues/2174)
*   更新 opn 包以修复 Windows 上“bower open”命令的问题
*   更新 bower-配置
*   不要在脚本钩子中插入环境变量，修复 [bower/config#47](https://github.com/bower/config/issues/47)
*   更新鲍尔-json
*   更严格地验证包名，只允许拉丁字母、点、破折号和下划线
*   在中添加对“保存”和“精确保存”的支持。bowerrc，# [2161](https://github.com/bower/bower/issues/1040)

所有的变化都可以在找到。