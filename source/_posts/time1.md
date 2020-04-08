---
title: 时间（一）
date: 2020-04-07 10:01:00
categories: JDK8
tag: [JDK8]
---

{% cq %}
**时间**是一种尺度，在物理定义是标量，借着时间，事件发生之先后可以按 过去-现在-未来 之序列得以确定（时间点），也可以衡量事件持续的期间以及事件之间和间隔长短（时间段） —— 维基百科
{% endcq %}

<!-- more -->

<div class="cleanslate w24tz-current-time w24tz-middle" style="display: inline-block !important; visibility: hidden !important; min-width:300px !important; min-height:145px !important;"><p><a href="//24timezones.com/%E6%9D%AD%E5%B7%9E/%E6%97%B6%E9%97%B4" style="text-decoration: none" class="clock24" id="tz24-1586261878-c1501-eyJob3VydHlwZSI6IjI0Iiwic2hvd2RhdGUiOiIxIiwic2hvd3NlY29uZHMiOiIxIiwiY29udGFpbmVyX2lkIjoiY2xvY2tfYmxvY2tfY2I1ZThjNmY3NjBkMDNhIiwidHlwZSI6ImRiIiwibGFuZyI6InpoIn0=" title="杭州 時間" target="_blank" rel="nofollow" herf="https://24timezones.com/clock-widget/digital"></a></p><div id="clock_block_cb5e8c6f760d03a"></div></div>
<script type="text/javascript" src="//w.24timezones.com/l.js" async></script>

## 单位

时间的基本国际单位是<font color=red>**秒**</font>。定义一秒为 *铯-133原子* 基态两个超精细能级间跃迁辐射振荡9,192,631,770周所持续的时间，其起点为世界时1958年的开始

## 时区

时区(Time Zone)是地球上的区域使用同一个时间定义。1884年在华盛顿召开国际经度会议时，为了克服时间上的混乱，规定将全球划分为24个时区（东西各 12 个时区）。造成时间上的混乱是由于世界各个国家位于地球不同位置上，因此不同国家，特别是东西跨度大的国家日出、日落时间必定有所偏差(这个偏差我们通常叫做时差)

![图片来自维基百科](https://res.cloudinary.com/incoder/image/upload/v1586272206/blog/time-zone.png)

### 理论时区

理论时区以被 15 整除的经线为中心，向东喜两侧延伸 7.5°，即每 15°划分为一个时区。理论时区的时间采用其中央经线（或标准经线）的地方时。所以每差一个时区，区时相差一个小时，相差多少个时区，就相差多少小时

### 法定时区

为了避开国界线，有的时区的形状并不规则，而且比较大的国家以国家内部行政分机型为时区界线。例如，中国跨5个时区，但为了使用方便简单并且全国统一使用一个区时，实际上在中国使用东8区的区时一般称为北京时间作为标准时间

## GMT

格林尼治平均时间（英语：Greenwich Mean Time，GMT）是指位于英国伦敦郊区的皇家格林尼治天文台当地的平太阳时，因为本初子午线被定义为通过那里的经线。  
自1924年2月5日开始，格林尼治天文台负责每隔一小时向全世界发放调时信息。  
格林尼治标准时间的正午是指当平太阳横穿格林尼治子午线时（也就是在格林尼治上空最高点时）的时间。由于地球每天的自转是有些不规则的，而且正在缓慢减速，因此格林尼治平时 **基于天文观测本身的缺陷**，已经被原子钟报时的<font color=red>协调世界时（UTC）所取代</font>。

## UTC

协调世界时（英语：Coordinated Universal Time，法语：Temps Universel Coordonné，简称UTC）是最主要的世界时间标准，其以原子时秒长为基础，在时刻上尽量接近于格林威治标准时间。

中华人民共和国采用ISO 8601:2000的国家标准GB/T 7408-2005《数据元和交换格式 信息交换 日期和时间表示法》中亦称之为协调世界时。

协调世界时是世界上调节时钟和时间的主要时间标准，它与0度经线的平太阳时相差不超过1秒，并不遵守夏令时（**由实验室用足够精确的铯原子钟导出的时间作为原子时，原子时的精确度极高，精度可以达到每2000万年才误差1秒**）。协调世界时是最接近格林威治标准时间（GMT）的几个替代时间系统之一。对于大多数用途来说，UTC时间被认为能与GMT时间互换，但GMT时间已不再被科学界所确定。

## DST

夏时制（英文：Daylight Saving Time），又称夏令时、日光节约时间，是一种在夏季月份牺牲正常的日出时间，而将时间调快的做法。通常使用夏时制的地区，会在接近春季开始的时候，将时间调快一小时，并在秋季调回正常时间。目前中国已经弃用DST

## ISO-8601

国际标准ISO 8601，是国际标准化组织的日期和时间的表示方法，全称为《数据存储和交换形式·信息交换·日期和时间的表示方法》。目前是2004年12月1日发行的第三版“ISO8601:2004”以替代1998年的第一版“ISO8601:1998”与2000年的第二版“ISO8601:2000”。

## 计算机中的时间

### JSR-310

JSR（Java Specification Requests）是，310 是一个编号，在 JDK8 中通过这个标准提供了新的改进日期时间的 API

详细做 Java 开发，对于JDK 的时间 API（小于JDK8版本）肯定都是吐槽不少，主要问题体现在以下几个方面

1. 最开始，Date 既要承载日期信息，又要做日期之间的转换，还要做不同日期格式的显示，职责较繁杂
   而后 JDK1.1 开始，将三项职责分开了
   1. 使用 Calendar 类实现日期和时间字段之间的转换
   2. 使用 DateFormat 类来格式化和分析日期字符串
   3. Date 只用来承载日期和时间信息

    尽管已经区分了各自的职责，但在使用时任然是很不方便
2. 谜之 year 和 month
   ```java
    // 定义的月是 0-11表示 1-12 月

    Date date = new Date(2020, 4, 8);
    // 输出结果：Sat May 08 00:00:00 CST 3920
    // 年竟然是 3920 = 2020 + 1900
    // 月竟然是 May = 4 + 1
    System.out.println(date);

    Calendar calendar = Calendar.getInstance();
    calendar.set(2020, 4, 8);
    // 输出结果：Fri May 08 11:27:11 CST 2020
    // 年输出：和预想一致
    // 月输出：还和 Date 一样是输入月份 +1 
    System.out.println(calendar.getTime());
   ```
3. Date 与 Calendar 类中的所有属性是可变的
   ```java
    // 计算两个日期之间的天数
    public static void main(String[] args) {
        Calendar birth = Calendar.getInstance();
        birth.set(1975, Calendar.MAY, 26);
        Calendar now = Calendar.getInstance();
        System.out.println(daysBetween(birth, now));
        // 连续计算两个 Date 实例的话，第二次会取得 0，因为 Calendar 状态是可变的
        System.out.println(daysBetween(birth, now)); // 显示 0？
    }

    public static long daysBetween(Calendar begin, Calendar end) {
        long daysBetween = 0;
        while(begin.before(end)) {
            begin.add(Calendar.DAY_OF_MONTH, 1);
            daysBetween++;
        }
        return daysBetween;
    }
   ```

>JSR 310的规范领导者 Stephen Colebourne，同时也是 [Joda-Time](https://www.joda.org/joda-time/) 的创建者，JSR 310是在Joda-Time的基础上建立的，参考了绝大部分的API

### 2038年问题

![图片来自维基百科](https://res.cloudinary.com/incoder/image/upload/v1586313844/blog/year_2038_problem.gif)

现时大部分使用UNIX的系统都是32位的，即它们会以32位有符号整数表示时间类型time_t。因此它可以表示136年的秒数。表示协调世界时间1901年12月13星期五20时45分52秒至2038年1月19日3时14分07秒（二进制：01111111 11111111 11111111 11111111，0x7FFF:FFFF），在下一秒二进制数字会是10000000 00000000 00000000 00000000（0x8000:0000），这是负数，因此各系统会把时间误解作1901年12月13日20时45分52秒（亦有可能回归到1970年）。这时可能会令软件发生问题，导致系统瘫痪

当前的解决方案  
把系统由32位转为64位系统。在64位系统下，此时间最多可以表示到292,277,026,596年12月4日15时30分08秒

## 附录

* [时间 • 维基百科](https://zh.wikipedia.org/wiki/%E6%97%B6%E9%97%B4)
* [时区 • 维基百科](https://zh.wikipedia.org/wiki/%E6%97%B6%E5%8C%BA)
* [UNIX时间 • 维基百科](https://zh.wikipedia.org/wiki/UNIX%E6%97%B6%E9%97%B4)
* [ISO 8601 • 维基百科](https://zh.wikipedia.org/wiki/ISO_8601)
* [格林尼治标准时间（GMT） •  维基百科](https://zh.wikipedia.org/wiki/%E6%A0%BC%E6%9E%97%E5%B0%BC%E6%B2%BB%E6%A8%99%E6%BA%96%E6%99%82%E9%96%93)
* [协调世界时（UTC） • 维基百科](https://zh.wikipedia.org/wiki/%E5%8D%8F%E8%B0%83%E4%B8%96%E7%95%8C%E6%97%B6)
* [JSR 310: Date and Time API](https://jcp.org/en/jsr/detail?id=310)
* [JSR310-新日期API(完结篇)-生产实战](http://throwable.club/2020/03/02/java-jsr310-in-action/)