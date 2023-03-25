# 为什么我会生锈

> 原文：<https://medium.com/hackernoon/why-im-dropping-rust-fd1c32986c88>

当我看到有一种新的系统级编程语言，提供与 C++类似的性能，并且没有垃圾收集时，我立刻产生了兴趣。虽然我喜欢用 C#或 javascript 之类的 GC 语言解决问题，但我有一种挥之不去的感觉，我正在失去 C++的原始力量。但是 C++有太多的漏洞和其他众所周知的问题，所以我通常会放弃它。

所以我一头扎了进去。好家伙，我潜水很深。

由于 Rust 本身还相当年轻，它的生态系统仍处于发展阶段。对于一些用例来说， [websockets](https://github.com/cyderize/rust-websocket) 或者 [serialization](https://github.com/serde-rs/json) 都有非常好的解决方案，并且很受欢迎。对于其他用例，Rust 还是欠缺的。一个这样的用例是 OpenGL GUI，比如 [CEGUI](http://cegui.org.uk/) 或 [nanogui](https://github.com/wjakob/nanogui) 。为了帮助社区和语言，我选择将 nanogui 移植到 Rust，完全在 Rust 中，没有使用到 C/C++的绑定。这个项目可以在[这里](https://github.com/Oipo/nanogui-rust-sdl)找到。

通常在 Rust 中开始的阶段被称为与借货检查阶段作斗争。和其他人一样，我也有一段时间被如何解决某些问题难住了。幸运的是，在[# rust-初学者](https://client00.chat.mibbit.com/?server=irc.mozilla.org&channel=%23rust-beginners)有一个很棒的社区，他们愿意帮助我解决这些愚蠢的问题。这花了我几个星期的时间，但是我已经开始掌握这门语言了。

但我不知道的是，当你遇到障碍时，寻找解决方案就像在丛林中穿行。通常有多个答案看起来像是你问题的解决方案，但是由于一个小细节，不能使用。

举个例子:假设您有一个基类小部件，并且希望所有实际的小部件(如标签、按钮或复选框)都具有某些易于共享的功能。在 C++或 C#等语言中，这是一个很容易解决的问题。您可以根据语言创建一个抽象类或基类，并在实际的类中继承它。

```
public abstract class Widget {
    private Theme _theme { get; set; }
    private int _fontSize { get; set; }
    public int GetFontSize() {
        return (_fontSize < 0) ? _theme.GetStandardFontSize() : _fontSize;
    }
}
```

在 Rust 中，要做类似的事情，你需要[特征](https://doc.rust-lang.org/book/traits.html)。然而，特征不知道底层的实现。所以一个 trait 可以定义抽象函数，但是不能访问任何底层字段。

```
trait Widget {
    fn font_size(&self) -> i32 {
        if self.font_size < 0 { //compiler error
            return self.theme.get_standard_font_size(); //compiler error
        } else {
            return self.font_size; //compiler error
        }
    }
}// [playground](https://play.rust-lang.org/?gist=e3d55a448df62f9d5172a76c5ecdc425&version=nightly&backtrace=0)
```

让那件事深入你的内心。我对此的第一反应是“对不起，什么？!"。虽然对 OOP 有一些合理的批评，但是把它作为一个解决方案是愚蠢的。

幸运的是，我发现，有一个很好的方法可以通过请求改变来改变语言。我不是唯一一个认为这是语言中严重受限的部分的人，目前有一个 RFC 正在让它变得不那么愚蠢。然而，这至少从 2016 年 3 月就开始了。特征作为一个概念已经成为语言的一部分很多年了。目前是 2016 年 9 月。为什么语言中如此重要和关键的部分仍然缺失？

在某些情况下，您可以通过向 trait 添加一个函数来解决这个问题，这个函数不是在 trait 本身中实现的，而是在一个实际的对象上实现的，然后使用这个函数来完成您的功能。

```
trait Widget {
    fn get_theme(&self) -> Theme;
    fn get_internal_font_size(&self) -> i32;
    fn get_actual_font_size(&self) -> i32 {
        if self.get_internal_font_size() < 0 {
            return self.get_theme().get_standard_font_size();
        } else {
            return self.get_internal_font_size();
        }
    }
}// [playground](https://play.rust-lang.org/?gist=f66f06effd0afa7352e21a25115d892e&version=nightly&backtrace=0)
```

但是现在你有了一个公共函数(特征函数就像一个接口，目前不可能将特征函数标记为 mod-only)，你 ***仍然*** 必须在你所有的具体类型中实现它。因此，要么不使用抽象函数并有大量的代码重复，要么使用示例的设置并有稍微少一些但仍然太多的代码重复和一个泄漏的 API。两者都不可接受。在 C++、C#和 heck 等成熟语言中，这也不是问题，甚至 [Go 也有合适的答案](https://github.com/luciotato/golang-notes/blob/master/OOP.md)。

另一个例子。对于 nanogui，CEGUI 也使用了这个概念，每个小部件都有一个指向父部件的指针和一个指向其子部件的指针向量。这个概念如何映射到 Rust？有几个答案:

1.  使用简单的 Vec <t>实现</t>
2.  使用 Vec
3.  使用 Vec <rc>> ></rc>
4.  使用 C 绑定

从我的旅程开始，我已经尝试了选项 1 到 3，但都有一些缺点，每一个都不适合使用。我目前认为第 4 点是我唯一剩下的选择。让我逐一介绍一下:

**选项 1**

这是初学这门语言的人会选择的选项。我也选择了这作为第一个选项，并立即在借用检查器中遇到了问题。当然，使用这种实现意味着小部件成为子部件和父部件的所有者。这是不可能的，因为这样一来，父节点和子节点将会循环引用拥有彼此。

**选项 2**

这是我接下来选择的选项。它的优势在于，它相当类似于 nanogui 的 C++风格。有几个缺点，比如不得不在库内外到处使用不安全的块，并且不能让 rust 借用检查器检查指针的有效性。但是这个选项的主要突破点是目前不可能创建自引用计数对象。注意，我不是指 C++的智能指针或者 Rust 自己的 Rc 类型。我指的是一个对象，它计算自己被引用了多少次，当这个值达到 0 时，它就会删除自己。你可以在 nanogui 的 C++版本中找到一个[的例子](https://github.com/wjakob/nanogui/blob/master/include/nanogui/object.h#L25)。

要做到这一点，您需要能够告诉编译器只允许从对象内部删除自己。以下面的 Rust 为例:

```
struct WidgetObj {
    pub parent: Option<*mut WidgetObj>,
    pub font_size: i32
}impl WidgetObj {
    fn new(font_size: i32) -> WidgetObj {
        WidgetObj {
            parent: None,
            font_size: font_size
        }
    }
}impl Drop for WidgetObj {
    fn drop(&mut self) {
        println!("widget font_size {} dropped", self.font_size);
    }
}fn main() {
    let mut w1 = WidgetObj::new(1);
    {
        let mut w2 = WidgetObj::new(2);
        w1.parent = Some(&mut w2);
    } unsafe { println!("parent font_size: {}", (*w1.parent.unwrap()).font_size) };
}// [playground](https://play.rust-lang.org/?gist=4878c275e299e53a8f24684966e15996&version=nightly&backtrace=0)
```

这将产生以下输出:

```
widget font_size 2 dropped
parent font_size: 2
widget font_size 1 dropped
```

这碰巧在自由错误之后没有使用，因为可能在删除之后存储器没有被设置为 0，但是行为当然是未定义的。

因此，要有一个正确工作的自引用计数对象，您需要将它分配到全局的某个位置。根本没有办法告诉编译器在变量超出范围时不要自动丢弃它。

好吧，我说。随你便吧，拉斯特。做循环有向图的惯用方法是什么？

**选项三**

经过一番搜索，我找到了一个很好的铁锈库，用来创建名为[铁锈森林](https://github.com/SimonSapin/rust-forest/)的树。它巧妙地创建了一种拥有节点、使用智能指针引用节点以及插入/移除节点的方法。然而，rust-forest 中给出的实现不允许不同 T 类型的节点出现在同一个图中，这是 nanogui 等库的要求。

为了说明见[这个操场](https://play.rust-lang.org/?gist=18327500a1f25de57707f92ae75b96d3&version=nightly&backtrace=0)。这里要完整地包括有点太长了，但问题在于这个函数:

```
// Widget is a trait
// focused_widgets is a Vec<Rc<RefCell<Widget>>>
fn update_focus(&self, w: &Widget) {
    self.focused_widgets.clear();
    self.focused_widgets.push_child(w); // This will never work, we don't have the reference counted version of the widget here.
}// [playground](https://play.rust-lang.org/?gist=18327500a1f25de57707f92ae75b96d3&version=nightly&backtrace=0)
```

顺便说一句，以下是一个可以解决的奇怪问题，但是我不明白为什么这是一个问题:

```
let refObj = Rc::new(RefCell::new(WidgetObj::new(1)));
&refObj as &Rc<RefCell<Widget>>; // non-scalar cast// [playground](https://play.rust-lang.org/?gist=824a67d6161ef8144cff5e0d99ee704a&version=nightly&backtrace=0)
```

**结论**

我在选项 1 到 3 中遇到的问题让我相信，选项 4，绑定到 C，将是我试图编写的库的唯一可行的替代方案。现在我在这个阶段，我在想，当我可以用 C 写的时候，为什么要写 C 绑定呢？还是 C++？

Rust 作为一种编程语言有一些好的方面。我很喜欢 Match 的工作方式。我喜欢 traits 背后的想法，就像 Go 中的接口一样，我喜欢将 cargo 作为一种打包工具。但是当谈到特征、引用计数和不可能重写编译器行为的实现细节时，我只能说:不，这对我不起作用。

真心希望大家继续使用和改进 Rust。但是我想写游戏。不用与编译器较劲，也不用编写 RFC 来使语言更有利于我的目标。

如果到目前为止你已经阅读了整篇文章并坚持下去，那么感谢你阅读我的长篇大论。如果没有，就没有 TL；我是医生，抱歉。这里有太多的概念和解决方案，我无法进一步浓缩。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！