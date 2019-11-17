---
title: BigDecimal 
date: 2019-10-20 10:04:00
categories: Util
tag: [Util]
---

float 和 double 同样也是可以表示浮点数，为啥在对于要求精确的进度计算时，尤其是关于币值相关，都采用 BigDecimal 类型来处理？

1. float 和 double 类型的主要设计目标是为了科学计算和工程计算。他们执行二进制浮点运算，这是为了在广域数值范围上提供较为精确的 **快速近似** 计算而精心设计的。然而，它们<font color=red>没有提供完全精确</font>的结果，所以不应该用于要求精确结果的场合。——《Effective Java》
2. float 精度 7 位，double 精度 16 位

综上所述，对于精确计算时，就不能采用 float 和 double 来计算了，而是 <font color=red>正确的使用 BigDecimal</font> 时，结果才是精确的，为什么会这么说，那就跟着我一块来深入了解 BigDecimal，我们查看`java.math`路径下，除了 BigDecimal 还有 BigInteger ，因此，我们先去了解 BigInteger

## BigInteger

Java 中，由 CPU 原生提供的整形最大范围是 64 位`long`类型整数。使用`long`类型整数可以直接通过 CPU 指令进行计算，速度非常快。如果使用的整数范围超过了`long`类型的范围怎么办？这时就只能用软件来模拟一个大整数。BigInteger表示不可变的任意精度的整数（继承`Number`）。BigInteger 内部用一个 `int[]` 数组来模拟一个非常大的整数

### 构造方法

* BigInteger(byte[] val)：将包含 BigInteger 的二进制补码表示形式的 byte 数组转换为 BigInteger
* BigInteger(int signum, byte[] magnitude)：将 BigInteger 的符号-数量表示形式转换为 BigInteger。
* BigInteger(int bitLength, int certainty, Random rnd)：构造一个随机生成的正 BigInteger，它可能是一个具有指定 bitLength 的素数
* BigInteger(int numBits, Random rnd)：构造一个随机生成的 BigInteger，它是在 0 到 (2numBits - 1)（包括）范围内均匀分布的值
* <font color=red>BigInteger(String val)</font>：将 BigInteger 的十进制字符串表示形式转换为 BigInteger，常用构造方法
* BigInteger(String val, int radix)：将指定基数的 BigInteger 的字符串表示形式转换为 BigInteger

```java
BigInteger bi = new BigInteger("1234567890");
// 计算出 bi⁵ = 2867971860299718107233761438093672048294900000
System.out.println(bi.pow(5)); 
```

### 常用运算方法

对于加减乘除等运算，BigInteger 提供了对应的方法

```java
BigInteger a = new BigInteger("1234567890");
BigInteger b = new BigInteger("9876543210");
// a+b = 11111111100
System.out.println("a+b = " + a.add(b));
// a-b = -8641975320
System.out.println("a-b = " + a.subtract(b));
// a*b = 12193263111263526900
System.out.println("a*b = " + a.multiply(b));
// a/b = 0
System.out.println("a/b = " + a.divide(b));
```

### 转换

和 `long` 类型整数运算比，`BigInteger`不会有范围限制，但缺点是速度比较慢。

```java
BigInteger i = new BigInteger("123456789000");
// 123456789000
System.out.println(i.longValue()); 
// java.lang.ArithmeticException: BigInteger out of long range
System.out.println(i.multiply(i).longValueExact()); 
```

>使用 `longValueExact()` 方法时，如果超出了 `long` 类型的范围，会抛出 `ArithmeticException`

`BigInteger` 和 `Integer` 、 `Long` 一样，也是不可变类，并且也继承自 `Number` 类。因为 `Number` 定义了转换为基本类型的几个方法：

* 转换为`byte`：`byteValue()`
* 转换为`short`：`shortValue()`
* 转换为`int`：`intValue()`
* 转换为`long`：`longValue()`
* 转换为`float`：`floatValue()`
* 转换为`double`：`doubleValue()`

通过上述方法，可以把 `BigInteger` 转换为基本类型。如果 `BigInteger` 表示的范围超过了基本类型，转换时将丢失高位信息，即结果不一定准确；因此，如果需要 <font color=red>准确的转换成基本类型</font>，可以使用 `intValueExact()`、`longValueExact()` 等方法，在转换时如果超出范围，将直接抛出 `ArithmeticException`异常

## BigDecimal

`BigDecimal` 与 `BigInteger`类似，`BigDecimal` 表示一个<font color=red>任意大小且精度完全准确</font>的浮点数。BigDecimal 是由任意精度的整数非标度值（unscaled value）和 32 位的整数标度（scale）组成，通常用于币值的计算。

### 构造方法

BigDecimal 拥有16 个构造方法，常用如下三种

* BigDecimal BigDecimal(double d); // 不允许使用，精度不能保证
* BigDecimal BigDecimal(String s); // 常用，推荐使用
* static BigDecimal valueOf(double d); // 常用，推荐使用

```java
BigDecimal bigDecimal = new BigDecimal(2);
BigDecimal bString = new BigDecimal("2.3");
BigDecimal bDouble = new BigDecimal(2.3);
BigDecimal bDouble1 = BigDecimal.valueOf(2.3);
// 输出：bigDecimal = 2
System.out.println("bigDecimal = " + bigDecimal);
// 输出：bString = 2.3
System.out.println("bString = " + bString);
// 输出：bDouble = 2.29999999999999982236431605997495353221893310546875
System.out.println("bDouble = " + bDouble);
// 输出：bDouble1 = 2.3
System.out.println("bDouble1 = " + bDouble1);
```

* 参数类型为 double 的构造方法的结果有一定的不可预知性；
* 参数类型为 String 的构造方法的结果是<font color=red>完全可预知</font>的，因此我们在编写时尽量都用 String 的构造方法
* 当 double 必须用作 BigDecimal 的源时可以用 BigDecimal 的静态方法 value()

### 常用方法

```java
BigDecimal a = new BigDecimal("1234567890.56789");
BigDecimal b = new BigDecimal("9876543210.01234");
// a+b = 11111111100.58023
System.out.println("a+b = " + a.add(b));
// a-b = -8641975319.44445
System.out.println("a-b = " + a.subtract(b));
// a*b = 12193263116887551591.2965077626
System.out.println("a*b = " + a.multiply(b));
// 报错：ArithmeticException，因为除不尽
// System.out.println("a/b = " + a.divide(b));
// 保留10位小数并四舍五入
System.out.println("a/b = " + a.divide(b, 10, RoundingMode.HALF_UP));
```

### 转换

与`BigInteger`相同

### 舍入模式

* ROUND_CEILING：向 **正无限大方向舍入** 的舍入模式。如果结果为正，则舍入行为类似于 RoundingMode.UP；如果结果为负，则舍入行为类似于 RoundingMode.DOWN
    ```java
    5.5  =>  6 
    1.1  =>  2
    -1.0  =>  -1 
    -2.5  =>  -2
    BigDecimal a1 = new BigDecimal("5.5");
    BigDecimal a2 = new BigDecimal("1.1");
    BigDecimal a3 = new BigDecimal("-1.0");
    BigDecimal a4 = new BigDecimal("-2.5");
    System.out.println("ROUND_CEILING模式：" + a1.setScale(0, RoundingMode.CEILING));
    System.out.println("ROUND_CEILING模式：" + a2.setScale(0, RoundingMode.CEILING));
    System.out.println("ROUND_CEILING模式：" + a3.setScale(0, RoundingMode.CEILING));
    System.out.println("ROUND_CEILING模式：" + a4.setScale(0, RoundingMode.CEILING));
    ```
* RoundingMode.DOWN：向 **零方向舍入** 的舍入模式。从不对舍弃部分前面的数字加 1（即截尾）。注意，此舍入模式始终不会增加计算值的绝对值
    ```java
    5.5  =>  5 
    1.1  =>  1 
    -1.0  =>  -1 
    -2.5  =>  -2
    BigDecimal a1 = new BigDecimal("5.5");
    BigDecimal a2 = new BigDecimal("1.1");
    BigDecimal a3 = new BigDecimal("-1.0");
    BigDecimal a4 = new BigDecimal("-2.5");
    System.out.println("DOWN模式：" + a1.setScale(0, RoundingMode.DOWN));
    System.out.println("DOWN模式：" + a2.setScale(0, RoundingMode.DOWN));
    System.out.println("DOWN模式：" + a3.setScale(0, RoundingMode.DOWN));
    System.out.println("DOWN模式：" + a4.setScale(0, RoundingMode.DOWN));
    ```
* RoundingMode.FLOOR（此舍入模式始终不会增加计算值）：向 **负无限大方向舍入** 的舍入模式。如果结果为正，则舍入行为类似于 RoundingMode.DOWN；如果结果为负，则舍入行为类似于 RoundingMode.UP
    ```java
    5.5  =>  5 
    1.1  =>  1
    -1.0  =>  -1 
    -2.5  =>  -3
    BigDecimal a1 = new BigDecimal("5.5");
    BigDecimal a2 = new BigDecimal("1.1");
    BigDecimal a3 = new BigDecimal("-1.0");
    BigDecimal a4 = new BigDecimal("-2.5");
    System.out.println("FLOOR模式：" + a1.setScale(0, RoundingMode.FLOOR));
    System.out.println("FLOOR模式：" + a2.setScale(0, RoundingMode.FLOOR));
    System.out.println("FLOOR模式：" + a3.setScale(0, RoundingMode.FLOOR));
    System.out.println("FLOOR模式：" + a4.setScale(0, RoundingMode.FLOOR));
    ```
* RoundingMode.HALF_DOWN：向 **最接近数字方向舍入** 的舍入模式，如果与两个相邻数字的距离相等，则向 **下舍入** 。如果被舍弃部分 > 0.5，则舍入行为同 RoundingMode.UP；否则舍入行为同 RoundingMode.DOWN
    ```java
    5.5  =>  5
    1.1  =>  1 
    -1.1  =>  -1
    -2.5  =>  -2
    BigDecimal a1 = new BigDecimal("5.5");
    BigDecimal a2 = new BigDecimal("1.1");
    BigDecimal a3 = new BigDecimal("-1.0");
    BigDecimal a4 = new BigDecimal("-2.5");
    System.out.println("HALF_DOWN模式：" + a1.setScale(0, RoundingMode.HALF_DOWN));
    System.out.println("HALF_DOWN模式：" + a2.setScale(0, RoundingMode.HALF_DOWN));
    System.out.println("HALF_DOWN模式：" + a3.setScale(0, RoundingMode.HALF_DOWN));
    System.out.println("HALF_DOWN模式：" + a4.setScale(0, RoundingMode.HALF_DOWN));
    ```
* RoundingMode.HALF_EVEN：向 **最接近数字方向舍入** 的舍入模式，如果与两个相邻数字的距离相等，则向 **相邻的偶数舍入** 。如果舍弃部分左边的数字为奇数，则舍入行为同 RoundingMode.HALF_UP；如果为偶数，则舍入行为同 RoundingMode.HALF_DOWN
    ```java
    5.5  =>  6 
    1.1  =>  1 
    -1.0  =>  -1
    -2.5  =>  -2
    BigDecimal a1 = new BigDecimal("5.5");
    BigDecimal a2 = new BigDecimal("1.1");
    BigDecimal a3 = new BigDecimal("-1.0");
    BigDecimal a4 = new BigDecimal("-2.5");
    System.out.println("HALF_EVEN模式：" + a1.setScale(0, RoundingMode.HALF_EVEN));
    System.out.println("HALF_EVEN模式：" + a2.setScale(0, RoundingMode.HALF_EVEN));
    System.out.println("HALF_EVEN模式：" + a3.setScale(0, RoundingMode.HALF_EVEN));
    System.out.println("HALF_EVEN模式：" + a4.setScale(0, RoundingMode.HALF_EVEN));
    ```
* <font color=red>RoundingMode.HALF_UP</font>（此舍入模式就是通常学校里讲的四舍五入）：向最接近数字方向舍入的舍入模式，如果与两个相邻数字的距离相等，则向上舍入。如果被舍弃部分 >= 0.5，则舍入行为同 RoundingMode.UP；否则舍入行为同 RoundingMode.DOWN
    ```java
    5.5  =>  6
    1.1  =>  1 
    -1.1  =>  -1 
    -2.5  =>  -3 
    BigDecimal a1 = new BigDecimal("5.5");
    BigDecimal a2 = new BigDecimal("1.1");
    BigDecimal a3 = new BigDecimal("-1.1");
    BigDecimal a4 = new BigDecimal("-2.5");
    System.out.println("HALF_UP模式：" + a1.setScale(0, RoundingMode.HALF_UP));
    System.out.println("HALF_UP模式：" + a2.setScale(0, RoundingMode.HALF_UP));
    System.out.println("HALF_UP模式：" + a3.setScale(0, RoundingMode.HALF_UP));
    System.out.println("HALF_UP模式：" + a4.setScale(0, RoundingMode.HALF_UP));
    ```
* RoundingMode.UNNECESSARY：用于断言请求的操作具有精确结果的舍入模式，因此不需要舍入。如果对生成精确结果的操作指定此舍入模式，则抛出 ArithmeticException
    ```java
    1.5  =>  抛出 ArithmeticException
    1.1  =>  抛出 ArithmeticException
    1.0  =>  1
    -1.1  =>抛出 ArithmeticException 
    -1.6  =>  抛出 ArithmeticException 
    ```
* RoundingMode.UP：**远离零方向舍入** 的舍入模式。始终对非零舍弃部分前面的数字加 1。注意，此舍入模式始终不会减少计算值的绝对值
    ```java
    5.5  =>  6 
    1.1  =>  2 
    -1.0  =>  -1
    -2.5  =>  -3
    BigDecimal a1 = new BigDecimal("5.5");
    BigDecimal a2 = new BigDecimal("1.1");
    BigDecimal a3 = new BigDecimal("-1.0");
    BigDecimal a4 = new BigDecimal("-2.5");
    System.out.println("UP模式：" + a1.setScale(0, RoundingMode.UP));
    System.out.println("UP模式：" + a2.setScale(0, RoundingMode.UP));
    System.out.println("UP模式：" + a3.setScale(0, RoundingMode.UP));
    System.out.println("UP模式：" + a4.setScale(0, RoundingMode.UP));
    ```

### 格式化

DecimalFormat 解析
| 符号      | 位置       | 描叙                                                                                                                       |
|-----------|----------|--------------------------------------------------------------------------------------------------------------------------|
| 0         | 数字       | 阿拉伯数字，如果不存在则显示0                                                                                               |
| #         | 数字       | 阿拉伯数字，如果不存在不显示0                                                                                               |
| .         | 数字       | 小数分隔符或货币小数分隔符                                                                                                 |
| ,         | 数字       | 分组分隔符                                                                                                                 |
| E         | 数字       | 分隔科学计数法中的尾数和指数。在前缀或后缀中无需加引号                                                                      |
| -         | 数字       | 负号                                                                                                                       |
| ;         | 子模式边界 | 分隔正数和负数子模式                                                                                                       |
| %         | 子模式边界 | 乘以 100 并显示为百分数                                                                                                    |
| \u2030    | 子模式边界 | 乘以 1000 并显示为千分数                                                                                                   |
| ¤(\u00A4) | 子模式边界 | 货币记号，由货币符号替换。如果两个同时出现，则用国际货币符号替换。如果出现在某个模式中，则使用货币小数分隔符，而不使用小数分隔符 |
| '         | 子模式边界 | 用于在前缀或或后缀中为特殊字符加引号，例如 "'#'#"将 123 格式化为 "#123"。要创建单引号本身，请连续使用两个单引号："# o''clock"  |

```java
// 建立货币格式化引用
NumberFormat currency = NumberFormat.getCurrencyInstance(); 
// 建立百分比格式化引用
NumberFormat percent = NumberFormat.getPercentInstance(); 
// 百分比小数点最多3位
percent.setMaximumFractionDigits(3); 
BigDecimal loanAmount = new BigDecimal("150.48");
BigDecimal interestRate = new BigDecimal("0.008");
BigDecimal interest = loanAmount.multiply(interestRate);
// 贷款金额: ￥150.48
System.out.println("贷款金额:\t" + currency.format(loanAmount)); 
// 利率: 0.8%
System.out.println("利率:\t" + percent.format(interestRate)); 
// 利息: ￥1.20
System.out.println("利息:\t" + currency.format(interest)); 

//===============================================================
DecimalFormat df = new DecimalFormat();
// 格式化之前的数字
double data = 1234.56789; 
 
// 1.定义要显示的数字的格式（这种方式会四舍五入）
String style = "0.0";
df.applyPattern(style);
// 1-->1234.6
System.out.println("1-->" + df.format(data));  

// 2.在格式后添加诸如单位等字符
style = "00000.000 kg";
df.applyPattern(style);
// 2-->01234.568 kg
System.out.println("2-->" + df.format(data));  

// 3.模式中的"#"表示如果该位存在字符，则显示字符，如果不存在，则不显示。
style = "##000.000 kg";
df.applyPattern(style);
// 3-->1234.568 kg
System.out.println("3-->" + df.format(data));  
 
// 4.模式中的"-"表示输出为负数，要放在最前面
style = "-000.000";
df.applyPattern(style);
// 4-->-1234.568
System.out.println("4-->" + df.format(data)); 
 
// 5.模式中的","在数字中添加逗号，方便读数字
style = "-0,000.0#";
df.applyPattern(style);
// 5-->-1,234.57
System.out.println("5-->" + df.format(data));  
 
// 6.模式中的"E"表示输出为指数，"E"之前的字符串是底数的格式，
// "E"之后的是字符串是指数的格式
style = "0.00E000";
df.applyPattern(style);
// 6-->1.23E003
System.out.println("6-->" + df.format(data));  
 
// 7.模式中的"%"表示乘以100并显示为百分数，要放在最后。
style = "0.00%";
df.applyPattern(style);
// 7-->123456.79%
System.out.println("7-->" + df.format(data));  
 
// 8.模式中的"\u2030"表示乘以1000并显示为千分数，要放在最后。
style = "0.00\u2030";
// 在构造函数中设置数字格式
DecimalFormat df1 = new DecimalFormat(style);
// df.applyPattern(style);
// 8-->1234567.89‰
System.out.println("8-->" + df1.format(data));  
```


### 其他

#### 科学计数法问题

```java
BigDecimal b = new BigDecimal("0.0000001");
// 输出结果：1E-7
System.out.println(b.toString());
// 输出结果：0.0000001
System.out.println(b.toPlainString());
```

>当 BigDecimal的值 小于一定值时(测试时发现：小于等于0.0000001)时，则会被记为科学计数法。可以使用 toPlainString()方法显示原来的值

#### 去除无效的 0

```java
BigDecimal b = new BigDecimal("0.000000100000000");
// 1E-7
System.out.println(b.stripTrailingZeros().toString());
// 0.0000001
System.out.println(b.stripTrailingZeros().toPlainString());
```

>stripTrailingZeros() 方法的本质是去除掉多余的0，其返回数据类型是BigDecimal，同样的在使用时需要注意科学技术法的问题

#### 保留小数位

```java
double num = 13.154215;
// 方式一
DecimalFormat df1 = new DecimalFormat("0.00");
String str = df1.format(num);
// 13.15
System.out.println(str);  
// 方式二
// #.00 表示两位小数 #.0000四位小数
DecimalFormat df2 =new DecimalFormat("#.00");
String str2 =df2.format(num);
// 13.15
System.out.println(str2);  
// 方式三
// %.2f %. 表示 小数点前任意位数   2 表示两位小数 格式后的结果为f 表示浮点型
String result = String.format("%.2f", num);
// 13.15
System.out.println(result);  
```

#### 大小比较

在比较两个BigDecimal的值是否相等时，要特别注意，使用 `equals()` 方法不但要求两个BigDecimal的 **值相等** ，还要求它们的 **scale()相等**，因此如果只是比较数值的大小，必须使用 `compareTo()` 方法来比较，它根据两个值的大小分别返回负数、正数和0，分别表示小于、大于和等于

```java
BigDecimal d1 = new BigDecimal("123.456");
BigDecimal d2 = new BigDecimal("123.45600");
// false,因为scale不同
System.out.println(d1.equals(d2)); 
// true,因为d2去除尾部0后scale变为2
System.out.println(d1.equals(d2.stripTrailingZeros())); 
// 0
System.out.println(d1.compareTo(d2)); 
```

## 附录

* [Java中浮点类型的精度问题 double float](https://www.cnblogs.com/baiqiantao/p/7449176.html)
* [廖雪峰 BigInteger](https://www.liaoxuefeng.com/wiki/1252599548343744/1279767986831393)
* [廖雪峰 BigDecimal](https://www.liaoxuefeng.com/wiki/1252599548343744/1279768011997217)
* [BigDecimal](https://doc.yonyoucloud.com/doc/jdk6-api-zh/java/math/BigDecimal.html)
* [Java 9中新的货币API](http://it.deepinmind.com/java/2015/01/06/looking-into-the-java-9-money-and-currency-api.html)