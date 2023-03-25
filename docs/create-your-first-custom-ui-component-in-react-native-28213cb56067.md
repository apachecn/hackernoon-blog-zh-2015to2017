# 在 React Native 中创建您的第一个自定义 UI 组件

> 原文：<https://medium.com/hackernoon/create-your-first-custom-ui-component-in-react-native-28213cb56067>

![](img/0eb8f62f0dc954812250f9cc3438e3f0.png)

一旦你安装了 [React Native](https://hackernoon.com/tagged/react-native) ，它为你提供了许多内置组件，“视图”是你可以使用的最基本的组件，还有其他组件可以用来构建你的 UI，如文本、图像和其他一些基本组件。

让我们假设你想创建一个有用户反馈的[应用](https://hackernoon.com/tagged/app)，你肯定需要一个“帖子”视图，包含每篇帖子的信息，发帖的用户，他的个人资料图片，然后可能是他的想法的文本。

你可能最终会写出这样的东西来完成它:

```
<TouchableOpacity style={styles.postContainer} onPress={this.props.postPressed}>
  <View style={styles.userInformations}>
    <Image source={{uri: ‘[http://cdn.yourserver.com/alex.jpg'](http://cdn.yourserver.com/alex.jpg')}} /> 
    <Text>Alex DuPont</Text>
  </View>
  <Text style={styles.status}>Hey, What’s up? </Text></TouchableOpacity>
```

我们刚刚创建了我们自己的组件，用 TouchableOpacity 作为主要元素，为帖子添加了一个可点击的动作。

这里的想法是创建一个新的组件来呈现您自己的代码难题，并将其称为 Post 组件。

在您的“components”文件夹下创建一个新文件，我们将在其中定义我们的类 Post:

```
class Post extends Component {constructor(props) {
 super(props)
 }render() {
 return (
 <TouchableOpacity style={styles.postContainer} onPress={this.props.postPressed}>
 <View style={styles.userInformations}>
 <Image source={{uri: ‘this.props.post.userPicture’}} /> 
 <Text>this.props.post.userName</Text>
 </View>
 <Text style={styles.status}>this.props.post.thoughts</Text></TouchableOpacity>
 )
 }}export default Post
```

为了使它具有动态性，我们使用了当前实例的道具。

现在，您可以在任何需要的地方使用 Post 组件，唯一缺少的部分是 Post 数据，您需要在获取一些数据库或调用 API 后将它作为组件的属性传递:

```
import Post from './components/Post.js'...<Post *isMedia*={false} *data*={this.state.post} />
```

快乐编码。😃 💻