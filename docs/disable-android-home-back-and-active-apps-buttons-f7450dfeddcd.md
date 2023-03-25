# 禁用 Android 主页、后退和活动应用按钮

> 原文：<https://medium.com/hackernoon/disable-android-home-back-and-active-apps-buttons-f7450dfeddcd>

在你完全进入这篇帖子之前，我想警告你关于这些功能的使用，因为它们不是用户友好的， **Android Play Store 不会批准这些 ***中的***，除非你有非常强有力的理由** 。

![](img/8dc806f12d58e516c3b781995dd7ec2b.png)

Photo by [Suika Ibuki](https://unsplash.com/photos/kknrCfZHsyo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/android?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 活动应用程序/重新排序应用程序按钮

将后的[权限添加到 AndroidManifest.xml](https://hackernoon.com/tagged/following)

```
<uses-permission android:name="android.permission.REORDER_TASKS" />
```

以下为该活动

```
@Override
 protected void onPause() {
        super.onPause();

        ActivityManager activityManager = (ActivityManager) getApplicationContext()
                .getSystemService(Context.ACTIVITY_SERVICE);

        activityManager.moveTaskToFront(getTaskId(), 0);
}
```

# 主页按钮

```
<category android:name="android.intent.category.LAUNCHER" />
        <category android:name="android.intent.category.HOME" />
        <category android:name="android.intent.category.DEFAULT" />
```

# 后退按钮

```
@Override
public boolean onKeyDown(int keyCode, KeyEvent event) {
    // Do nothing or catch the keys you want to block
}
```

## 参考

[](http://stackoverflow.com/questions/6507063/home-button-disable/6530002#6530002) [## 主页按钮禁用

### 我知道让用户控制主页按钮是不明智的。但是我正在开发一个安卓锁定系统…

stackoverflow.com](http://stackoverflow.com/questions/6507063/home-button-disable/6530002#6530002) 

如果你做到了这一点，你就成功了！！祝您愉快！

如果这对你有所帮助，就拍拍屁股走人。它鼓励我写更多的帖子。谢谢你的支持。

*[***@ pradyumna _ d***](https://twitter.com/pradyumna_d)***|****文件贵* [*加密货币*](https://hackernoon.com/tagged/cryptocurrency) *税费用**[***bear tax***](https://bear.tax)******