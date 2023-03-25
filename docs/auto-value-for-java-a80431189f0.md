# 自动赋值

> 原文：<https://medium.com/hackernoon/auto-value-for-java-a80431189f0>

![](img/883fccea7e85f223d6283482f73bf867.png)

Photo by [Matthew Szlichta](https://unsplash.com/photos/utIhBw00Jxs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

Auto Value 是一个帮助您轻松构建值类的库。它会在构建时自动生成代码，以减少源代码中大量样板文件。

# 以前

让我们从一个简单的 POJO 开始，看看我们可以改进什么。

```
public class Point { private double x;
    private double y; public double getX() { return x; }
    public double getY() { return y; } public void setX(double x) { x = x; }
    public void setY(double y) { y = y; } @Override
    public int hashCode() {...} @Override
    public boolean equals(Object other) {...} @Override
    public String toString() {...}
}
```

这个简单的代码有什么问题？

1.  Getter 和 setter 看起来像样板文件，但是我们仍然必须添加以符合封装规则。
2.  手动覆盖`hasCode()`和`equals()`以区分不同的对象。
3.  为了更好地记录，手动超越`toString()`。

**AutoValue 来救援了。**

# 开始吧

汽车价值如何帮助我们？这是我们使用 AutoValue 后的示例代码。

```
import com.google.auto.value.AutoValue;

@AutoValue
public abstract class Point { public abstract double x(); public abstract double y(); public static Point create(double x, double y) {
    return new AutoValue_Point(x, y);
  }
}
```

通过添加`@AutoValue`注释，Auto Value 为我们自动生成了`AutoValue_Point`类。我们通过抽象接口`Point`访问真实的类。我们解决了什么问题？

1.  不再有吸气剂了
2.  没有 setter，这意味着在大多数情况下不可变
3.  隐藏生成类中所有基本函数(equals，hashCode)的细节

*另外，需要注意的一点是，如果是数组类型，比如* `*List*` *，我们仍然可以修改值。*

# 生成类做什么？

我们还可以检查`AutoValue_Point`为我们做了什么。

```
final class AutoValue_Point extends Point { private final double x;
  private final double y;

  AutoValue_Point(
      double x,
      double y) {
    this.x = x;
    this.y = y;
  }

  @Override
  public double x() {
    return x;
  }

  @Override
  public double y() {
    return y;
  }

  @Override
  public String toString() {
    return "Point{"
        + "x=" + x + ", "
        + "y=" + y
        + "}";
  }

  @Override
  public boolean equals(Object o) {
    if (o == this) {
      return true;
    }
    if (o instanceof Point) {
      Point that = (Point) o;
      return (Double.*doubleToLongBits*(this.x) == Double.*doubleToLongBits*(that.x()))
          && (Double.*doubleToLongBits*(this.y) == Double.*doubleToLongBits*(that.y()));
    }
    return false;
  }

  @Override
  public int hashCode() {
    int h = 1;
    h *= 1000003;
    h ^= (Double.*doubleToLongBits*(this.x) >>> 32) ^ Double.*doubleToLongBits*(this.x);
    h *= 1000003;
    h ^= (Double.*doubleToLongBits*(this.y) >>> 32) ^ Double.*doubleToLongBits*(this.y);
    return h;
  }
}
```

# 构建器模式

如果参数太多，很难读取怎么办？AutoValue 还支持构建器模式来实例化对象。

```
import com.google.auto.value.AutoValue;

@AutoValue
public abstract class Point {

  public abstract double x();

  public abstract double y();

  public static Builder builder() {
    return new AutoValue_Point.Builder();
  }

  **@AutoValue.Builder
  public abstract static class Builder {** **public abstract Builder x(double x);

    public abstract Builder y(double y);

    public abstract Point build();
  }**
}
```

用法和普通的构建器一样简单。

```
Point p = Point.builder().x(100).y(100).build();
```

# 更新值

AutoValue 对象的设计是不可变的，但是 builder 模式也有利于我们在必要时克隆对象。只需添加一个抽象的`toBuilder`来表明我们有通过现有值创建构建器的功能。然后，我们可以在更新值之后或在任何时候将构建器转换回对象。

```
@AutoValue
public abstract class Point {

  public abstract double x();

  public abstract double y();

  public static Builder builder() {
    return new AutoValue_Point.Builder();
  }

  **public abstract Builder toBuilder();**

  **public Point withX(double x) {
    return toBuilder().x(x).build();
  }**

  @AutoValue.Builder
  public abstract static class Builder {
    public abstract Builder x(double x);

    public abstract Builder y(double y);

    public abstract Point build();
  }
}
```

# 参考

[](https://github.com/google/auto/tree/master/value) [## 谷歌/汽车

### auto-Java 源代码生成器的集合。

github.com](https://github.com/google/auto/tree/master/value)