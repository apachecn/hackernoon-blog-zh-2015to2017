# 和 Java 约会

> 原文：<https://medium.com/hackernoon/going-on-a-date-with-java-9bdac2c950b3>

![](img/b64e503eff9009bb3389861b6d46fca5.png)

Photo by [Heather Zabriskie](https://unsplash.com/photos/yBzrPGLjMQw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

Java 8 的所有流都在正确的位置，这正是我想要的。你知道吗，我想我要带它出去来个数字约会。今天是 8 月 4 日星期五，刚过 CEST 时间 15:00。

> 我们多晚见面？

别担心，我会想出办法的。让我看看使用哪个类。

## 日期

这是个好名字！让我们看一看。

```
The class Date represents a specific instant in time, with millisecond precision.
```

是的，我需要精确。让我们用这一个。

```
>>> Date *date* = **new** Date();
```

看看这些日期，三个一行，这将是伟大的。

```
>>> System.***out***.println(*date*);
Fri Aug 04 15:10:50 CEST 2017
```

嘿，一个时区。那是从哪里来的？

```
public [String](https://docs.oracle.com/javase/8/docs/api/java/lang/String.html) **toString**()
Converts this Date object to a String of the form:
 dow mon dd hh:mm:ss zzz yyyy
where:
<snip>
zzz is the time zone (and may reflect daylight saving time). Standard time zone abbreviations include those recognized by the method parse. If time zone information is not available, then zzz is empty - that is, it consists of no characters at all.
```

好吧，这不是答案。斯达克弗洛，你是我们唯一的希望。

```
What time zone does Date.toString() display? - Stack Overflow
[https://stackoverflow.com/questions/4199217/what-time-zone-does-date-tostring-display](https://stackoverflow.com/questions/4199217/what-time-zone-does-date-tostring-display)
It displays using TimeZone.***getDefault***() which, in turn, will default to the time zone of the operating system it is running on (i.e. the host)
```

啊，那么转换只是为了展示？

```
>>> System.***out***.println(*date*.getHours());
15
```

什么？

![](img/dfc60c972af83fb58c0cb03ac6bdefda.png)

让我们看看文件。

```
[@Deprecated](http://twitter.com/Deprecated)
public int **getHours**()Deprecated. As of JDK version 1.1, replaced by Calendar.**get**(Calendar.HOUR_OF_DAY).
```

哦，当然了。别担心，我去看看日历。

## 日历

```
Calendar *cal* = Calendar.getInstance();
```

当然，实例化是最近十年的事了。单身才是最流行的！不过我确信他们考虑到了线程安全。让我们问堆栈溢出来确定。

```
Is java.util.Calendar thread safe or not? - Stack Overflow
[https://stackoverflow.com/questions/12131324/is-java-util-calendar-thread-safe-or-not](https://stackoverflow.com/questions/12131324/is-java-util-calendar-thread-safe-or-not)
Aug 26, 2012 - If you read the code you will see that none of the instance methods are synchronized, and none of the instance fields are volatile . ... In short, the Calendar class is not thread-safe, and GregorianCalendar isn't either because it inherits the non-thread-safe fields and methods.
```

哦。好吧。没关系，反正对我来说也没那么重要。我们去看看我们能做些什么！

```
**Getting and Setting Calendar Field Values**
The calendar field values can be set by calling the set methods.
```

当然，固定的方法。就像 setHours 和 setMonth，对吗？

```
public void **set**(int field, int value)
Sets the given calendar field to the given value.
```

嗯，那这个怎么用呢？

```
cal.set(Calendar.**MINUTE**, 20);
```

当然，因为拥有一个类型系统的最大好处就是你可以显式忽略它。但这可能只是低层次的 API，我敢肯定这不是我们所有的。

```
public abstract void **add**(int field, int amount)
Adds or subtracts the specified amount of time to the given calendar field, based on the calendar's rules. public abstract void **roll**(int field, boolean up)
Adds or subtracts (up/down) a single unit of time on the given time field without changing larger fields. public void **roll**(int field, int amount)
Adds the specified (signed) amount to the specified calendar field without changing larger fields. A negative amount means to roll down.
```

这是什么 API？你在说什么？你为什么带着日期滚，还有“滚下来”到底是什么意思？这个类只有在你在山上的时候才有意义吗？

我们定期检查设计这个东西的人吗？因为我不确定他是否会没事。

在与我相关的层次上，为什么没有我真正想做的事情的操作？为什么我不能去下周，或者一天的开始或结束？为什么每次与您合作时，您都希望我自己实现所有这些功能？

Java，你让我改变主意了。

![](img/33be908afe1dc25c88d3bd85a64654a8.png)

The designers of Java’s date/time libraries.

## Java 8

不过，我显然是落伍了。Java 8 凭借其全新的日期/时间 API 脱颖而出。我们得到了什么？

```
The first classes you will probably encounter when using the new API are LocalDate and LocalTime.
```

好吧，让我们看看。

```
class **LocalDateTime** A date-time without a time-zone in the ISO-8601 calendar system, such as 2007-12-03T10:15:30.
```

好的，没有时区。所以这意味着当我想查看日期时，我必须提供它？

```
>>> LocalDateTime *timePoint* = LocalDateTime.now();
>>> System.***out***.println(*timePoint*);
2017-08-04T15:23:41.541
```

嘿，那是在我的时区。但是它没有时区信息。

```
public static LocalDateTime **now**()
Obtains the current date-time from the system clock in the default time-zone.
```

所以你是说…

```
>>> System.***out***.println(*timePoint.*getHour*()*);
15
```

![](img/dfc60c972af83fb58c0cb03ac6bdefda.png)

让我们看看医生对此有什么看法。

```
This class does not store or represent a time-zone. Instead, it is a description of the date, as used for birthdays, combined with the local time as seen on a wall clock. It cannot represent an instant on the time-line without additional information such as an offset or time-zone.
```

所以这是一个奇特的字符串？我是说，没有时区时间就没有意义。你总是有一个时区，即使你假装没有，我们刚刚看到它发生了。为什么不总是强迫用户显式？什么时候使用默认时区更好？为什么要添加一些“本地”和挂钟的概念，并隐藏那些实际上并不难的东西呢？

没有什么简单明了的东西我可以用吗？

```
**Class Instant**
An instantaneous point on the time-line.This class models a single instantaneous point on the time-line. This might be used to record event time-stamps in the application.
```

我谨慎乐观…

```
>>> Instant instant = Instant.now();
>>> System.***out***.println(*instant*);
2017-08-04T15:28:01.354Z
```

UTC 默认，对我来说有意义。这是有前途的！只是时间线上的一个简单的、可预测的值，总是使用 UTC，所以当我需要向用户显示它时，我可以简单地转换它。我确信他们至少实现了一个比日历更好的 API

```
public int **get**(TemporalField field)
Gets the value of the specified field from this instant as an int.public Instant **plus**(long amountToAdd, TemporalUnit unit)
Returns a copy of this instant with the specified amount added.
```

你知道吗，Java，我想我会呆在家里玩我的 Python。

## 也不全是坏事

我们退一步讲，因为 LocalDateTime 的 API 其实挺好看的。

```
public static LocalDateTime **of**(int year,
                               int month,
                               int dayOfMonth,
                               int hour,
                               int minute,
                               int second,
                               int nanoOfSecond)
Obtains an instance of LocalDateTime from year, month, day, hour, minute, second and nanosecond.public LocalDateTime **plusWeeks**(long weeks)
Returns a copy of this LocalDateTime with the specified number of weeks added.This method adds the specified amount in weeks to the days field incrementing the month and year fields as necessary to ensure the result remains valid. The result is only invalid if the maximum/minimum year is exceeded.public LocalDateTime **withHour**(int hour)
Returns a copy of this LocalDateTime with the hour-of-day altered.
```

有一个类实现了相同的 API，使得时区更加明确。

```
ZonedDateTime is a date and time with a fully qualified time zone. This can resolve an offset at any point in time.class **ZonedDateTime** A date-time with a time-zone in the ISO-8601 calendar system, such as 2007-12-03T10:15:30+01:00 Europe/Paris.
```

我们来试试吧！

```
>>> ZonedDateTime *timePoint* = ZonedDateTime.now();
>>> System.***out***.println(*timePoint*);
2017-08-04T15:33:02.325+02:00[Europe/Amsterdam]
>>> System.***out***.println(*timePoint.*getHour());
15
>>> timePoint = timePoint.of(1983, JULY.getValue(), 20, 15, 27, 40, 0, ZoneId.of("Europe/London"));
>>> System.***out***.println(*timePoint*);
1983-07-20T15:27:40+01:00[Europe/London]
>>> *timePoint = timePoint.*plusYears(30).minusHours(4)
>>> System.***out***.println(*timePoint*);
2013-07-20T11:27:40+01:00[Europe/London]
```

这是明确的，功能性的，非常优雅。API 是流畅的，这使得设置具体日期或执行多次修改变得更加民事化。里面也没有惊喜。

奇怪的是，前面讨论的类通常是在讨论这个类之前描述的，而且很多焦点都集中在 LocalX 类上。但是我建议在大多数情况下只使用 ZonedDateTime。一般来说，在处理日期和时间时，最好的做法是在内部将一切都显式地视为 UTC。然后，将日期时间转换为适用的时区，该时区可以是用户特定的，只在应用程序中需要显示它的地方显示。ZonedDateTime 可以很好地促进这种模式。

这篇文章的内容很大程度上是一种讽刺，并没有准确反映作者的思维过程或编程能力。实际上，更多的是脏话。