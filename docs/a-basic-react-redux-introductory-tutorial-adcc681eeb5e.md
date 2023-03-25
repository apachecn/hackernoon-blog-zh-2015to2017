# 一个基本的 React + Redux 入门教程

> 原文：<https://medium.com/hackernoon/a-basic-react-redux-introductory-tutorial-adcc681eeb5e>

在本教程中，我们将使用 React 和 Redux 创建一个联系人列表。我们将有一个联系人列表，一旦我们点击其中一个，将显示该联系人的详细信息。

这将为我们理解一个 *React+Redux* 应用程序的关键和基本组件提供一个借口，这个例子是基于 Stephen Grider 在 [Udemy](https://www.udemy.com/react-redux/learn/v4/content) 上的 Modern React with Redux 课程中的书籍例子。我确实鼓励任何人购买它。

代码也从他的 [React Simple Starter](https://github.com/StephenGrider/ReduxSimpleStarter) 项目开始

**注**:喜欢语法着色吗？试试这里的[要点](https://gist.github.com/miguelsaddress/da47c98fa5fbafee5eddc22434d82a39)或者检查这里的[回购](https://github.com/miguelsaddress/Contact-List-React-Redux)

让我们开始:

React+Redux 应用程序的**关键组件**如下:

1.  还原剂
2.  容器
3.  动作(和动作创建者)

# 1.还原剂

缩减器是一个返回应用程序状态的函数

假设应用程序状态如下

```
state = {     contacts: [{ "name": "Miguel", "phone": "123456789", },{ "name": "Peter", "phone": "883292300348", },{        "name": "Jessica", "phone": "8743847638473",    },{         "name": "Michael",         "phone": "0988765553", }],   activeContact: { "name": "Miguel", "phone": "123456789", }}
```

我们需要**两个减速器**，一个用于*触点*，另一个用于*有源触点*

例如，我们的联系人列表可以是应用程序中的一个状态，保存该列表的完全有效(但静态)的缩减器应该是:

```
export default function () { return [{ "name": "Miguel", "phone": "123456789", },{ "name": "Peter", "phone": "883292300348", },{ "name": "Jessica", "phone": "8743847638473", },{ "name": "Michael", "phone": "0988765553", }];}
```

我们必须有一个*“root Reducer”*，它是我们应用程序的所有 reducer 的组合(我们的应用程序状态/ *Redux 存储*)。为此， *Redux* 提供了*组合减压器*功能:

```
// reducers/index.jsimport { combineReducers } from 'redux';import ContactsReducer from './reducer_contacts'import ActiveContactReducer from './reducer_active_contact'const rootReducer = combineReducers({ contacts: ContactsReducer, activeContact: ActiveContactReducer});export default rootReducer;
```

我们如何将该状态连接到一个*反应*组件中？我们需要 ReactRedux 包，它有助于我们连接 React 和 Redux。怎么会？**容器**

# 2.容器

容器包装了一个 *React* 组件，并向他提供了我们需要传递给它的状态，作为组件内部的 *props* 。

哪些组件应该是容器/智能组件，哪些应该是哑组件？**当一个组件需要从我们的 *Redux 存储*中了解某个特定的状态时，那么它就需要成为一个容器。**

要将一个组件与 *Redux Store* 连接起来，我们需要从 react-redux 库中导入 connect 函数，并向其传递一些要描述的参数。

让我们假设在一个名为 ContactList 的组件中有一个联系人列表，我们需要将它连接到 *Redux Store* 。

```
import React, {Component} from 'react'import {connect} from 'react-redux'import selectContact from '../actions/action_select_contact'import {bindActionCreators} from 'redux'class ContactList extends Component { renderList() { return this.props.contacts.map((contact) => { return ( <li key={contact.phone} onClick={() => this.props.selectContact(contact)} className='list-group-item'>{contact.name}</li> ); }); } render() { return ( <ul className = 'list-group col-sm-4'> {this.renderList()} </ul> ); }} function mapStateToProps(state) { return { contacts: state.contacts };}export default connect(mapStateToProps[,...later...])(ContactList)
```

注意 mapStateToProps 函数将如何接收 *Redux Store* 的**整体**状态，我们必须从中选择对我们来说**感兴趣的部分，返回一个将被用作 ContactList 道具的对象。如果我们返回下面的对象**

```
function mapStateToProps(state) { return { amazingContacts: state.contacts  }
}
```

一旦连接到联系人列表，就可以通过 this.props.amazingContacts 访问

# 3.行动

如果你仔细考虑这个组件，你会意识到一些非常重要的事情:**我们只是在读取**，但是我们没有办法修改组件的状态。为此，我们需要**动作创建者**来生成动作。然后，这些动作将被传递给合并到我们的 rootReducer 中的所有 Reducer，它们中的每一个都将根据请求的动作返回一个修改(或未修改)状态的新副本。我们的 reducer 现在看起来像这样(注意默认情况，因为我们总是必须返回一个状态

```
// reducer_active_contact.jsexport default function(state = null, action) { switch (action.type) { case 'CONTACT_SELECTED': return action.payload }  // i dont care about the action because it is not inside my // list of actions I am interested int (case statements inside the switch) return state}
```

我们还有工作要做，我们必须创建动作创建者，这是一个函数的*花哨的*名称，该函数返回一个包含两个元素的对象:*类型*和*有效负载*。在这两个元素中，唯一必需的元素(包括名称类型)是类型。

```
// actions/action_select_contact.jsfunction selectContact(contact) { return { type: 'CONTACT_SELECTED', payload: contact }}export default selectContact;
```

现在我们可以有一个创建动作的函数，但是这些动作必须流经 *Redux* 。我们如何让动作流过*Redux reducer*？通过**连接**功能！那是秘密函数的参数

mapDispatchToProps:

```
// ... some other imports...import selectContact from '../actions/action_select_contact'import {bindActionCreators} from 'redux'// The ContactList component goes here// mapStateToProps is herefunction mapDispatchToProps(dispatch) { return bindActionCreators({selectContact: selectContact}, dispatch);}export default connect(mapStateToProps, mapDispatchToProps)(ContactList)
```

这个函数确保无论何时触发 selectContact 动作，它返回的任何内容都将流经所有的 reducers。如果我们有更多的动作，它会为每个动作做同样的事情。请注意，名称可能与对象不同:

```
function mapDispatchToProps(dispatch) { return bindActionCreators({ myAction1: action1, myAction2: action2 }, dispatch);}
```

现在，我们在 ContactList 组件中的 props 也将包含我们连接的动作(selectContact，myAction1，myAction2…)

ContactList 组件的结果如下所示:

```
import React, {Component} from 'react'import {connect} from 'react-redux'import selectContact from '../actions/action_select_contact'import {bindActionCreators} from 'redux'class ContactList extends Component { renderList() { return this.props.contacts.map((contact) => { return ( <li key={contact.phone} onClick={() => this.props.selectContact(contact)} className='list-group-item'>{contact.name}</li> ); }); } render() {    return ( <ul className = 'list-group col-sm-4'> {this.renderList()} </ul> ); }}function mapStateToProps(state) { return { contacts: state.contacts };}function mapDispatchToProps(dispatch) { return bindActionCreators({
    selectContact: selectContact
  }, dispatch);}export default connect(mapStateToProps, mapDispatchToProps)(ContactList)
```

接下来的步骤是:

1.  减压器内部(注意复数)根据收到的动作类型采取动作
2.  更新状态(这将触发受影响组件的重新渲染)

在我们的例子中，唯一关心所选联系人的 reducer 是 ActiveContactReducer，我们还没有创建它，但是我们现在将要创建它。

减速器接收两个参数:它们关心的状态片段(不是整个状态)和流经减速器的电流动作。记住，它们返回该状态的一个新的**副本**，该副本根据接收到的动作进行修改。

在这种情况下，activeContact 状态将被更新为 action.payload 中包含的 Contact。无论函数返回什么，都将按原样分配给 reducer 正在处理的状态。缩减器必须总是返回一些东西:

```
//reducer_active_contactexport default function (state = null, action) { switch (action.type) { case 'CONTACT_SELECTED': return action.payload } return state; }
```

现在，为了显示一个或另一个联系人，我们需要一个<contactdetail>容器(记住，它是一个容器，因为它关心 *Redux 存储的一部分状态)。*</contactdetail>

所以，我们现在必须:

1.  创建一个<contactdetail>组件</contactdetail>
2.  将组件连接到 *Redux Store*
3.  将 reducer 添加到 rootReducer

一旦完成，<contactdetail>组件将最后显示。</contactdetail>

要将组件连接到 *Redux Store* ，与之前相同:mapStateToProps 函数作为参数与组件一起传递给 connect 函数，并导出容器。

```
// inside containers/contact-detail.jsimport { connect } from 'react-redux'// ... ContactDetail component here ...function mapStateToProps(state) { return { contact: state.activeContact 
    //activeContact is defined in the rootReducer }}export default connect(.....)
```

reducer 以与 ContactReducer 相同的方式添加到 rootReducer 中

```
import { combineReducers } from 'redux';import ContactsReducer from './reducer_contacts'import ActiveContactReducer from './reducer_active_contact'const rootReducer = combineReducers({ contacts: ContactsReducer, activeContact: ActiveContactReducer});export default rootReducer;
```

# 流程摘要

现在一切都连接好了，所以单击列表中的一个联系人应该会触发 selectContact action creator，它将被传递给 rootReducer 中的每个 reducer。ActiveContactReducer 将通过改变它所控制的状态( *activeContact* )来对该动作作出反应，将它设置为 action.payload 中的 selectedContact，当这种情况发生时，< ContactDetail >将看到它的状态被修改，并将被重新呈现，为 this.props.contact 提供一个新值。

<contactdetail>将如下所示:</contactdetail>

```
import React, { Component } from 'react'import { connect } from 'react-redux'class ContactDetail extends Component { render() { if (!this.props.contact) { return ( <div>Select a contact from the list to see its details</div> ); }    return ( <div> <h3>Contact Details for: {this.props.contact.name}</h3> <div>Phone: {this.props.contact.phone}</div> </div> ); }}function mapStateToProps(state) { return { contact: state.activeContact }}export default connect(mapStateToProps)(ContactDetail);
```

# 决赛成绩

最终的结果并不令人惊讶，但它是有效的，你可以在这张 GIF 图片中看到:

![](img/9102721d7d068e074832281093ec831b.png)

The Contacts Application in action

# 所有的东西是如何连接在一起的？

好了，这一切就像魔术一样…我的应用程序最初是如何知道我的减速器在哪里和在哪里的？(请记住，状态是通过组合所有的归约器和组合归约器来定义的)

这是在我们的主文件中完成的，在这里我们使用<provider>组件定义应用程序，该组件接收存储，使它对它包装的组件可用。</provider>

为了传递存储(记住，这是 rootReducer ),我们将把它与 applyMiddleware 函数结合起来，该函数将保存我们的中间件(当我们需要它们时),并在到达应用程序之前通过它们传递*状态*。

```
import React from 'react';import ReactDOM from 'react-dom';import { Provider } from 'react-redux';import { createStore, applyMiddleware } from 'redux';import App from './components/app';import reducers from './reducers'; // this exports rootReducer!!!const createStoreWithMiddleware = applyMiddleware()(createStore);ReactDOM.render( <Provider store={createStoreWithMiddleware(reducers)}> <App /> </Provider>, document.querySelector('.container'));
```

❤如果你觉得这个有用，可以考虑给我买杯❤咖啡

[https://www.buymeacoffee.com/mamoreno](https://www.buymeacoffee.com/mamoreno)