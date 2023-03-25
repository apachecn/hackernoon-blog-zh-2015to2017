# [React]复习:单元测试很容易

> 原文：<https://medium.com/hackernoon/react-unit-test-example-tutorial-jest-enzyme-jsdom-mocha-chai-state-props-component-7ec850a98566>

![](img/6c104c52fc4623f0573860d6ca6225d5.png)

[http://www.deviantart.com/art/verifying-her-credentials-51202151](http://www.deviantart.com/art/verifying-her-credentials-51202151)

# 找到正确的 React 单元测试工具

为 React 组件选择合适的单元测试工具是令人困惑的，由于 google 的***【React 单元测试】*** 的结果，数百个 chai+jsdom、jest+mocha、mocha+enzyme 等组合，映入我的眼帘。嘿，谷歌，你能告诉我最好的一个，不要让我思考吗？

React 组件的单元测试稍有不同，React 中的单元测试不是验证函数的输出，而是需要 4 个额外的测试目的( [*Ref Artem*](https://hackernoon.com/testing-react-components-with-jest-and-enzyme-41d592c174f#.ywbnzlakf) ):

1.  *测试基本组件渲染*
2.  *测试道具*
3.  *测试事件*
4.  *测试事件处理程序*

两个最受欢迎的选项是**玩笑**和**酶**。在花了几个小时尝试这些工具后，我最终选择了伟大的 Airbnb 开源酶。

## 玩笑

Jest 是一个由脸书维护的 JavaScript 测试运行程序。包括了性能，嘲讽，**快照**，代码覆盖率，沙箱。

*当使用 Jest 测试 React 或 React 本机应用程序时，您可以编写一个* ***快照*** ***测试*** *将渲染组件的输出保存到文件中，并在后续运行时将组件的输出与快照进行比较。这有助于了解组件何时改变其行为。*

## jsdom +摩卡

[jsdom](https://github.com/tmpvar/jsdom) 提供 React 所需的 dom 进行渲染，完全在 node/io.js 中实现浏览器 DOM 实现的合适子集。

## 酶

是一个包装像 **React TestUtils** 、 **JSDOM** 和 **CheerIO** 这样的包的库，为编写单元测试创建一个更简单的接口(使用浅层渲染)。

# 例子

这是使用 E **nzyme** 测试 React 组件的示例中的 [git](https://github.com/wahengchang/react-test-must-know) :

**安装**

```
$ git clone https://github.com/wahengchang/react-test-must-know
$ npm install
```

**运行测试**

```
$ npm test> jest
**PASS ** src/__tests__/**simpleFoo-test.js
PASS ** src/__tests__/**Foo-test.js****Test Summary** › Ran all tests.
**› 7 tests passed** (7 total in 2 test suites, run time 2.08s)
```

## **应该知道**

**1。组件中状态/属性的验证值**

```
const wrapper = shallow(<ComponentName />);expect(wrapper**.state()**.data).toBe('something');
expect(wrapper**.props()**.data).toBe('something');
```

***2。组件中标签的校验值***

```
const wrapper = shallow(<ComponentName />);expect(wrapper**.find('h4')**.length).toBe(1);
expect(wrapper**.find('h4')**.at(0).text()).toBe('Something');
```

***3。模拟按钮*的点击事件**

```
const wrapper = shallow(<ComponentName />);expect(wrapper.state().data).toBe('state1');
**wrapper.find('button').simulate('click');** expect(wrapper.state().data).toBe('state2');
```

# 欺骗

## 断言风格( [*出自*](https://stackoverflow.com/questions/40795850/how-to-test-style-for-a-react-component-attribute-with-enzyme) )

```
wrapper.find('.myClassname').get(0).style;
expect(containerStyle).to.have.property('opacity', '1');
```

## 断言类名([到](https://stackoverflow.com/questions/40795850/how-to-test-style-for-a-react-component-attribute-with-enzyme))

```
const span = mount(<Test />).find('span');
expect(span.html().match(/style="([^"]*)"/i)[1]).toBe('color: #000;');
```

# *备注

## 浅层渲染

将组件作为一个单元进行测试，

```
import { shallow } from 'enzyme';

const wrapper = **shallow**(<MyComponent />);
```

## 完全渲染

可能**与 DOM API**交互的组件，或者可能需要整个生命周期来全面测试组件(即`componentDidMount`等)。)

```
import { mount } from 'enzyme';

const wrapper = **mount**(<MyComponent />);
```

## 静态渲染

用于将 react 组件渲染到 ***静态 HTML*** 中，并分析生成的 HTML 结构。

```
import { render } from 'enzyme';

const wrapper = **render**(<MyComponent />);
```

## 酶.包装

包装器指的是提供 API 的酶包装器类(更多)。

```
wrapper.**find**(Foo)
wrapper.**find**('.icon-star')
wrapper.**find**('button').simulate('click')
wrapper.**contains**(<div className="unique" />)
wrapper.**props**().data
wrapper.**state**().data
```

# 参考:

[https://github.com/wahengchang/react-test-must-know](https://github.com/wahengchang/react-test-must-know)

[http://blog . Ian Nelson . systems/12-reasons-why-I-love-unit-tests/](http://blog.iannelson.systems/12-reasons-why-i-love-unit-tests/)

[https://github . com/Facebook/jest/blob/master/examples/snapshot/link . react . js](https://github.com/facebook/jest/blob/master/examples/snapshot/Link.react.js)

【https://github.com/facebook/jest】

[https://facebook.github.io/jest/docs/tutorial-react.html](https://facebook.github.io/jest/docs/tutorial-react.html)

[https://github . com/Facebook/jest/tree/master/examples/snapshot](https://github.com/facebook/jest/tree/master/examples/snapshot)

[https://www . code mentor . io/vijayst/unit-testing-react-components-jest-or-enzyme-du 1087 LH 8](https://www.codementor.io/vijayst/unit-testing-react-components-jest-or-enzyme-du1087lh8)

[https://hacker noon . com/testing-react-components-with-jest-and-enzyme-41d 592 c 174 f # . d3lo 5 wnl 5](https://hackernoon.com/testing-react-components-with-jest-and-enzyme-41d592c174f#.d3lo5wnl5)

[http://airbnb.io/enzyme/docs/api/shallow.html](http://airbnb.io/enzyme/docs/api/shallow.html)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)