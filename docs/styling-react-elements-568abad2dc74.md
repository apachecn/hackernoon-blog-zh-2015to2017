# 样式反应元素

> 原文：<https://medium.com/hackernoon/styling-react-elements-568abad2dc74>

我的初级开发人员系列的第四部分。关于我，我来自非传统的计算机科学背景，是一名活动家，倡导为技术空间的多样性和包容性而奋斗。我写关于前端开发、 [JavaScript](https://hackernoon.com/tagged/javascript) 、 [ReactJS](https://hackernoon.com/tagged/reactjs) 和技术包容的技术主题的博客。

## 谁说 CSS 很简单，谁就是疯了…或者已经写了很长时间了。

![](img/f09cd40ef90ad0950552ef7729066e97.png)

我将向你展示 3 种方法来设计你的 React 组件。

方法 1

第一种方法是将样式内联到组件中。

```
<input style={{backgroundColor: 'blue'}} type="text" />
```

Camel Case 你的 CSS 属性，然后赋予它一个字符串值。

方法 2

第二种方法是将您的样式分配给一个变量，然后将该变量传递给组件。

```
const inputStyle = {
  color: 'blue';
}
```

然后将该变量作为道具传递给组件。

```
<form onSubmit={this.handleSubmit}>
   <label>
     Name:
     <input style={inputStyle} type="text"   
        value={this.state.value} onChange={this.handleChange} />
   </label>
   <input type="submit" value="Submit" />
</form>
```

内联样式化 React 组件的正确语法是

方法 3

第三种方式也是我必须实现的是使用 SASS。这种方法相当简单，但是当您使用模块化代码时，事情可能会超级嵌套。在其父组件中，它被传递了以下属性:`label`、`field`、`id`

```
<**BirthDateInput** label="Date of Birth"
  field={fields.userBirthDate}
  id="userBirthDate"
/>
```

子组件看起来像:

```
<**div** className={formInputContainer}>
  <**label** htmlFor={id}>Date</**label**>
  <**div** className={date}>
    <**div**>
      <**SelectInput** s={s}
        id={`${id}-${DATE_KEY_MONTH}`}
        field={emulateField(DATE_KEY_MONTH)}
        values={getMonths()}
        errorInline={this.props.errorInline}
        funPlaceholder={this.props.funPlaceholder}
        placeholder={this.props.monthPlaceholder}
      />
    </**div**>
```

子组件中有许多无关的代码，但最重要的是要注意它的嵌套。在您单独的 SASS 样式表中，我们可以看到`<SelectInput />`被置于`.birthDate`下。

```
.**birthDate** {
  **div**:**last-child** .**formInputContainer** {
    **select** {
      **color**: 'black*'*;
    }

    **select**[**name**^='userBirthDate'] {
      **color**: 'grey';
    }
  }
}
```

上面 SASS 代码中的第二行允许您将文本放在`<SelectInput />`中。那么为什么我们要在上面设计两次呢？`<SelectInput />`有一个占位符和用户输入的实际文本。

行`select { color: 'black';}`指向用户输入的文本。最后，行`select[name^='userBirthDate'] { color: 'grey';}`指向`<SelectInput />`中的占位符文本。