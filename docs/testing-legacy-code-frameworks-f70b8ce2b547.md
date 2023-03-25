# 测试遗留代码:框架

> 原文：<https://medium.com/hackernoon/testing-legacy-code-frameworks-f70b8ce2b547>

遗留[代码](https://hackernoon.com/tagged/code)的一个常见特性是与外部框架或 SDK 的紧密耦合。它可以是“[管理器](https://hackernoon.com/tagged/manager)或“ApiClient”单例的形式，在整个应用程序中最关键的地方使用。或者可能更微妙，比如利用框架提供的“DateUtil”类。

你可能会问“你有什么问题？我为什么要重新发明轮子？”。不，你不应该。听我说完。

使用库、SDK、框架等。这是一件好事，尤其是当他们成熟并经过战斗考验的时候。[开发自己的 HTTP 客户端或 date util](https://www.youtube.com/watch?v=-5wpm-gesOY) 毫无意义。问题是，当你在使用它们的时候没有考虑如何替换它们。

因为有时候库是被弃用的。或者新版本包含重大变更。或者一个更新更好的库发布了，而你还在用恐龙时代写的代码。

或者你只是想写一些单元测试。当遗留代码中夹杂着框架方法调用时，乐趣就开始了。

## 一个

哦，是的，每个人都喜欢憎恨单身人士。当代码使用外部库中的单例时，情况会更糟。

```
public void loadImage(){
  Picasso.with(context).load(url).into(view);
}
```

这是一行漂亮的代码。任何 Android 开发者都会告诉你这一点。杰克·沃顿出版毕加索作品的那一天应该是 Android 社区的零年。

但是尝试为该方法编写一个单元测试。或者更好的是，想象一下真实的代码是什么样子，例如一个老化的列表适配器，带有无数错误修复和清理的伤痕。

您可以修改代码，通过依赖注入提供对 Picasso 的引用，并在单元测试中模拟它。它会工作得很好。

但是想象一下你不得不切换到另一个库，就像[凌空](https://developer.android.com/training/volley/index.html) ( *为什么会有人用毕加索以外的东西？反正…* )。您需要将您的代码更改为如下所示:

```
public void loadImage(){
  imageLoader.get(url, ImageLoader.getImageListener(view, 
    R.drawable.default, R.drawable.error));
}
```

当然，你的测试也必须重写。

如果你用一层薄薄的包装纸包住毕加索，像这样:

```
public interface GenericImageLoader {
  void loadImage(String url, ImageView imageView);
}public class PicassoImageLoader implements GenericImageLoader {
  public void loadImage(url, view){
    Picasso.with(context).load(url).into(view);
  }
}/// usage in the code: 
private GenericImageLoader loader = new PicassoImageLoader();
public void initializeStuff(){
  this.loader.loadImage("http://...", profileImageView);
}
```

您将不得不接受妥协，不对包装器进行单元测试。如果你只把它作为一个包装器，方法调用将直接映射到库方法。只要你不在里面做什么疯狂的事情，你应该是安全的。

现在，对 *initializeStuff()* 方法进行单元测试更加简单。如果您必须切换到凌空，那么，您只需创建一个不同的 *GenericImageLoader* 接口的实现:

```
public class VolleyImageLoader implements GenericImageLoader {
  public void loadImage(url, view){
    imageLoader.get(url, ImageLoader.getImageListener(view,
      R.drawable.default, R.drawable.error));
  }
}// usage in the code:
private GenericImageLoader loader = new VolleyImageLoader();
public void initializeStuff(){
  this.loader.loadImage("http://...", profileImageView);
}
```

*initializeStuff()* 方法保持不变，但是有了一个[全新的世界](https://www.youtube.com/watch?v=t9-CS2v8wcc) ( *咳咳！)*后面的 *GenericImageLoader* 接口。好棒的多态性！

## 静态方法

现在，假设在您想要测试的方法中有一个包含静态方法的实用程序类。通常，如果静态方法没有任何神奇之处，它们就不是问题。但是你有一个由真正喜欢静态方法的人设计的 API。像这种[请求位置更新的方式](https://developer.android.com/training/location/receive-location-updates.html#updates)在安卓:

```
public void startLocationUpdates() {
    LocationServices.FusedLocationApi.requestLocationUpdates(
            mGoogleApiClient, mLocationRequest, this);
}
```

祝你测试成功。你甚至不能嘲笑它。您唯一的机会是围绕 API 创建一个包装器:

```
public interface LocationApi {
  void requestLocationUpdates(GoogleApiClient client,                       
              LocationRequest request, LocationListener listener);
}public class FusedLocationApiWrapper implements LocationApi { public void requestLocationUpdates(GoogleApiClient client,                       
              LocationRequest request, LocationListener listener) {
    LocationServices.FusedLocationApi.requestLocationUpdates(     
                    client, request, listener);
  }
}// usage in code 
private LocationApi wrapper = new FusedLocationApiWrapper();public void startLocationUpdates() {
    wrapper.requestLocationUpdates(
            mGoogleApiClient, mLocationRequest, this);
}
```

同样，只要你只包装静态方法，你就可以不用测试。

然而，您可能会意识到 *LocationApi* 接口仍然与 *FusedLocationApi 紧密耦合。*它使用 *LocationRequest* 类，需要一个 *GoogleApiClient* 和一个 *LocationListener，*Google Play 服务的所有部分。

为了在 location API 上创建一个真正的抽象层，您必须定义自己的方式来提供包含在 *LocationRequest* 中的信息。

```
public interface LocationApi {
  void requestQuickLocation();
  void requestPreciseLocation();
}public class FusedLocationApiWrapper implements LocationApi {
  private OnLocationListener listener; public FusedLocationApiWrapper(GoogleApiClient client){
    this.client = client;
  } public void requestQuickLocation(GoogleApiClient client,                       
              LocationRequest request, LocationListener listener) {
    LocationRequest request = LocationRequest.create();
    request.setMaxWaitTime(ONE_SECOND);
    request.setPriority([PRIORITY_BALANCED_POWER_ACCURACY](https://developers.google.com/android/reference/com/google/android/gms/location/LocationRequest.html#PRIORITY_BALANCED_POWER_ACCURACY));
    LocationServices.FusedLocationApi.requestLocationUpdates(     
                    client, request, listener);
  } public void requestPreciseLocation(GoogleApiClient client,                       
              LocationRequest request, LocationListener listener) {
    LocationRequest request = LocationRequest.create();
    request.setMaxWaitTime(FIVE_SECONDS);
    request.setPriority([PRIORITY_HIGH_ACCURACY](https://developers.google.com/android/reference/com/google/android/gms/location/LocationRequest.html#PRIORITY_HIGH_ACCURACY));
    LocationServices.FusedLocationApi.requestLocationUpdates(     
                    client, request, listener);
  }
}// usage in code
private GoogleApiClient client;
private LocationApi wrapper = new FusedLocationApiWrapper(client);public void startLocationUpdates() {
    wrapper.requestQuickLocation();
}
```

客户端代码看起来干净多了，更容易进行单元测试，也更容易换成另一个位置提供者。你可以写很多额外的代码！不喜欢什么？(*不要回答那个*)。

## 最终类和方法

库和框架有时会将它们的公共类和方法标记为 final。从安全角度来看，这很有意义。作为一个 API 创建者，你不希望开发者对你的 API 内部有太多的兴趣。所以你试图让你的面向公众的接口尽可能的密闭:最终的方法，实现单个实例的单例等等。

但是你不能嘲笑他们。见上。

## 结论

封装很重要。[关注点分离](https://en.wikipedia.org/wiki/Separation_of_concerns)很重要。[低耦合](https://en.wikipedia.org/wiki/Coupling_(computer_programming))很重要。[依赖倒置](https://en.wikipedia.org/wiki/Dependency_inversion_principle)很重要。

但更重要的是知道它们很重要。你并不总是有时间把所有的东西抽象出来。你也不应该。只要记住外部库是强大的野兽。最好和他们保持一定的距离。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！