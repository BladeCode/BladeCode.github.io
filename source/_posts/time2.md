---
title: 时间（二）之核心类
date: 2020-04-08 22:01:00
categories: JDK8
tag: [JDK8, Time]
---

上一篇[时间（一）](https://incoder.org/2020/04/07/time1/)文章，我们已经了解学习了时间相关的一些概念和时间相关的其他知识，那么本篇文章开始我们深入在计算机领域中关于时间的相关核心类的知识，先来让我们看看 JSR-310（JDK8+） 和之前（JDK7 之前）的比较

![](https://res.cloudinary.com/incoder/image/upload/v1586423539/blog/java-time-vs.png)

<!-- more -->

## JDK7

### Date

Date 是 Java 最早提供用来封装日期时间的类，由于不易于国际化且参数计算不符合日常认知或不正确，很多获取年、月、日、小时等数据的方法都已废弃（@Deprecated），被 Calendar 类的方法替代

Date 类有两个关键的成员变量
```java
// 记录当前时间戳
private transient long fastTime;

/*
 * If cdate is null, then fastTime indicates the time in millis.
 * If cdate.isNormalized() is true, then fastTime and cdate are in
 * synch. Otherwise, fastTime is ignored, and cdate indicates the
 * time.
 * cdate 对象是 BaseCalendar.Date 类，继承自 sun.util.calendar.CalendarDate 包含很多已计算好的日期时间相关变量，如 dayOfWeek(所在星期的第几天)，leapYear(是否闰年)等
 * 如果 cdate 对象为空，用 fastTime 变量代表精确到毫秒的时间
 * 如果 cdate.isNormalized() 方法返回 true，则 fastTime 和 cdate 已经同步过
 * 如果 cdate.isNormalized() 方法返回 false，则忽略 fastTime 的值，使用 cdate 代表时间
 */
private transient BaseCalendar.Date cdate;
```

Date 类提供了两个构造函数
```java
// 无参构造方法，创建当前时间的 Date 类
public Date(){
    this(System.currentTimeMillis());
}

// 传入一个 Unix 时间戳，创建特定的时间 Date 类
public Date(long date){
    fastTime = date;
}

// 其他通过年月日创建的构造方法已被 Calendar.set() 和 DateFormat.parse() 等方法替代
```

>静态方法 `System.currentTimeMillis()` 返回 UTC 时间从 <font color=#FF0000>1970-01-01 00:00:00</font> 到现在的总**毫秒**数，返回类型为 **long**，也是我们最常见获取当前时间的方法

`java.sql.Date`，`java.sql.Time`，`java.sql.Timestamp` 类都继承自 `java.util.Date` 类，是专门用于数据库连接的，由于是继承关系，从数据结构来看它们和父类的区别不大。

主要区别在于 `Timestamp` 类可以表示至纳秒级，其 `fastTime` 字段从秒之后被截断，毫秒至纳秒精度保持在特有的 `nanos` 字段中，需要注意 `Timestamp` 类的纳秒进度可能是 <font color=#FF0000>假的</font>

```java
package java.sql.Timestamp;

/**
 * Constructs a Timestamp object
 * using a milliseconds time value. The
 * integral seconds are stored in the underlying date value; the
 * fractional seconds are stored in the nanos field of
 * the Timestamp object.
 *
 * @param time milliseconds since January 1, 1970, 00:00:00 GMT.
 *        A negative number is the number of milliseconds before
 *         January 1, 1970, 00:00:00 GMT.
 * @see java.util.Calendar
 */
public Timestamp(long time) {
    super((time/1000)*1000);
    nanos = (int)((time%1000) * 1000000);
    if (nanos < 0) {
        nanos = 1000000000 + nanos;
        super.setTime(((time/1000)-1)*1000);
    }
}
```
可以看出，在 `fastTime` 字段强行截断后，进行毫秒值直接乘以 1000000 的操作后赋给了 nanos 字段，成为了 “只能表示到毫秒的纳秒级精度” 。当然，还可以通过 `setNanos(int n)` 方法给纳秒数赋精确值。

虽然从数据结构上看没有什么特别，但如果涉及到 Timestamp 类的父子类型转换或时间比较，就需要注意这里的“坑”
1. equals() 方法的不对称性 `java.sql.Timestamp` 类和其父类 `java.util.Date` 的 equals() 方法是不符合对称性
    ```java
    public static void main(String[] args){
        Data date = new Date();
        Timestamp timestamp = new Timestamp(date.getTime());
        System.out.println(date.equals(timestamp));
        System.out.println(timestamp.equals(date));
    }

    // 输出结果
    true
    false
    ```

2. 时间比较类方法的 “异常” 现象如下，两个有毫秒之差的时间点，`after()` 方法返回不符合客观事实
    ```java
    public static void main(String[] args){
        Long current = System.currentTimeMillis();
        Date t1 = new Timestamp(current);
        Date t2 = new Timestamp(current + 1);
        System.out.println(t2.after(t1));
        System.out.println(t2.compareTo(t1) > 0);
    }

    // 输出结果
    false
    true
    ```

如果在不确定类型的情况下进行时间的比较，尽量使用 <font color=#FF0000>compareTo()</font> 方法，可以保证正确性

### Calendar

Calendar 类是一个日历抽象类，提供了一组对年月日时分秒星期等日期信息操作的函数，并针对不同国家和地区的日历提供了相应的子类，即本地化（比如：公历【GregorianCalendar】，佛历【BuddhistCalendar】，日本历【JapaneseImperialCalendar】等）

从 JDK1.1 版本开始，在处理日期和时间时系统推荐使用 Calendar 类进行实现。在设计上，Calendar 类的功能要比 Date 类强大太多，而且在实现方式上也比 Date 类要复杂些

Calendar 类可以通过静态工厂方法或 new 子类的方式来获得实例
1. getInstance() 方法，有四个重载方法，参数是时区和地区，如果不传会取服务器默认的时区和地区（地区的出现是专门为了区分泰国和日本）
   * getInstance()
   * getInstance(TimeZone zone)
   * getInstance(Locale aLocale)
   * getInstance(TimeZone zone, Locale aLocale)
2. 新建子类对象
   ```java
   Calendar calendar = new GregorianCalendar();
   ```
   Calendar 类可以实现带时区的年月日时分秒星期等对 Unix 时间戳的转换，内部通过子类复杂的 computeTime() 方法进行计算。
    * 使用 getTime() 方法返回 java.util.Date 类型的时间
    * 使用 getTimeInMillis() 方法返回当前 Unix 时间戳
    * 通过 get(int field) 方法获取其他年月日等单独信息
  
        | 常量 | 含义 |
        |:-:|:-:|
        | Calendar.YEAR  | 年份 |
        | Calendar.MONTH  | 月份 |
        | Calendar.DATE  | 日期 |
        | Calendar.DAY_OF_MONTH  | 日期，和上面字段意义完全相同 |
        | Calendar.HOUR  | 12 小时制的小时 |
        | Calendar.HOUR_OR_DAY  | 24 小时制的小时 |
        | Calendar.MINUTE  | 分钟 |
        | Calendar.SECOND  | 秒 |
        | Calendar.DAY_OF_WEEK  | 星期几 |
        | Calendar.DAY_OF_YEAR  | 今年的第几天 |

    也可以通过多个 set 重载方法设定各种值，同时，add() 方法支持对单个值的加减，从而实现时间推移的计算，传入负数即为减
    
    ```java
    public static void main(String[] args){
        Calendar calendar = Calendar.getInstance();
        System.out.println("当前日期： " + calendar.getTime());
        calendar.add(Calendar.DATE, 10);
        System.out.println("日期+10： " + calendar.getTime());
        calendar.add(Calendar.DATE, -5);
        System.out.println("日期-5： " + calendar.getTime());
    }

    // 输出结果
    当前日期： Fri Apr 10 15:37:46 CST 2020
    日期+10： Mon Apr 20 15:37:46 CST 2020
    日期-5： Wed Apr 15 15:37:46 CST 2020
    ```

`GregorianCalendar` 对象可以直接使用 `isLeapYear(int year)` 接口判断是否闰年。需要注意两点
1. Calendar 中 MONTH 的范围： 0-11（Calendar.JANUARY - Calendar.DECEMBER），因此为避免使用错，<font color=#FF0000>Calendar.JANUARY</font> 来表示一月
2. Calendar 中 DAY_OF_WEEK <font color=#FF0000>星期日是 1</font>，并不是我们习惯的星期一是 1

### SimpleDateFormat

`SimpleDateFormat` 是一个以语言环境敏感的方式来格式化和分析日期的类，SimpleDateFormat允许选择任何用户自定义的日期时间格式来格式化

```java
public static void main(String[] args){
    Date date = new Date();
    System.out.println("格式化之前： " + date);
    SimpleDateFormat ft = new SimpleDateFormat("z yyyy-MM-dd HH:mm:ss");
    System.out.println("格式化之后： " +  ft.format(date));
}

// 输出结果
格式化之前： Fri Apr 10 15:55:42 CST 2020
格式化之后： CST 2020-04-10 15:55:42
```

#### 字符含义 

日期和时间格式由日期和时间模式字符串中的日期和时间模式字符串指定，从 `A` 到 `Z` 和从 `a` 到 `z` 的无引号字母被解释为表示日期或时间字符串组件的模式字母。可以使用单引号`'`引用文本以避免解释。"`''`"表示单引号。所有其他字符都不会被解释；它们只是在格式化期间复制到输出字符串中，或者在解析期间与输入字符串匹配。

定义了以下模式字母（所有其他字符`A` — `Z` 和 `a` - `z` 保留），详细说明见 `SimpleDateFormat` 类 Java Doc 中注释明

| 字母 | 日期或时间组成 | 说明 | 示例 |
|:-:|:-:|---|---|
| G | Era designator【是一个代号】 | Text | AD |
| y（小写） | Year【年】 | Year | 1996; 96 |
| Y | Week year【周年】 | Year | 2009; 09 |
| M | Month in year (context sensitive)【一年中的月份(上下文相关)】 | Month | July; Jul; 07 |
| L | Month in year (standalone form)【一年中的月份(独立形式)】 | Month | July; Jul; 07 |
| w（小写） | Week in year【一年中的第几周】 | Number | 27 |
| W | Week in month【每月的周】 | Number | 2 |
| D | Day in year【一年中的一天】 | Number | 189 |
| d（小写） | Day in month【每月的一天】 | Number | 10 |
| F | Day of week in month【每月的星期几】 | Number | 2 |
| E | Day name in week【星期几】 | Text | Tuesday; Tue |
| u（小写） | Day number of week (1 = Monday, ..., 7 = Sunday)</br> 【星期几(1 =星期一，...，7 =星期日)】 | Number | 1 |
| a（小写） | Am/pm marker【上午/下午标记】 | Text | PM |
| H | Hour in day (0-23)【一天中的小时，0-23表示】 | Number | 0 |
| k（小写） | Hour in day (1-24)【一天中的小时， 1-24表示】 | Number | 24 |
| K | Hour in am/pm (0-11)【上午/下午 0-11表示】 | Number | 0 |
| h（小写） | Hour in am/pm (1-12)【上午/下午 1-12表示】 | Number | 12 |
| m（小写） | Minute in hour【一小时内】 | Number | 30 |
| s（小写） | Second in minute【分钟】 | Number | 55 |
| S | Millisecond【毫秒】 | Number | 978 |
| z（小写） | Time zone【时区】 | General time zone | Pacific Standard Time;</br> PST; GMT-08:00 |
| Z | Time zone【时区】 | RFC 822 time zone | -0800 |
| X | Time zone【时区】 | ISO 8601 time zone | -08; -0800; -08:00 |

#### 格式化示例

给定太平洋时间，2001-07-04 12:08:56，以下示例展示按照自定的格式显示时间

| 日期和时间模式 | 结果 |
|---|---|
| "yyyy.MM.dd G 'at' HH:mm:ss z" | 2001.07.04 AD at 12:08:56 PDT |
| "EEE, MMM d, ''yy" | Wed, Jul 4, '01 |
| "h:mm a" | 12:08 PM |
| "hh 'o''clock' a, zzzz" | 12 o'clock PM, Pacific Daylight Time |
| "K:mm a, z" | 0:08 PM, PDT |
| "yyyyy.MMMMM.dd GGG hh:mm aaa" | 02001.July.04 AD 12:08 PM |
| "EEE, d MMM yyyy HH:mm:ss Z" | Wed, 4 Jul 2001 12:08:56 -0700 |
| "yyMMddHHmmssZ" | 010704120856-0700 |
| "yyyy-MM-dd'T'HH:mm:ss.SSSZ" | 2001-07-04T12:08:56.235-0700 |
| "yyyy-MM-dd'T'HH:mm:ss.SSSXXX" | 2001-07-04T12:08:56.235-07:00 |
| "YYYY-'W'ww-u" | 2001-W27-3 |

### TimeZone

## JDK8+

### Clock
### Instant
### LocalDate
### LocalTime
### LocalDateTime
### OffsetTime
### OffsetDateTime
### ZonedDateTime
### ZoneId
### Year
### Month
### DayOfWeek
### MonthDay
### YearMonth

## 总结

## 附录

* [Java SE 8 日期和时间](https://www.oracle.com/technetwork/cn/articles/java/jf14-date-time-2125367-zhs.html?printOnly=1)
* [循序渐进解读计算机中的时间—应用篇（上）](https://mp.weixin.qq.com/s/oMQ--gLOGMXpblM4g8TnZA)
* [循序渐进解读计算机中的时间—应用篇（下）](https://mp.weixin.qq.com/s/N5Kkky9MyA9b73SM3NVVEQ)
* [JSR310新日期API(二)-日期时间API](http://throwable.club/2019/01/01/java-jsr310-time-api)
* [Java中日期时间相关核心类](https://dslztx.github.io/blog/2018/09/07/Java%E4%B8%AD%E6%97%A5%E6%9C%9F%E6%97%B6%E9%97%B4%E7%9B%B8%E5%85%B3%E6%A0%B8%E5%BF%83%E7%B1%BB)
* [《Java 8 实战》● 第 12 章](https://book.douban.com/subject/26772632)