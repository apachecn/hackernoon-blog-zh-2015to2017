# 如何使用 React 和 Redux 构建便笺应用程序

> 原文：<https://medium.com/hackernoon/how-to-build-a-sticky-notes-app-using-react-and-redux-4bcc1fa01218>

![](img/dc7422232bbe82042c67a1b23dfd84af.png)

在本教程中，我将向您展示如何使用 React、Redux、选择器、Redux Sagas、一点点 Node 和 [Cosmic JS](https://cosmicjs.com) 创建一个简单的便笺应用程序。为了理解如何使用 Restful API，本教程将展示如何向 Cosmic JS API 发出 AJAX (XHR)请求，以便在我们的 Cosmic JS 桶中检索、添加、更新和删除数据/媒体。让我们开始吧。

# TL；速度三角形定位法(dead reckoning)

[下载 GitHub 回购](https://github.com/cosmicjs/sticky-notes)
试玩

```
{
  "name": "sticky-notes",
  "version": "1.0.0",
  "description": "Sticky Notes app built using react, redux & cosmic.",
  "main": "index.js",
  "scripts": {
    "start": "npm run build; node server/index.js",
    "start-dev": "nodemon server/index.js",
    "build": "webpack -p",
    "build-dev": "webpack -w",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/jazibsawar/sticky-notes.git"
  },
  "author": "Muhammad Musa",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/jazibsawar/sticky-notes/issues"
  },
  "homepage": "https://github.com/jazibsawar/sticky-notes#readme",
  "dependencies": {
    "async": "^2.5.0",
    "babel": "^6.23.0",
    "babel-core": "^6.25.0",
    "babel-loader": "^7.1.1",
    "babel-plugin-transform-class-properties": "^6.24.1",
    "babel-plugin-transform-regenerator": "^6.24.1",
    "babel-polyfill": "^6.23.0",
    "babel-preset-es2015": "^6.24.1",
    "babel-preset-react": "^6.24.1",
    "babel-preset-stage-0": "^6.24.1",
    "body-parser": "^1.17.2",
    "cosmicjs": "^2.39.91",
    "express": "^4.15.3",
    "immutable": "^3.8.1",
    "moment": "^2.18.1",
    "prop-types": "^15.5.10",
    "react": "^15.6.1",
    "react-addons-css-transition-group": "^15.6.0",
    "react-addons-transition-group": "^15.6.0",
    "react-color": "^2.13.4",
    "react-dom": "^15.6.1",
    "react-modal-dialog": "^4.0.7",
    "react-redux": "^5.0.5",
    "react-router": "^3.0.5",
    "react-spinners": "0.0.30",
    "redux": "^3.7.2",
    "redux-logger": "^3.0.6",
    "redux-saga": "^0.15.6",
    "redux-thunk": "^2.2.0",
    "reselect": "^3.0.1",
    "styled-components": "^2.1.1",
    "volleyball": "^1.4.1",
    "webpack": "^3.4.1",
    "webpack-livereload-plugin": "^0.11.0",
    "whatwg-fetch": "^2.0.3"
  },
  "devDependencies": {
    "chai": "^4.1.0",
    "cross-env": "^5.0.1",
    "mocha": "^3.5.0",
    "nodemon": "^1.11.0"
  }
}
```

# 我们正在安装什么，为什么:

1.  我们将使用 **whatwg-fetch** 和 **cosmicjs** 库来处理我们对 Cosmic JS Bucket 的请求。
2.  我们正在安装 **react** 和 **react-dom** 来构建我们的 react 组件。
3.  我们将使用 **redux** 、react-redux、redux-logger、reselect 和 redux-sagas 来帮助我们实现所谓的 redux 架构。
4.  我们使用了**样式组件**来使 stickynotes 组件样式通用化。我们也可以在其中传递样式变量。
5.  我们使用**反应旋转器**来使用加载组件。
6.  我们使用**反应颜色**来使用颜色选择器组件。
7.  我们使用 **react-modal** -dialog 来使用 react 模态组件。
8.  我们使用脸书的**不可变**包来使用不可变对象。
9.  我们使用**时刻**来格式化日期。
10.  我们使用 **react-router** 进行路由和导航。
11.  开发依赖中唯一值得一提的是 **webpack** 和**排球**。Webpack 将帮助我们将所有的 react 和 redux 文件捆绑到一个大的“捆绑”文件中，该文件将在我们的 index.html 中使用。排球是一个很酷的小库，它允许我们在控制台中看到传入和传出的 HTTP 请求。

# 构建我们的应用程序:

现在，我们将在客户目录中设置我们的 index.html。将以下代码复制并粘贴到您的 index.html 文件中:

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Cosmic Sticky Notes App!</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
    <link href="https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css" rel="stylesheet" >
    <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.4/js/bootstrap.min.js"></script>
    <script src="/dist/bundle.js" defer></script>
</head>
<body id="top">
<div id="root"></div>
</body>
</html>
```

这里，我们将目标定在我们的**根** div，以便稍后放置我们的 react 组件。位于我们的 dist 目录中的 **bundle.js** 文件是我们的 webpack.config 文件在捆绑了我们所有的 react 组件后将显示的内容。现在，设置我们的 webpack.config 文件来捆绑我们所有的 react 文件，并将该捆绑文件导出到我们的 dist 目录。将以下代码复制到您的 **webpack.config.js** 文件中:

```
var path = require('path');
var LiveReloadPlugin = require('webpack-livereload-plugin');
var webpack = require('webpack');module.exports = {
  entry: './client/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'client/dist')
  },
  context: __dirname,
  resolve: {
    extensions: ['.js', '.jsx', '.json', '*']
  },
  module: {
    rules: [{
        test: /\.jsx?$/,
        exclude: /(node_modules|bower_components)/,
        loader: 'babel-loader',
        options: {
          presets: ['react', 'es2015', 'stage-0']
        }
      },
    ]
  },
  plugins: [
    new webpack.DefinePlugin({
      'process.env': {
        'COSMIC_BUCKET': JSON.stringify(process.env.COSMIC_BUCKET),
        'COSMIC_READ_KEY': JSON.stringify(process.env.COSMIC_READ_KEY),
        'COSMIC_WRITE_KEY': JSON.stringify(process.env.COSMIC_WRITE_KEY)
      }
    }),
    new LiveReloadPlugin({appendScriptTag: true}),
  ]
};
```

然后，当我们使用 **express** 制作我们的服务器时，我们有了 **server/index.js** 文件。

在客户端文件夹中有 store.js 文件，该文件创建 redux 存储来管理状态和数据。正在发生的事情如下:

*   我们使用了 redux-thunk 和 redux-sagas 中间件。
*   我们使用组合减速器来组合主减速器和组减速器
*   然后我们用上面的东西做了存储。
*   然后我们触发家庭和团体的传奇。

```
import { createStore, applyMiddleware, combineReducers } from 'redux';
import homeReducer from './containers/Home/reducer';
import homeSagas from './containers/Home/sagas'; import groupReducer from './containers/Group/reducer';
import groupSagas from './containers/Group/sagas';import thunkMiddleware from 'redux-thunk';
import createSagaMiddleware from 'redux-saga';const sagaMiddleware = createSagaMiddleware();const middlewares = [
  sagaMiddleware,
  thunkMiddleware,
];const reducers = combineReducers({
  home: homeReducer,
  group: groupReducer,
});
const store = createStore(
  reducers,
  applyMiddleware(...middlewares),
);sagaMiddleware.run(homeSagas)
sagaMiddleware.run(groupSagas)export default store;
```

现在我们有了 client/Routes.js。这里，我们有两条路由:

1.  Path="/" = >列出所有组
2.  path = "/group/:group slug/:groupId " = >列出某些组的所有笔记

```
import React from 'react';
import { Router, Route, browserHistory } from 'react-router';
import { connect } from 'react-redux';
import Home from './containers/Home';
import Group from './containers/Group';const Routes = ({}) => {
  return (
    <Router history={browserHistory}>
        <Route path="/" component={Home} />
        <Route path="/group/:groupSlug/:groupId" component={Group} />
    </Router>
  )
};// const mapState = ({}) => ({});
// const mapDispatch = {};export default connect(null, null)(Routes);
```

在客户端文件夹中，我们创建了一个配置文件来保存 API_KEYS 和 universal BUCKET 名称。你可以编辑这个文件来使用你自己的键和 slugs。

```
export default {
  bucket: {
    slug: process.env.COSMIC_BUCKET || 'sticky-note-app',
    read_key: "YOUR READ KEY",
    write_key: "YOUR WRITE KEY",
  }
}
```

# 有两种实用工具:

*   请求
*   宇宙的

## 请求

Request 使用 whatwg-fetch 将 AJAX 请求发送到 Cosmic JS API。此实用程序用于调用那些不在 Cosmic JS 包中的端点，例如基于过滤器的删除媒体和搜索对象

## 宇宙 JS

cosmicjs 使用 Cosmic JS 包向 Cosmic API 打 AJAX 请求。

# 有两个容器:

*   主页
*   组

## 主页

该容器列出了向下的组。该组添加新的注释组。它还编辑和删除注释组。这将从 reducers 中选择 NoteGroup 数据，并将它们传递给它们的组件。这也将添加、编辑和删除 NoteGroup 操作传递给 home 组件。

## 组

这将从减速器中选择 Notes 数据，并将它们传递给它们的组件。这也将添加、编辑和删除注释操作传递给 home 组件。

# 此应用程序中有以下组件

*   **拾色器**
*   **对话框**
*   **装载机**
*   **便利贴**
*   **首页**
*   **组**

## 颜色选择器

该组件用于选择便笺的颜色。

```
import React, {Component} from 'react';
import { ChromePicker } from 'react-color'; class ColorPickerField extends Component {
  constructor(props){
    super(props);
  } render() {
    return (
      <ChromePicker
        color={this.props.color}
        onChangeComplete={this.props.changeColor}
      />
    )
  }
}export default ColorPickerField;
```

## 对话

该组件将显示弹出窗口和对话框，以获取输入数据和输出响应

```
import React, {Component} from 'react';
import { ModalContainer, ModalDialog } from 'react-modal-dialog'; class Dialog extends Component {
  constructor(props){
    super(props);
  }
 handleClose = () => {
    this.props.closeDialog();
  }
  render() {
    const { open } = this.props;
    return (
      <div>
        {
          open &&
          <ModalContainer onClose={this.handleClose}>
            <ModalDialog onClose={this.handleClose}>
              { this.props.children }
            </ModalDialog>
          </ModalContainer>
        }
      </div>
    )
  }
}export default Dialog;
```

## 装货设备

这是一个加载器组件，每当 ajax 请求发生时就会触发。

```
import React, {Component} from 'react';
import { BounceLoader } from 'react-spinners'; class Loader extends Component {
  constructor(props){
    super(props);
  }
 render() {
    const style = {
      position: "fixed",
      top: "50%",
      left: "50%",
      marginTop: "-9em",
      marginLeft: "-15em",
    };
    return (
      <div style={style}>
        <BounceLoader
          color={'#123abc'}
          loading
          size={300}
        />
      </div>
    )
  }
}export default Loader;
```

## 粘扣带

该组件接受一组选项，并使用编辑、标题 onclick 和删除处理程序将它们转换为 stickynotes。

```
import React, {Component} from 'react';
import { NoteListWrapper, NoteWrapper, Note } from './styles';
import moment from 'moment/moment'
class StickyNotes extends Component {
 render() {
    const { options } = this.props;
    return (
      <NoteListWrapper>
        {
          !!options && options.toArray().map((option, index) => {
          return  <NoteWrapper key={`note_${index}`}>
              <Note backgroundColor={option.getIn(['metadata', 'color'])||"black"} color="#ffffff" rotate={index%2 === 0? -2 : 4}>
                <small>{moment(option.get('created')).fromNow()}</small>
                <h4 onClick={() => this.props.handleClick(option)}>{option.get('title').substring(0,11)}</h4>
                <p>{option.get('content').substring(0, 41)}</p>
                <span className="buttons">
                  <a onClick={() => this.props.deleteOption(option.toJS(), index)} className="pull-right"><i className="fa fa-trash-o"></i></a>
                  <a onClick={() => this.props.editOption(option, index)} style={{ marginRight: "1vw" }} className="pull-right"><i className="fa fa-pencil"></i></a>
                </span>
              </Note>
            </NoteWrapper>
          })
        }
      </NoteListWrapper>
    )
  }
}export default StickyNotes;
```

## 主页

这是一个组件，我们将 NoteGroup 选项提供给 Sticky Notes 组件，并具有添加、编辑和删除处理程序。

```
import React, {Component} from 'react';
import Dialog from '../Dialog';
import StickyNotes from '../StickyNotes';
import ColorPicker from '../ColorPicker';class Home extends Component {
  constructor(props){
    super(props);
    this.state = {
      title: "",
      content: "",
      color: "#fff",
      selectedGroup: null,
      openAddDialog: false,
      openEditDialog: false,
      group: {
        title: "",
      },
    }
  } addGroup = () => {
    const { title, content, color } = this.state;
    this.props.addGroup({
      title,
      content,
      color,
    });
    this.setState({ title: "", content: "", openAddDialog: false })
  } editGroup = () => {
    const { group, selectedGroup } = this.state;
    const { state } = this;
    this.props.editGroup({
      title: group.title,
      content: group.content,
      color: group.color,
    }, group.slug, selectedGroup);
    this.setState({ ...state, group: { title: "" }, openEditDialog: false })
  } goToNoteGroup = (group) => {
    this.props.changeRoute(`/group/${group.get('slug')}/${group.get('_id')}`);
  } editOption = (group, selectedGroup) => {
    this.setState({
      openEditDialog: true,
      group: group.toJS(),
      selectedGroup,
    });
  }
  render() {
    const { groups } = this.props;
    const { state } = this;
    const { title, content, color, openAddDialog, openEditDialog, group } = this.state; const styles = {
      btnCircle: {
        width: "30px",
        height: "30px",
        textAlign: "center",
        padding: "6px 0",
        fontSize: "12px",
        fontWeight: "700",
        lineHeight: "1.42",
        borderRadius: "15px",
        margin: "10px",
        border: "none",
        outline: "none",
        float: "right"
      }
    } return (
      <div className="container-fluid">
      <div className="row">
        <button style={styles.btnCircle} className="btn btn-primary btn-lg" onClick={() => this.setState({ openAddDialog: true })}>
          <i className="fa fa-plus"></i>
        </button>
      <Dialog
        open={openAddDialog}
        closeDialog={() => this.setState({ openAddDialog: false })}
      >
        <div className="container-fluid">
          <div className="col-xs-12">
            <input placeholder="Enter Name ..." type="text" style={{ margin: "1vh 0" }} value={title} className="form-control" onChange={(e) => this.setState({ title: e.target.value })} />
          </div>
          <div className="col-xs-12">
            <textarea value={content} placeholder="Enter Description ..." rows="4" cols="50" style={{ margin: "1vh 0" }} className="form-control" onChange={(e) => this.setState({ content: e.target.value })} />
          </div> <div className="col-xs-12">
            <ColorPicker color={color} changeColor={(color) => this.setState({ color: color.hex })} />
          </div> <div className="col-xs-12">
            <input type="button" disabled={title === "" && "disabled"} style={{ margin: "1vh 0" }} value="Add Group" className="btn btn-primary btn-lg" onClick={this.addGroup} />
          </div>
        </div>
      </Dialog> <Dialog
        open={openEditDialog}
        closeDialog={() => this.setState({ openEditDialog: false })}
      >
        <div className="container-fluid">
          <div className="col-xs-12">
            <input style={{ margin: "1vh 0" }} type="text" value={group.title} className="form-control" onChange={(e) => this.setState({ ...state, group: {  ...this.state.group, title: e.target.value } })} />
          </div>
          <div className="col-xs-12">
            <textarea value={group.content||""} style={{ margin: "1vh 0" }} type="text" className="form-control" onChange={(e) => this.setState({ ...state, group: {  ...this.state.group, content: e.target.value } })} />
          </div>
          <div className="col-xs-12">
            <ColorPicker color={group.color} changeColor={(color) => this.setState({ ...state, group: { ...this.state.group, color: color.hex }})} />
          </div>
          <div className="col-xs-12">
            <input disabled={group.title === "" && "disabled"} style={{ margin: "1vh 0" }} type="button" value="Edit Group" className="btn btn-warning btn-lg" onClick={this.editGroup} />
          </div> </div>
      </Dialog> {
        <StickyNotes
          options={groups}
          editOption={this.editOption}
          deleteOption={this.props.deleteGroup}
          handleClick={this.goToNoteGroup}
        />
      }
      </div>
      </div>
    )
  }
}export default Home;
```

## 组

这是一个组件，我们在其中为 StickyNotes 组件提供 Notes 选项，并具有添加、编辑和删除处理程序。

```
import React, {Component} from 'react';
import Dialog from '../Dialog';
import StickyNotes from '../StickyNotes';
import ColorPicker from '../ColorPicker';class Group extends Component {
  constructor(props){
    super(props);
    this.state = {
      title: "",
      content: "",
      color: "",
      selectedNote: null,
      openAddDialog: false,
      openEditDialog: false,
      openViewDialog: false,
      note: {
        title: "",
      },
    }
  } addNote = () => {
    const { title, color, content } = this.state;
    const { groupId, addedMedia } = this.props;
    const image = this.refs.imageFile.files[0];
    const file = this.refs.attachedFile.files[0];
    this.props.addNote({
      title,
      content,
      color,
      image,
      file,
    }, groupId);
    this.setState({ title: "", content: "", openAddDialog: false });
  } editNote = () => {
    const { note, selectedNote } = this.state;
    const { state } = this;
    let METAFIELDS = note;
    METAFIELDS.metafields[1].value = note.color || "#000"
    this.props.editNote({
      title: note.title,
      color: note.color || "#000",
      content: note.content,
      metafields: METAFIELDS.metafields,
    }, note.slug, selectedNote);
    this.setState({ ...state, note: { title: "" }, openEditDialog: false })
  } editOption = (note, selectedNote) => {
    this.setState({
      openEditDialog: true,
      note: note.toJS(),
      selectedNote,
    });
  } goToNote = (note) => {
    this.setState({
      note: note.toJS(),
      openViewDialog: true,
    })
  }
  render() {
    const { notes } = this.props;
    const { state } = this; const { title, content, color, openAddDialog, openEditDialog, openViewDialog, note } = this.state; const styles = {
      btnCircle: {
        width: "30px",
        height: "30px",
        textAlign: "center",
        padding: "6px 0",
        fontSize: "12px",
        fontWeight: "700",
        lineHeight: "1.42",
        borderRadius: "15px",
        margin: "10px",
        border: "none",
        outline: "none",
        float: "right"
      }
    } return (
      <div className="container-fluid">
      <button style={styles.btnCircle}  onClick={() => this.setState({ openAddDialog: true })} className="btn btn-primary btn-lg">
        <i className="fa fa-plus"></i>
      </button>
      <Dialog
        open={openAddDialog}
        closeDialog={() => this.setState({ openAddDialog: false })}
      >
        <div className="container-fluid">
          <div className="col-xs-12">
            <input style={{ margin: "1vh 0" }} type="text" className="form-control" value={title} onChange={(e) => this.setState({ title: e.target.value })} /> <br />
          </div>
          <div className="col-xs-12">
            <textarea style={{ margin: "1vh 0" }} type="text" className="form-control" value={content} onChange={(e) => this.setState({ content: e.target.value })} /> <br />
          </div>
          <div className="col-xs-12">
            <input style={{ margin: "1vh 0" }} type="file" className="form-control" ref="imageFile" /> <br />
          </div>
          <div className="col-xs-12">
            <input style={{ margin: "1vh 0" }} type="file" className="form-control" ref="attachedFile" /> <br />
          </div>
          <div className="col-xs-12">
            <ColorPicker color={color} changeColor={(color) => this.setState({ color: color.hex })} />
          </div>
          <div className="col-xs-12">
            <input disabled={(title === "" || content === "") && "disabled"} style={{ margin: "1vh 0" }} type="button" className="btn btn-success btn-md" value="Add Note" onClick={this.addNote} />
          </div>
        </div>
      </Dialog> <Dialog
        open={openEditDialog}
        closeDialog={() => this.setState({ openEditDialog: false })}
      >
        <div className="container-fluid">
          <div className="col-xs-12">
            <input style={{ margin: "1vh 0" }} type="text" value={note.title} className="form-control" onChange={(e) => this.setState({ ...state, note: {  ...this.state.note, title: e.target.value } })} />
          </div>
          <div className="col-xs-12">
            <textarea style={{ margin: "1vh 0" }} type="text" value={note.content||""} className="form-control" onChange={(e) => this.setState({ ...state, note: {  ...this.state.note, content: e.target.value } })} />
          </div>
          <div className="col-xs-12">
            <ColorPicker color={note.color} changeColor={(color) => this.setState({ ...state, note: { ...this.state.note, color: color.hex }})} />
          </div>
          <div className="col-xs-12">
            <input disabled={(note.title === "" || note.content === "") && "disabled"} style={{ margin: "1vh 0" }} type="button" value="Edit Note" className="btn btn-warning btn-lg" onClick={this.editNote} />
          </div>
        </div>
      </Dialog> <Dialog
        open={openViewDialog}
        closeDialog={() => this.setState({ openViewDialog: false })}
      >
        <div className="container-fluid">
          <div className="col-xs-12">
            <h1 style={{ margin: "1vh 0" }}>{note.title}</h1>
          </div>
          <div className="col-xs-12">
            <p style={{ margin: "1vh 0" }}>{note.content}</p>
          </div>
          {
            !!note.metadata && !!note.metadata.feature_image &&
            <div className="col-xs-12">
              <img style={{ margin: "1vh 0" }} width="128" height="128" src={note.metadata.feature_image.imgix_url} />
            </div>
          }
          {
            !!note.metadata && !!note.metadata.attachment &&
            <div className="col-xs-12">
              <a style={{ margin: "1vh 0" }} href={note.metadata.attachment.imgix_url} target="_blank">Click on this link to open attachment in the new link</a>
            </div>
          }
        </div>
      </Dialog> <StickyNotes
        options={notes}
        editOption={this.editOption}
        deleteOption={this.props.deleteNote}
        handleClick={this.goToNote}
      />
      </div>
    )
  }
}export default Group;
```

# 结论

因此，这是一个涵盖了 Cosmic JS RESTful API 所有场景的应用程序。我希望你喜欢这个教程。

> 这篇文章最初出现在[宇宙 JS 博客](https://cosmicjs.com/blog/how-to-build-a-sticky-notes-app-using-react-and-redux)上。