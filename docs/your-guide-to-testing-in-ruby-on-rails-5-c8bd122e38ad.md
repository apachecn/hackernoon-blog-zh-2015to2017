# Ruby on Rails 5 测试指南

> 原文：<https://medium.com/hackernoon/your-guide-to-testing-in-ruby-on-rails-5-c8bd122e38ad>

![](img/33709e7891bad81f574a126cb762655b.png)

注意:这篇文章来自我即将出版的书[中的一个样本章节，在 Rails 6](https://buildasaasappinrails.com) 中构建一个 SaaS 应用。这本书指导你从卑微的开始，通过部署一个应用到生产。这本书现在已经开始预售了，你可以马上拿到免费的一章！

另外，我的新项目[的测试版已经发布了。如果你失去了对拉取请求的跟踪，让旧的请求留在身边，或者只是喜欢一个通过多个服务(Github、Gitlab 和 Bitbucket)聚集这些请求的仪表板，](https://pullmanager.com)[查看一下](https://pullmanager.com)。

测试，虽然需要更多一点的认知负荷，甚至被一些开发人员所厌恶，但通常会在以后得到回报。在某些时候，你几乎总是会说“我很高兴我为此写了测试”。TDD(测试驱动开发)是 Rails 社区发展起来的一种方法。虽然，没有完全被社区中的所有人接受。这种方法让您首先编写测试，然后编写代码使您的测试通过。

# 为什么要测试

当测试正确进行时，它将提供一些主要的好处。首先，它将帮助你开发应用程序中的行为。您可以编写一个测试，验证您的模型是否存在电子邮件。然后意识到您需要验证密码或者添加一个“重新输入密码”输入框。

测试的另一个重要原因是在修改代码或添加新特性时保护自己免受应用程序中的退化。当您重构类、折叠代码或删除无关代码时，这也将给你信心和保证。如果笔试在修改前后都通过了，你就可以开始了！另一种测试方法是在发现应用程序中的问题或错误后编写测试。确保修复有效，并且不会在将来困扰您的应用程序。

# 测试框架

像 Rails 的许多其他部分一样，也有默认的 gem 和库。以及其他可选的替代项，它们有时比缺省值更常用。

**RSpec vs MiniTest**

从 Rails 的最新版本开始，默认的测试框架是 MiniTest。这本身就是对 Rails Test::Unit 的原始测试框架的改进。在选择 Rails 应用程序的许多方面时，个人偏好可以发挥很大的作用。测试也不例外。

MiniTest 的最大替代品是 RSpec。关注测试的可读性和可组合性。当命名测试和测试组时，它使用诸如“描述”和“它”这样的词。例如:

```
describe “the test” do it “is a success” do expect(1).to eq(1) endend
```

而 MiniTest 将使用其他编程测试框架中的资产术语。就我个人而言，我喜欢 RSpec，觉得它更容易推理，而且命令行界面(CLI)也更好一些。

**水豚特征测试 vs 综合测试**

测试工作中另一个值得注意的选择是增加了水豚。虽然您可以使用内置集成测试来测试工作流。它让人们对现实世界的浏览器工作流有了更多的渴望。这就是水豚的用武之地。允许您编写功能测试，引导您在应用程序中选择页面。执行行为，如点击、填写表格或验证一切正常。

Capybara 直接插入 RSpec，允许您使用相同的语法和措辞来编写测试。这是一个来自他们文档的例子！

```
describe “the signin process”, :type => :feature do
 before :each do
   User.make(email: ‘user@example.com’, password: ‘password’)
 end it “signs me in” do
   visit ‘/sessions/new’
   within(“#session”) do
     fill_in ‘Email’, with: ‘user@example.com’
     fill_in ‘Password’, with: ‘password’
   end
   click_button ‘Sign in’
   expect(page).to have_content ‘Success’
 end
end
```

**FactoryGirl**

我要提到的最后一个选择是 gem FactoryGirl。Rails 中测试对象的默认方法是 fixtures。夹具文件是以 YAML 格式编写的。提供基本的静态模拟对象表示。随着模型对象变得越来越复杂，夹具会变得越来越难。包括他们的社团激增。FactoryGirl 将自己描述为创建测试对象的 API。也就是说，您将通过编程使用 API 为您的测试创建模拟对象。你可以在 Ruby 中提供一个工厂定义，然后像普通对象一样使用 API 来`. build '或`. create`。factory girl 还使得通过工厂定义包含关联变得非常简单。

# 测试类型

在典型的 Rails 应用程序中，有三种主要的测试类型。模型(单元)测试、控制器测试和视图测试。此外，您还可以使用功能测试。这些测试将作为应用程序的端到端演练测试。每种类型的测试在测试世界中都有它的位置，并且可能同等重要。

# 模型/单元测试

模型规格很可能是您将在 Rails 中处理的最简单的测试类型。通过这些测试，您正在测试模型对象上的普通 CRUD(创建/读取/更新/删除)操作。此外，他们将测试验证和/或关联。模型规格通常会运行得非常快，并且相当独立

```
FactoryGirl.define do factory :user do name “Same Name” email “test@user.com” endendclass User < ApplicationRecord validates :email, presence: true
  validates :email, uniqueness: true
  validates :name, presence: true…describe User do it “has a valid factory” do expect(FactoryGirl.build(:user).save).to be_true end it “is invalid without a name” do expect(FactoryGirl.build(:user, name: nil).save).to be_false end it “is invalid without a unique name” do user = FactoryGirl.create(:user) expect(FactoryGirl.build(:user, name: “Same Name”)).to be_false end it “is invalid without an email” do expect(FactoryGirl.build(:user, email: nil)).to be_false endend
```

对于这个例子，有一个已经有效的用户工厂定义，它正在设置一些默认值。这个特定的场景是测试用户模型是否可以创建用户。然后在这个用户模型的几个关键 Rails 模型验证器上验证用户创建失败。

# 控制器测试

当涉及到测试你所有的条件逻辑时，控制器测试将是你的面包和黄油。测试基于请求类型、用户类型、模型的成功/失败等等。控制器规格比模型规格稍慢一些，因为它们使用了请求/响应周期。尽管仍然比功能规格快得多。这使得它成为进行大量测试的好地方。

```
describe UsersController do describe “GET index” do it “assigns @users” do user = FactoryGirl.create(:user) get :index expect(assigns(:users)).to eq([user]) end it “renders the index template” do get :index expect(response).to render_template(“index”) end end describe “GET #show” do it “renders the #show view” do get :index, {id:user.id} response.should render_template :show end end endend
```

在这个测试文件中，您会发现我们正在测试一个列出用户的页面。首先，测试动作分配正确的输出。在创建一个用户并获得:index 操作之后，控制器将拥有一个实例变量，该变量等于来自测试的一个用户的数组。第二个测试是简单地测试获取:index 动作是否会呈现索引模板。

显然，这些测试并不复杂，只是为了让您体验一下控制器测试有多简单。在真实世界的控制器文件中，您将测试每个动作。测试其行为和渲染。以及操作的每个分支，如数据库事务的成功或失败。

# 查看测试

在特性测试中测试视觉表现的所有方面的开发人员经常忽略视图规格。特性测试意味着给你高层次的行为测试。然而，它们绝对不应该用来测试服务器渲染模板的每一个细节。很多时候，您需要基于某种 if 语句或逻辑来测试不同的标记，这可以在视图规范中完成。

```
FactoryGirl.define do factory :user do
    name “Same Name”
    email “[test@user.com](mailto:test@user.com)”
    admin false
  endend<%- if @user.admin? %> <h1>Welcome back admin</h1><%- else %> <h1>Welcome back pal</h1><%- end %>describe “rendering homepage” do it “displays admin message” do assign(:user, FactoryGirl.create(:user, admin: true)) render :template => “home/index.html.erb” expect(rendered).to match /admin/ end it “displays regular user message” do assign(:user, FactoryGirl.create(:user)) render :template => “home/index.html.erb” expect(rendered).to match /pal/ endend
```

在这个例子中，我们有一个简单的视图模板，它在标记中呈现一条消息。该名词基于用户变量中引用的用户是否是管理员。在这里，视图规范使用每个测试(一个管理员，一个非管理员)中创建的用户对象呈现消息，并使用正则表达式测试文本。

# 功能测试

最后，特性规格(测试)是一次性测试整个应用程序(或者大部分行为)的好方法。使用 RSpec 和 Capybara，您将能够编写简单明了的测试，就像您是最终用户一样浏览您的应用程序。

```
describe “Signing Up”, :type => :feature doit “allows a user to sign up” do
 visit root_path expect(page).to have_content ‘Sign Up’
 within(“form#user_new”) do fill_in ‘Name’, with: ‘John’
   fill_in ‘Email’, with: ‘user@example.com’
   fill_in ‘Password’, with: ‘password’ fill_in ‘Retype Password’, with: ‘password’
 end
 click_button ‘Sign Up’
 expect(page).to have_content ‘Welcome to Sample App’ expect(page).to have_content ‘John’
 end
end
```

这个测试模拟一个用户第一次访问您的应用程序。然后看到一个注册页面，填写完毕，注册后成功访问应用。

正如你所看到的，特性测试提供了一种令人惊奇的高级测试方式，就像用户与你的应用程序交互一样。然而，随之而来的是事实特性测试的运行速度会比其他类型的测试慢。因此，不测试应用程序中的每个分支或可能性是明智的。使用控制器或其他规格来测试边缘情况。

# 其他测试工具

此外，我还想介绍一些额外的工具，它们将包含在您的应用程序的代码库中。这些工具将有助于解决诸如测试清理这样的问题。另一个是测试覆盖完整性工具，它包括一个本地 web 工具来逐个文件地查看分析。最后，是一个很容易模仿外部资源的工具，比如第三方 API。

数据库清理器(Database Cleaner):数据库清理器是一个测试工具，它允许你在每次测试后使用不同的策略将数据库清理到干净的状态。这里的重要性在于，虽然 Rails 试图将它们的测试包装在事务中。这可以防止一个测试数据库的更改影响另一个测试。然而，当使用 javascript 驱动程序时，这些测试是在另一个线程中执行的。这意味着您将需要另一种方法来将数据库恢复到原始状态，即使用截断。特别是，数据库清理器将允许你基于测试类型设置“清理”方法。

**Simplecov** :我们将使用这个 gem 来计算、显示和调查代码覆盖率。这个工具将使用内置的 Ruby 覆盖信息，以及合并不同工具和测试类型覆盖的结果。之后，它将生成信息，保存到一个报告中，并自动创建一个 HTML 站点供您浏览代码覆盖率分析。

**VCR** : VCR 允许你“记录”HTTP 事务，并在本地保存它们的响应。这允许您“回放”来自文件的响应，使得运行与外部资源交互的测试变得容易和快速。随着速度的提高，它允许离线测试，不用担心第三方的改变会影响你的测试。但是，我建议您仍然对实际的外部资源的响应执行某种测试。

# CI 和你

本章最后要讨论的是持续集成。这意味着您将不断地将变更合并到主分支中，在主分支中，各种管道对新合并的代码进行处理。在大多数情况下，这是一种将变更合并到一个中心分支的方法。然后在外部系统上运行测试，并使用测试结果来选择另一个操作，比如部署或请求对代码库进行修复。

有一些不同的工具可以用于 CI。第一个可能在[詹金斯](http://jenkins.io/)中推荐。Jenkins 的伟大之处在于它是一个开源项目，并且是免费的。但是，他们的网站上没有托管解决方案。也就是说，你要么必须建立和维护你的 Jenkins 服务器，要么寻找一个能为你做这些的服务。

对于那些不想维护自己的服务器来运行持续集成的人来说，这是一个好消息。有相当多的服务运行 CI 服务。拥有主要存储库服务的集成墙(GitHub、Bitbucket 等)。这意味着对您的存储库的简单推送可以触发您的测试在他们的服务上自动运行。随后是一份用你喜欢的任何反应介质写的结果报告。例如电子邮件、聊天服务等。

在没有任何从属关系的情况下，我的应用程序通常使用的服务器是 [CircleCI](http://circleci.com/) 。我发现它们的易用性和价格(对我来说是免费的)很好地满足了我的需求。其他选择还有 [TravisCI](https://travis-ci.org/) 和[semaphoresci](https://semaphoreci.com/)。我建议检查一下这些产品，选择最适合你的。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)