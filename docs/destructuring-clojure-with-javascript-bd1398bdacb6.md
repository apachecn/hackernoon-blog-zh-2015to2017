# 用 Javascript 析构 Clojure

> 原文：<https://medium.com/hackernoon/destructuring-clojure-with-javascript-bd1398bdacb6>

Clojure 是我在 T2 Swym 公司工作时学习的一种语言。当我去年加入时，我不知道它意味着什么，除非它是导致 Javascript for loops 出现问题的[。](http://stackoverflow.com/a/111111/1304559)事实证明它非常接近于 [Javascript](https://hackernoon.com/tagged/javascript) 。

![](img/be290c1b77282727c1f0a5c585c59268.png)

Source — [http://java.ociweb.com/mark/clojure/article.html](http://java.ociweb.com/mark/clojure/article.html)

来到 Clojure。这是一种美丽的语言。一开始很奇怪，但你会慢慢喜欢的。函数式语言似乎更倾向于。在这篇文章中，我将谈论许多语言共有的一个特性，但在 Clojure 和 Javascript 中有具体的细节，有它们的快乐和痛苦。破坏。为什么是 Clojure？—那是以后的事了。

析构使赋值变得更容易，这可能需要多行代码。

# 简单的解构，

简单来说，从向量和数组中快速加载变量，不多不少重复一个字。

```
//JS// Old way
var list = [1,2,3];
var x = list[0], y = list[1], z = list[2];
console.log(x, y, z);// Destructured way
var list = [1,2,3];
var [x, y, z, a] = list;
console.log(x, y, z, a);
// Note - Any extra parameter not accounted for becomes *undefined*
```

Clojure 和 JS 的一个明显区别是数据结构的不变性。Like 变量赋值可用，但不一定好用。这在理解 Clojure 时起了很大作用，我最初认为这是一个障碍。但是随着时间的推移，直觉显现出来了。

```
;; Clojure;; Using let definition, same can be done with a *def* for the list
(let [list [1 2 3]
  [x y z] list]
  (println x y z));; Better one, list ref created within the destructuring
(let [[x y z a :as list] [1 2 3]]
 (println x y z a list))
;; Note - Extra parameter unaccounted for becomes *nil*
```

更深入地研究向量，尤其是当你不想列出参数和析构的时候

```
// JS
// Bunching rest of the values with a spread operator
var list = [1, 2, 3, 5, 6, 7, 8];
var [x, y, z, …a] = list;
console.log(x, y, z, a);
// Note - The last param will be the spread operator(...) Powerful for array merging, and data processing. [More on spread operators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)// Ignoring parts
var list = [1, 2, "ignore me", 3];
var [x,y,,z] = list;
console.log(x,y,z);
```

Clojure 与之匹配

```
;; Clojure
;; Spread operator
(let [[x y z & rest-of-it :as list] [1 2 3 5 6 7 8]]
 (println x y z rest-of-it));; Ignoring 
(let [[x y _ z :as list] [1 2 "ignore me" 3]]
  (println x y z))
```

选择缺省值很简单，没有以前那些脆弱的 if 检查。

```
// JS
// Old way - Default values
var list = [1,2];
var x = list[0], y = list[1];
var z = list[2] || 'default z'; // This check is flimsy, you need to check for *undefined* to avoid *falsy* errors. for eg: false will pass to second part // Default values
var list = [1, 2];
var [x,y,z="default z"] = list;
console.log(x,y,z);
```

Clojure 中相同

```
;; Clojure
;; Default values
(let [[x,y,z :or [z 10]] [1 2]]
 (println x y z))
```

# 对象和嵌套结构

这就是真正的乐趣所在。通过简化赋值，对于接受配置和可选参数的函数非常强大。

```
// JS
// Object
var o = {“x”: 1, “y”: 2, “z”: 3};
var {x, y, z} = o;
console.log(x, y, z);
// Also works - var {x, y, z} = {x: 1, y: 2, z: 3};
// Also works - var x,y,z; ({x, y, z} = o);// Renaming
var o = {“x”: 1, “y”: 2, “z”: 3};
var {x: a, y: b, z: c} = o;
console.log(a, b, c);// Default values with renaming
var o = {“x”: 1, “y”: 2, “z”: 3};
var {x, y, z: c, p: d=10, q=20} = o;
console.log(a, b, c, d, q);// Usage in functions
function doSomethingAwesome({mandatoryParam, renameParam: changedParam, optionalParam=1, optionalRenameParam: changedOptionalParam=2}){
 console.log(mandatoryParam, changedParam, optionalParam, changedOptionalParam);
}
doSomethingAwesome({mandatoryParam: 10, renameParam: 20});// Nested objects
var o = {
 “a”: {
 “x”: 1,
 “y”: [2, 3]
 }
};// With renaming and default values
var {a: {x, y: [aa, bb, cc=20], z=10}} = o;
console.log(a, x, y, p, aa, bb, cc, z);
```

与 Javascript 相比，Clojure 有一些枪，如 *:keys* 和 *:strs* 绑定形式，将析构捆绑在一起。警告——进入一个稍微复杂一点的领域

```
;; Clojure
;; Object — not so efficient when there is no renaming
(let [{x :x y :y} {:x 1 :y 2 :z 3}]
 (println x y))

;; Object with renaming
(let [{rx :x y :y} {:x 1 :y 2 :z 3}]
 (println rx y));; Object with keywords and strings, but no renaming
(let [{:keys [x y z] :as list} {:x 1 :y 2 :z 3}
 {:strs [xx yy zz] :as list-str} {“xx” 11 “yy” 22 “zz” 33}]
 (println x y z xx yy zz));; Object with defaults
(let [{:keys [x y z xx yy zz] :or {xx 10 yy 20 zz 30} :as list} {:x 1 :y 2 :z 3}]
 (println x y z xx yy zz))

;; Object defaults with renaming
(let [{rx :x y :y rz :z :or {rz 30}} {:x 1 :y 2}]
 (println rx y rz))

;; Usage in functions — not so efficient
(defn do-something-awesome [{
 mandatoryParam :mandatoryParam
 changedParam :renameParam
 optionalParam :optionalParam
 changedOptionalParam :optionalRenameParam
 :or {optionalParam 1 changedOptionalParam 2} }]
 (println mandatoryParam changedParam optionalParam changedOptionalParam))(do-something-awesome {:mandatoryParam 10 :renameParam 20});; Usage in functions — better
(defn do-something-awesome [{
 :keys [mandatoryParam optionalParam optionalRenameParam]
 changedParam :renameParam
 changedOptionalParam :optionalRenameParam
 :or {optionalParam 1 changedOptionalParam 2}
 }]
 (println mandatoryParam changedParam optionalParam changedOptionalParam))(do-something-awesome {:mandatoryParam 10 :renameParam 20});; Nested objects
(def o {
 :a {
 :x 1
 :y [2, 3]
 }
})
;; With default values
(let [{ {:keys [x y]} :a} o
 [y1,y2] y] ;; forced to add a line to break y to params
 (println x y y1 y2));; y1, y2 don’t get assigned, what’s wrong? a TODO for me to figure out
(let [{ {:keys [x [[y1,y2] :y]]} :a} o]
 (println x y y1 y2))
```

keys 绑定窗体和常规析构函数的用法是一种很好的用法，而不是只有一种。*支持重命名；作为更高层次的*绑定形式

现在是时候大开杀戒了。让我们看看上面哪个组合打破了 REPL。

**JS Overkill**——很难找到这种情况——使用内部析构进行重命名

```
// Overkill?
var o = {
 “a”: {
 “x”: 1,
 “y”: [2, 3]
 }
};
var {a: {x, y: p = [aa, bb, dd=20], z=10}} = o;
console.log(a, x, y, p, aa, bb, dd, z);
// Throws error, renaming with internal destructuring doesn’t work
```

Clojure Overkill 更容易被发现。试图在析构对象时析构对象内部的向量。

```
;; y1, y2 don't get assigned, what’s wrong? a TODO for me to figure out
(let [{ {:keys [x [[y1,y2] :y]]} :a} o]
 (println x y y1 y2))
```

析构使得代码更直观，更容易编写函数，阅读函数式编程。两者对比，让 Javascript 的函数起源更加清晰。一个新的问题是 Javascript 原型对象属于函数式编程范式吗？

不幸的是，对于 Javascript 用户来说，在所有基于浏览器的 JIT 上使用析构并不完全安全，但是如果你了解你的部署，就去做吧。

一定要让我知道你的想法和你将如何使用它们，并请改进我可能写错的任何东西。

**参考文献**

**Javascript** https://developer . Mozilla . org/en/docs/Web/Javascript/Reference/Operators/destructing _ assignment
[https://developer . Mozilla . org/en-US/docs/Web/Javascript/Reference/Operators/Spread _ operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)

**Clojure**
[先看这个很有用——http://blog . brunobonacci . com/2014/11/16/clo jure-complete-guide-to-destructing/](http://blog.brunobonacci.com/2014/11/16/clojure-complete-guide-to-destructuring/)
[http://clojure.org/guides/destructuring](http://clojure.org/guides/destructuring)
[https://gist.github.com/john2x/e1dca953548bfdfb9844](https://gist.github.com/john2x/e1dca953548bfdfb9844)

页（page 的缩写）S: PHP 烂，Javascript 美，Java 一致，CSS 再美，客观 C 诡异，Python 敏感(读空白)，C#/。网是变种人等等。全是个人观点。

页（page 的缩写）附言:我用来参考的要点

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)