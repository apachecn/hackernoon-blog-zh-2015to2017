# 使用 WeakHashMap 保存回调的引用

> 原文：<https://medium.com/hackernoon/using-weahhashmap-to-save-references-to-callbacks-ed4f78ccb33>

根据' [Effective Java](https://hackernoon.com/tagged/effective-java) ，第二版'，“…确保回调被及时垃圾收集的最好方法是只存储对它们的*弱引用*，例如，通过只将它们作为键存储在 WeakHashMap 中。”

在 [JDK](https://hackernoon.com/tagged/jdk) 源代码中有一个例子，使用 WeakHashMap 保存回调以防止内存泄漏。

让我们看看这个班级

```
BaseResourceFactory
```

有一个字段:listenerMap

，这是一个 WeakHashMap，保存正在添加的侦听器。

```
// Use a WeakHashMap as it automatically removes dead objects when they're
// collected
private final WeakHashMap<ResourceFactoryListener,Boolean> listenerMap =
        new WeakHashMap<ResourceFactoryListener,Boolean>();
```

要添加一个监听器，客户端只需要将监听器保存为 key，和 Boolean。TRUE 作为地图的值。

```
@Override public void addFactoryListener(ResourceFactoryListener l) {
    listenerMap.put(l, Boolean.*TRUE*);
}
```

为什么使用布尔型？真的吗？

因为它是一个不变的物体:

```
public static final Boolean *TRUE* = new Boolean(true);
```

所以我们把它作为一个占位符，不需要创建任何额外的对象或者使用任何额外的内存。

为了获取所有侦听器，getFactoryListeners 方法返回 WeahHashMap 的 keySet 的防御性副本。

```
protected ResourceFactoryListener[] getFactoryListeners() {
    return listenerMap.keySet().toArray(new ResourceFactoryListener[0]);
}
```

最后，还有移除方法。

```
@Override public void removeFactoryListener(ResourceFactoryListener l) {
    // remove will return null if there is no mapping, so it's safe to call
    // with unregistered listeners
    listenerMap.remove(l);
}
```