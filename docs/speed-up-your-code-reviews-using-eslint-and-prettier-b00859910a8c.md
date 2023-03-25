# 使用 ESLint 和 Prettier 加速你的代码审查

> 原文：<https://medium.com/hackernoon/speed-up-your-code-reviews-using-eslint-and-prettier-b00859910a8c>

![](img/6465f6aa87b4fdac6599e70f7b22498b.png)

by [Marc-Olivier Jodoin](https://unsplash.com/@marcojodoin)

如果你想构建伟大的软件，代码审查是非常重要的。这是一种与团队其他成员分享[代码库](https://hackernoon.com/tagged/codebase)知识的有效方式，这是一个很好的学习机会，因为评审者可能会提出比你通常的方法更好的解决问题的方法，它有助于识别实现中的逻辑错误或差距，它有助于确保代码库保持可读性、可维护性并遵循你的团队的编码惯例等

代码审查也很耗时。对于评审者来说，它要求他们通过变更来寻找问题和机会进行[改进](https://hackernoon.com/tagged/improvement)。他们检查的东西越多，花费的时间就越多，注意力就越不集中。对于作者来说，一旦评审结束，他们需要按照评审意见重构代码，做额外的测试，做自我评审等等。冲洗并重复。

我们应该努力使这个过程更快，这样我们就可以尽快地将软件交付给我们的用户。我们可以通过尽可能自动化代码审查，让他们关注非自动化方面，来减少审查人员审查代码所花费的时间。对于作者来说，我们可以尽早对代码给出反馈，这样他们就可以在开发过程中更早地进行重构。

检查变更是否遵循编码惯例、最佳实践和代码格式是我们可以自动化的事情。这些也是在代码评审过程中引发最多挑剔的问题，从而在评审评论中产生更多的噪音。

ESLint 和 T2 prettle 是两个流行的工具，可以帮助我们实现这个目标。更漂亮的[格式化](https://github.com/prettier/prettier#what-does-prettier-do)代码，ESLint 帮助执行编码约定[发现代码中有问题的模式](https://eslint.org/docs/rules/#best-practices)。ESLint 还有一个[自动修复](https://eslint.org/docs/user-guide/command-line-interface#--fix)模式，可以自动修复一些违反规则的情况。两个[都有用于所有流行编辑器的](https://github.com/prettier/prettier#editor-integration) [插件](https://eslint.org/docs/user-guide/integrations#editors)，这确保了违规行为被快速显示给开发者。但是，如果开发人员使用的编辑器没有这些插件，或者是偶尔贡献代码的人，并且您不想通过要求他们安装或配置插件来增加他们工作流的摩擦，我们可以使用 git 提交挂钩，以便在提交代码时自动检查代码。Git 提交挂钩也有助于确保所有提交的代码都遵守规则，并且不会因为错误配置的编辑器或其他原因而出现[坏窗口](https://blog.codinghorror.com/the-broken-window-theory/)。您可以使用 [lint-staged](https://github.com/okonet/lint-staged) 来轻松设置 git 提交挂钩。

如果你刚建立一个项目，不想花时间选择规则或配置，漂亮的默认设置和 ESLint 可以用流行的风格指南初始化。

如果你想把它引入到一个现有的项目中，你可以运行所有的文件，并使用 ESLint auto-fix 按照新的规则修改现有的代码。对于自动修复未覆盖的规则，您可以先[禁用](https://eslint.org/docs/user-guide/configuring#configuring-rules)所有剩余的不可自动修复的规则，然后手动批量修复它们，并在修复后重新启用它们。如果这是一个非常大的项目，你可能想把你的代码库分成不同的部分，让[目录特定的 ESLint 配置](https://eslint.org/docs/user-guide/configuring#configuration-cascading-and-hierarchy)，一次对一个部分进行修改。

最初发表于 sheshbabu.com