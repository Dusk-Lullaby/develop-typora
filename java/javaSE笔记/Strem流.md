# `Stream` 流

## 1. `Stream`流

### 1.1 管道

管道：

管道是一系列的聚合操作。管道包含以下组件：

1. 源：可以是集合，数组，生成器函数或I / O通道
2. 零个或多个中间操作。诸如过滤器之类的中间操作产生新的流。
3. 终结操作。终结操作（例如`ForEach`）会产生非流结果，例如原始值（如双精度值），集合或者在`forEach`的情况下根本没有任何价值。



流：

流是一系列元素。与集合不同，它不是存储元素的数据结构。取而代之的是，流通过管道携带来自源的值。

筛选器操作返回一个新流，该流包含于筛选条件（此操作的参数）匹配的元素。



### 1.2 如何获取`Stream`流

<font color = "blue">  `Collection`</font>

```java
default Stream<E> stream();
```

<font color = "blue">`Stream`接口</font>

```java
// 获取流
public static<T> Stream<T> of(T...values);
// 将两个流拼接形成一个新的流
public static<T> Stream<T> concat(Stream<? extends T> a, Stream<? extends T> b);
```

<font color = "blue">示例</font>

```java
package com.lullaby.stream;

import java.util.Arrays;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.stream.Stream;

public class StreamTest {

    public static void main(String[] args) {
        List<Integer> number1 = Arrays.asList(1, 2, 3, 4, 5);
        Stream<Integer> s1 = number1.stream();

        Stream<Integer> s2 = Stream.of(6, 7, 8,9, 10);

        Stream<Integer> s3 = Stream.concat(s1, s2);

        Map<String, Integer> map = new HashMap<>();
        map.put("a", 1);
        map.put("b", 2);
        Stream<Map.Entry<String, Integer>> s4 = map.entrySet().stream();
    }
}
```

```java
package com.lullaby.stream;

import java.util.Arrays;
import java.util.stream.DoubleStream;
import java.util.stream.IntStream;
import java.util.stream.LongStream;
import java.util.stream.Stream;

public class NumberStream {

    public static void main(String[] args) {
        Stream<String> s1 = Stream.of("1", "2", "3", "4");
        IntStream s2 = s1.mapToInt(Integer::parseInt);
        int[] arr = s2.toArray();
        System.out.println(Arrays.toString(arr));

        LongStream s3 = Stream.of("1", "2", "3", "4").mapToLong(Long::parseLong);
        long[] arr2 = s3.toArray();
        System.out.println(Arrays.toString(arr2));

        DoubleStream s4 = Stream.of("1", "2", "3", "4").mapToDouble(Double::parseDouble);
        double[] arr3 = s4.toArray();
        System.out.println(Arrays.toString(arr3));
    }
}
```



### 1.3 `Stream`流中间聚合操作

<font color = "blue">`Stream接口`</font>

```java
Stream<T> filter(Predicate<? super T> predicate);	// 根据给定的条件筛选流中的元素
<R> stream<R> map(Function<? super T, ? extends R> mapper);	// 将流中的元素进行类型转换
Stream<T> distinct();	// 去重
Stream<T> sorted();	// 排序，如果存储元素没有实现Comparable或者相关集合没有提供Comparator将抛出异常
Stream<T> limit(long maxSize);	// 根据给定的上限，获取流中的元素
Stream<T> skip(long n);	// 跳过给定数量的元素

IntStream mapToInt(ToIntFunction<? super T> mapper);	// 将流中全部元素转为整数
LongStream mapToLong(ToLongFunction<? super T> mapper);	// 将流中全部元素转为长整数
DoubleStream mapToDouble(ToDoubleFunction<? super T> mapper);	// 将流中元素全部转为双精度浮点数
```

<font color = "blue">示例</font>

```java
package com.lullaby.stream;

import java.util.stream.Stream;

public class AggregateOperation {

    public static void main(String[] args) {
//        List<Integer> numbers = List.of(30, 21, 58, 67, 90, 72);
//        for (Integer number : numbers) {
//            System.out.println(number);
//        }

        Stream<Integer> s1 = Stream.of(30, 21, 58, 67, 90, 72, 58, 30, 80);
//        s1.filter(new Predicate<Integer>() {
//            @Override
//            public boolean test(Integer integer) {
//                return integer % 2 == 0;
//            }
//        });
//        Stream<String> s2 = s1.filter(number -> number % 2 == 0).distinct().skip(1).sorted().limit(2).map(new Function<Integer, String >() {
//            @Override
//            public String apply(Integer integer) {
//                return "字符串" + integer;
//            }
//        });
        Stream<String> s2 = s1.filter(number -> number % 2 == 0).distinct().skip(1).sorted().limit(2).map(integer -> "字符串" + integer);
        s2.forEach(System.out::println);
        // 将流中的元素收集为一个List集合,此时流就已经消亡了
//        List<Integer> existNumbers = s2.collect(Collectors.toList());
//        for (Integer number : existNumbers) {
//            System.out.println(number);
//        }
        // 在使用流进行收集操作将报错
//        Set<Integer> set = s2.collect(Collectors.toSet());
//        for (Integer number : set) {
//            System.out.println(number);
//        }
//        Integer[] arr = s2.toArray(new IntFunction<Integer[]>() {
//            @Override
//            public Integer[] apply(int value) {
//                return new Integer[value];
//            }
//        });
        // 在使用流进行收集操作将报错
//        Integer[] arr = s2.toArray(Integer[]::new);
//        for (Integer number : arr) {
//            System.out.println(number);
//        }
    }
}
```



### 1.4 `Stream`流终结操作

```java
void forEach(Consumer<? super T> action);	// 遍历操作流中元素
<A> A[] toArray(IntFunction<A[]> generator);	// 将流中元素按照给定的转换方式转为数组
<R, A> R collect(Collector<? super T, A, R> collector);	// 将流中的元素按照给定方式搜集起来

Optional<T> min(Comparator<? super T> comparator);	// 根据给定的排序方式获取流中最小元素
Optional<T> max(Comparator<? super T> comparator);	// 根据给定的排序方式获取流中最大元素
Optional<T> findFirst();	// 获取流中第一个元素

long count();	// 获取流中元素数量
boolean anyMatch(Predicate<? super T> predicate);	// 检测流中是否存在给定条件的元素
boolean allMatch(Prediacte<? super T> predicate);	// 检测流中是否全部满足给定条件
boolean noneMatch(Predicate<? super T> predicate);	// 检测流中是否全部不满足给定条件
```

<font color = "blue"> 示例</font>

```java
package com.lullaby.stream;

import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;

public class AggregateOperation {

    public static void main(String[] args) {
        List<String> numbers = Arrays.asList("30", "21", "58", "67", "90", "72");
//        Stream<String> s = numbers.stream();
//        Optional<String> first = s.findFirst();
//        System.out.println(first.get());
//        Optional<String> optional = s.max(String::compareTo);
//        String max = optional.get();
//        System.out.println(max);
//        System.out.println(s.count());

        boolean exist1 = numbers.stream().map(Integer::parseInt).anyMatch(integer -> integer % 2 == 1);
        System.out.println(exist1);

        boolean exist2 = numbers.stream().map(Integer::parseInt).allMatch(integer -> integer % 2 == 1);
        System.out.println(exist2);

        boolean exist3 = numbers.stream().map(Integer::parseInt).noneMatch(integer -> integer % 2 == 1);
        System.out.println(exist3);
    }
}
```



### 1.5 `Stream`流聚合操作与迭代器的区别

聚合操作（如`forEach`）似乎像迭代器。但是，它们有几个基本的差异：

它们使用内部迭代器：聚合操作不包含诸如`next`的方法来指示它们处理集合的下一个元素。使用内部委托，你的应用程序确定要迭代的集合，而`JDK`确定如何迭代该集合。通过外部迭代，你的应用程序既可以确定要迭代的集合，又可以确定迭代的方式。但是，外部迭代只能顺序地迭代集合的元素。内部迭代没有此限制。它们可以轻松地利用并行计算的优势。这涉及将问题分为子问题，同时解决这些问题，然后将解决方案的结果组合到子问题中。

它们处理流中的元素：聚合操作从流中而不是直接从集合中处理元素。因此，它们也称为流操作。

它们支持将行为作为参数：你可以将`Lambda`表达式指定为大多数聚合操作的参数。这使你可以自定义特定聚合操作的行为。



<font color = "blue">面试题</font>

使用`Stream`流将一个基本数据类型的数组，转换为包装类型的数组，再将包装类型的数组转换为基本类型数组。

```java
package com.lullaby.stream;

import java.util.Arrays;
import java.util.stream.DoubleStream;
import java.util.stream.IntStream;
import java.util.stream.LongStream;
import java.util.stream.Stream;

public class NumberStream {

    public static void main(String[] args) {
        int[] intArr1 = {1, 2, 3, 4};
//        Integer[] intArr2 = Arrays.stream(intArr1).mapToObj(i -> i).toArray(Integer[]::new);
        Integer[] intArr2 = Arrays.stream(intArr1).boxed().toArray(Integer[]::new);

        Integer[] intArr3 = {1,2,3,4,5};
        int[] intArr4 = Arrays.stream(intArr3).mapToInt(Integer::intValue).toArray();
    }
}
```



## 2. 包装类

### 2.1 什么是包装类

不论怎样，总有理由使用对象代替原始数据类型，并且Java平台为每种原始数据类型提供了包装类。这些类将“原始数据类型”包装在对象中。

| 基本数据类型 | 包装类  | 基本数据类型 | 包装类    |
| ------------ | ------- | ------------ | --------- |
| int          | Integer | char         | Character |
| byte         | Byte    | boolean      | Boolean   |
| short        | Short   | float        | Float     |
| long         | Long    | double       | Double    |



### 2.2 自动装箱和自动拆箱

<font color = "blue">自动装箱</font>

通常，包装是由编译器完成的，如果你在期望一个对象的地方使用原始数据类型，则编译器会为你将原始数据类型放入其他包装类中。

<font color = "blue">自动装箱方法</font>

```java
包装类名.valueOf(原始数据类型的值);
```

<font color ="blue">示例</font>

```java
// 变量num期望获取一个整数对象，但在赋值时给定的时一个基本数据类型，此时编译器会将int值5进行包装
// 调用的是Integer.valueOf(5);
Integer num = 5;
```

<font color = "blue">自动拆箱</font>

类似的，如果在期望使用基本数据类型的情况下使用原始包装类型，则编译器会为你解包该对象

<font color = "blue">自动拆箱方法</font>

```java
包装类对象.xxxValue();
```

<font color = "blue">示例</font>

```java
Integer num = new Integer(10);
// 变量a期望获取一个基本数据类型的值，但赋值时给定的是一个引用数据类型的对象
// 此时编译器会将这个引用数据类型的对象中存储的值取出来，然后赋值给a，调用的是num.intValue();
int a = num;
```



```java
package com.lullaby.box;

public class BoxTest {

    public static void main(String[] args) {
        // 变量number1需要的是一个对象，但赋值的时候给的是一个基本数据类型int值
        // 编译器就会将这个int值进行包装，调用Integer.valueOf(5)进行包装
        Integer number1 = 5;    // Integer.valueOf(5)

        // 变量number2需要的是一个基本数据类型值，但是赋值的时候给的却是一个对象
        // 编译器就会将这个对象进行解包，调用number1.intValue();进行解包
        int number2 = number1;

        Double d1 = 5.0;    // Double.valueOf(5.0)
        double d2 = d1;     // Double.doubleValue()
    }
}
```



### 2.3 字符串转数字的方法

`Integer.parseInt("123")`将字符串类型的数字转换为整数

`Long.parseLong("123")`将字符串类型的数字转换为长整数

`Byte.parseByte("13")`将字符串类型的数字转换为字节

`Short.parseShort("13")`将字符串类型的数字转换为短整数

`Float.parseFloat("12.0f")`将字符串类型的数字转换为单精度浮点数

`Double.parseDouble("123")`将字符串类型的数字转换为双精度浮点数

`Boolean.parseBoolean("true")`将字符串类型的数字转换为布尔值

<font color = "blue">示例</font>

```java
int num = Integer.parseInt("11");
float f = Float.parseFloat("12.5");
```

<font color = "red">**注意：如果字符串参数的内容无法正确转换为对应的基本数据类型，则会抛出`java.lang.NumberFormaxException`异常**</font>



## 3. 日期时间

### 3.1 `Date`类

<font color = "blue">常用方法</font>

```java
public Date();	// 无参构造，表示计算机系统当前时间，精确到毫秒
public Date(long date);	// 带参构造，表示根据给定的时间数字来构建一个日期对象，精确到毫秒

public long getTime();	// 获取日期对象中的时间数字，精确到毫秒
public boolean before(Date when);	// 判单当前对象表示的日期是否在给定日期之前
public boolean after(Date when);	// 判断当前对象表示的日期是否在给定日期之后
```

<font color = "blue">示例</font>

```java
package com.lullaby.date;

import java.util.Date;

public class DateTest {

    public static void main(String[] args) {
        Date now = new Date();  // 获取计算机系统当前时间
        System.out.println(now);
        // 获取系统当前时间（数字形式）
        long time = System.currentTimeMillis();
        Date date = new Date(time);
        System.out.println(date);
        long dateTime = date.getTime(); // 获取日期对象中的数字时间
        System.out.println(time - dateTime);
        long yesterday = time - 24 * 60 * 60 * 1000;
        Date yesterdayDate = new Date(yesterday);   // 昨天
        boolean before = yesterdayDate.before(now); // 昨天是否在今天之前
        boolean after = yesterdayDate.after(now);   // 昨天是否在今天之后
        System.out.println(before);
        System.out.println(after);
    }
}
```

<font color = "blue">思考：根据熟悉的方式打印日期</font>

我们可以将日期转换为我们熟悉的字符串日期格式，然后再打印



### 3.2 `SimpleDateFormat`类

<font color = "blue">常用方法</font>

```java
public SimpleDateFormat(String pattern);	// 根据给定的日期格式化对象
public final String format(Date date);	// 将给定日期对象格式化
public Date parse(String source) throw ParseException;	// 将给定的字符串格式日期解析为日期对象
```

<font color = "blue">示例</font>

```java
package com.lullaby.date;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class DateUtil {

    public static final String format1 = "yyyy-MM-dd HH:mm:ss";
    public static final String format2 = "yyyy/MM/dd HH:mm:ss";
    public static final String format3 = "yyyy/MM/dd";
    public static final String format4 = "yyyy-MM-dd";

    public static String format(String pattern, Date date) {
        SimpleDateFormat format = new SimpleDateFormat(pattern);
        return format.format(date);
    }

    /**
     * 根据给定的日期格式，将日期对象转化为字符串格式的日期
     * @param pattern 日期格式
     * @param dateStr 日期对象
     * @return 日期
     * @throws ParseException
     */
    public static Date parse(String pattern, String dateStr) throws ParseException {
        SimpleDateFormat format = new SimpleDateFormat(pattern);
        return format.parse(dateStr);
    }
}
```

```java
package com.lullaby.date;

import java.text.ParseException;
import java.util.Date;

public class SimpleDateFormatTest {

    public static void main(String[] args) throws ParseException {
//        String format1 = "yyyy-MM-dd HH:mm:ss";
//        String format2 = "yyyy/MM/dd HH:mm:ss";
        Date date = new Date();
//        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
//        String dateStr = sdf.format(date);
//        System.out.println(dateStr);

        Date yesterday = new Date(System.currentTimeMillis() - 24 * 60 * 60 * 1000);
//        SimpleDateFormat dateFormat = new SimpleDateFormat(format2);
//        String yesterdayStr = dateFormat.format(yesterday);
//        System.out.println(yesterdayStr);

        System.out.println(DateUtil.format(DateUtil.format1, date));
        System.out.println(DateUtil.format(DateUtil.format2, yesterday));

        String s = "2020-10-10 10:10:10";
        Date date1 = DateUtil.parse(DateUtil.format1, s);
        System.out.println(date1);
    }
}
```



### 3.3 `Calendar`类

<font color = "blue">常用静态字段</font>

| 字段值       | 含义                                                         |
| ------------ | :----------------------------------------------------------- |
| YEAR         | 年                                                           |
| MONTH        | 月，<font color = "red">从0开始</font>                       |
| DAY_OF_MONTH | 月中第几天                                                   |
| HOUR         | 时（12小时制）                                               |
| HOUR_OF_DAY  | 时（24小时制）                                               |
| MINUTE       | 分                                                           |
| SECOND       | 秒                                                           |
| DAY_OF_WEEK  | 周中第几天,<font color = "red">一周的第一天是周日，因此周日是1</font> |

<font color = "blue">常用方法</font>

```java
public static Calendar getInstance();	// 获取日历对象
public final Date getTime();	// 获取日历表示日期对象
public final void setTime(Date date);	// 设置日历表示的日期对象

public int get(int field);	// 获取给定的字段的值
public void set(int field);	// 设置给定字段的值和更改数量滚动日历
public void roll(int field, int amount);	// 根据给定的字段和更改数量滚动日历
public int getActualMaximum(int field);	// 获取给定字段的实际最大数量
```

<font color = "blue">示例</font>

```java
package com.lullaby.Calendar;

import com.lullaby.date.DateUtil;

import java.util.Calendar;
import java.util.Date;

public class CalendarTest {

    public static void main(String[] args) {
        // 获取一个日历对象，默认是系统当前时间
        Calendar c = Calendar.getInstance();
        Date now = c.getTime(); // 获取日历中的当前日期
        System.out.println(now);
        long time = now.getTime() - 3 * 24 * 60 * 60 * 1000;
        c.setTime(new Date(time));  // 设置日历的日期
        System.out.println(c.getTime());

        int year = c.get(Calendar.YEAR);    // 获取日历中的年份
        System.out.println(year);
        int month = c.get(Calendar.MONTH) + 1;  // 获取日历中的月份，因为月份是从0开始，因此要+ 1
        System.out.println(month);
        int day = c.get(Calendar.DAY_OF_MONTH); // 获取月中的第几天
        System.out.println(day);
        int hour = c.get(Calendar.HOUR);
        int hour_24 = c.get(Calendar.HOUR_OF_DAY);
        System.out.println(hour);
        System.out.println(hour_24);
        int minute = c.get(Calendar.MINUTE);
        int second = c.get(Calendar.SECOND);
        System.out.println(minute);
        System.out.println(second);

        c.set(Calendar.YEAR, 1999); // 设置年份为1999
        c.set(Calendar.MONTH, 9 -1);    // 设置月份为9月
        c.set(Calendar.DAY_OF_MONTH, 10);   // 设置天数为该月第10天
        c.set(Calendar.HOUR_OF_DAY,18);
        c.set(Calendar.MINUTE, 30);
        c.set(Calendar.SECOND, 0);
        System.out.println(DateUtil.format(DateUtil.format1, c.getTime()));
        c.roll(Calendar.YEAR, 1);   // 年份+1
        String s1 = DateUtil.format(DateUtil.format1, c.getTime());
        System.out.println(s1);
        c.roll(Calendar.YEAR, -2);
        System.out.println(DateUtil.format(DateUtil.format1, c.getTime()));
        // 获取日历中月份最大天数
        int maxDays = c.getActualMaximum(Calendar.DAY_OF_MONTH);
        System.out.println(maxDays);
        c.set(Calendar.MONTH, 1);
        System.out.println(c.getActualMaximum(Calendar.DAY_OF_MONTH));
    }
}
```

<font color = "blue">综合练习</font>

日历制作

1. 上一月日期展示
   1. 求出本月第一天是周几，然后计算上一月显示天数
   2. 求出上一月月中天数的最大值
2. 本月日期展示
   1. 求出本月天数最大值

3. 下一月日期展示
   1. 上一月展示天数，本月展示天数

```java
package com.lullaby.Calendar;

public class DayInfo {

    private int number;

    private boolean currentDay;

    private boolean change;

    public DayInfo(int number, boolean currentDay) {
        this.number = number;
        this.currentDay = currentDay;
    }

    public void show() {
        if (currentDay) {   // 当前月黑色
            if (change) {
                System.out.println(number);
            } else {
                System.out.print(number + "\t");
            }
        } else {    // 其余月份红色
            if (change) {
                System.err.println(number);
            } else {
                System.err.print(number + "\t");
            }
        }
        try {
            Thread.sleep(30L);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }

    public void setChange(boolean change) {
        this.change = change;
    }

    public boolean isChange() {
        return change;
    }
}
```

```java
package com.lullaby.Calendar;

import java.util.ArrayList;
import java.util.Calendar;
import java.util.List;

public class MyCalendar {

    public static void main(String[] args) {
        int year = 2021;
        int month = 6;
        show(year, month);
    }

    private static void show(int year, int month) {
        int totalDays = 42; // 日历显示总天数
        Calendar calendar = Calendar.getInstance();
        calendar.set(Calendar.YEAR, year);
        calendar.set(Calendar.MONTH, month);
        calendar.set(Calendar.DAY_OF_MONTH, 1);
        // 获取设置的日历的月份第一天是周几
        int week = calendar.get(Calendar.DAY_OF_WEEK);
        System.out.println(week);
        // 获取本月最大天数
        int currentMonthMaxDays = calendar.getActualMaximum(Calendar.DAY_OF_MONTH);
        calendar.roll(Calendar.MONTH, -1);  // 表示上一月
        // 获取上一月最大天数
        int prevMonthMaxDays = calendar.getActualMaximum(Calendar.DAY_OF_MONTH);
        // 计算上一月显示天数
        int prevMonthDisplayDays = week == 1 ? 6 : week - 2;
        // 计算下月显示天数
        int nextMonthDisplayDays = totalDays - prevMonthDisplayDays - currentMonthMaxDays;
        List<DayInfo> days = new ArrayList<>();
        int prevMonthStartDay = prevMonthMaxDays - prevMonthDisplayDays + 1;
        for (int i = prevMonthStartDay; i <= prevMonthMaxDays; i++) {
            days.add(new DayInfo(i, false));
        }
        // 当前月所有天数全部展示
        for (int i = 1; i <= currentMonthMaxDays; i++) {
            days.add(new DayInfo(i, true));
        }
        // 下一月显示天数
        for (int i = 1; i <= nextMonthDisplayDays; i++) {
            days.add(new DayInfo(i, false));
        }
        System.out.println(year + "年" + (month + 1) + "月");
        System.out.println("一\t二\t三\t四\t五\t六\t日");
        for (int i = 0; i < days.size(); i++) {
            DayInfo info = days.get(i);
            boolean changeLine = (i % 7 == 6);
            info.setChange(changeLine);
            info.show();
        }
    }
}
```
