# 让我们制作可重用的网络组件:谷歌地图

> 原文：<https://medium.com/hackernoon/lets-make-reusable-web-components-google-maps-8be7577d925>

![](img/9ff4d9d097ea1b59565b1d8913e22953.png)

推动 web 开发前进的一个巨大挑战是缺乏对 web 组件的完全共识。

事实证明，到处都有框架，每个框架都有自己的开销和想法，一些常见的 [UI](https://hackernoon.com/tagged/ui) 组件仍然是从头开始开发的。

这是意料之中的，因为框架迫使你进入他们的世界，所以尽管 React 或 Vue 组件是可重用的，但你必须忠于那个框架。我们中的一些人在承诺方面有问题。

有心理医生，但他们通常什么也治不好。

有一个 **Web 组件**标准，诞生于这样一个事实:将 HTML/JS/CSS 混杂在一起在概念上是非常不愉快的。

例如，我们可以按照他们的教程指示，将[谷歌](https://hackernoon.com/tagged/google)地图放在我们的页面上。

```
<div id=”map”></div><script>
 function initMap() {
  // Create a map object and specify the DOM element for display.
  var map = new google.maps.Map(document.getElementById(‘map’), {
    center: {lat: -34.397, lng: 150.644}, zoom: 8
    });
 }
</script>
<script src=”[https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&callback=initMap](https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&callback=initMap)" async defer></script>
```

但是，这正是重点。是我们页面上的*，不是我们 app* 里的***。***

**当然，在我们使用 *google.maps* 对象之前，需要加载脚本，这是一个问题。脚本标签应该在 HTML 之后。这不太好。**

**在一个页面上可能需要很多组件的世界里，我们只能用 initMap 作为入口点。我们可能正在加载其他脚本，等等..变得一团糟。**

**所以，让我们尝试一种不同的方法。我们必须动态加载 JS。**

```
var e = document.createElement(‘script’)
e.src = s; 
document.body.appendChild(e)
```

**为了简单起见，我们将这个函数称为 load。**

```
const load = async (s) => {
  return new Promise(async r=>{
   for(i=0;i<document.scripts.length;i++)
     if(document.scripts[i].src == s) return r()
   let e = document.createElement('script')
   e.src = s 
   document.body.appendChild(e)
   e.onload = r
  })
}
```

**当脚本加载后，这个承诺就实现了。它还首先检查脚本是否已经加载。**

**所以，现在我们可以制作我们的可重用组件，一个 gmap 函数:**

```
const gmap = async (e) => {
  return new Promise(async r=>{
     e.style.height = e.style.width = e.getAttribute('size')
     let latlng = e.getAttribute('latlng').split(',')
     await load('[http://maps.googleapis.com/maps/api/js?v=3'](http://maps.googleapis.com/maps/api/js?v=3'))
     let map = new google.maps.Map(e, {
      center: new self.google.maps.LatLng(latlng[0], latlng[1]),
      zoom: 14,
      mapTypeId: google.maps.MapTypeId.ROADMAP
     });
     r(map)
  })
}
```

**这是一个异步函数，它将等待加载脚本完成。这将确保一切都按顺序进行。我们可能希望在一个页面上有多个地图。**

```
<div control=gmap size=200px latlng=33.808678,-117.918921></div>
<div control=gmap size=200px latlng=28.3852377,-81.5660627></div>
```

**并将 div 提供给 gmap“组件”以创建控件**

```
(async () => {
  let controls = document.querySelectorAll(‘div[control=gmap]’)
  for (var i = 0; i < controls.length; i++)
    await gmap(controls[i])
})()
```

**所有的依赖都是免费的，并且是普通的 JS 格式。**

**重要的是，没什么需要记住的。
Div、函数和语言特性。**

**不知道哪种模式会胜出，但有可能只是使用语言而没有额外的东西，也很有趣。**