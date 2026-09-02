# `Lambda`表达式

## 1. `Lambda`表达式标准语法

<font color = "blue"> `Lambda`表达式标准语法</font>

```java
（参数类型1 变量1， 参数类型2 变量2，···参数类型n 变量n）-> {
    // 代码块
    [return 返回值;]
};
```

<font color = "blue"> 示例</font>

```java
package com.Sonnet.Lambda;

public interface Actor {

    void performance();
}

```

```java
package com.Sonnet.Lambda;

public class ActorTest {

    public static void main(String[] args) {

        // 匿名内部类
        Actor actor = new Actor() {
            @Override
            public void performance() {
                System.out.println("蔡徐坤");
            }
        };
        actor.performance();

        Actor a = () -> {
            System.out.println("波罗蜜");
        };
        a.performance();

        // Lambda表达式
        Actor actor1 = () -> {
            System.out.println("蔡徐坤");
        };
        actor1.performance();
    }
}

```

<font color = "red" > **`Lambda`表达式只能使用在只有一个接口方法的接口上，只有一个接口方法的接口称之为函数式接口(`Functional Interfance`)**</font>

<font color = "blue"> 练习</font>

定义一个接口将任意对象以字符串的形式展示出来，并在测试类中使用`Lambda`表达式完成测试

```java
package com.Sonnet.lambda;

public interface Printable {

    void print(Object o);
}

```

```java
package com.Sonnet.lambda;

public class PrintableTest {

    public static void main(String[] args) {

        Printable p = new Printable() {
            @Override
            public void print(Object o) {
                System.out.println(o);
            }
        };
        p.print(1);

        // 函数式编程思想
        Printable p1 = (Object o) -> {
            System.out.println(o);
        };
        p1.print(2);
    }
}

```

定义一个接口计算两个树的和，并在测试类中使用`Lambda`表达式完成测试

```java
package com.Sonnet.lambda;

public interface Add {

    int add(int a, int b);

}

```

```java
package com.Sonnet.lambda;

public class AddTest {

    public static void main(String[] args) {

//        Add add = new Add() {
//            @Override
//            public int add(int a, int b) {
//                return 0;
//            }
//        };

        Add add = (int a, int b) -> {
            return a + b;
        };

        System.out.println(add.add(1, 2));
    }
}

```



## 2. `Lambda`表达式省略规则

1. （）中所有的参数类型可以省略
2. 如果（）中有且仅有一个参数，那么（）可以省略
3. 如果{}中有且仅有一条语句，那么{}可以省略，这条语句后的分号也可以省略。如果这条语句是return语句，那么return关键字也可以省略。

<font color = "blue"> 示例</font>

编写一个接口，打印系统当前时间。并在测试类中使用`Lambda`表达式完成测试。

```java
package com.Sonnet.lambda;

public interface PrintTime {

    void printTime();
}

```

```java
package com.Sonnet.lambda;

public class PrintTimeTest {

    public static void main(String[] args) {

//        PrintTime time = new PrintTime() {
//            @Override
//            public void printTime() {
//                System.out.println(System.currentTimeMillis());
//            }
//        };

        PrintTime time1 = () -> System.out.println(System.currentTimeMillis());
        time1.printTime();
    }
}

```



编写一个接口，获取一个指定范围内的随机数。并在测试类中使用`Lambda`表达式完成测试。

```java
package com.Sonnet.lambda;

public interface RandomNumber {
    int getRandomNumber(int start, int end);
}

```

```java
package com.Sonnet.lambda;

import java.util.Random;

public class RandomNumberTest {

    public static void main(String[] args) {

        Random random = new Random();

//        RandomNumber number = new RandomNumber() {
//            @Override
//            public int getRandomNumber(int start, int end) {
//                return (int) (Math.random() * (end - start) + start);
//            }
//        };

        RandomNumber number = (start, end) -> (int) (Math.random() * (end - start) + start);
        System.out.println(number.getRandomNumber(10, 29));


        Random random1 = new Random();
        int result = random1.nextInt(10) + 20;

        RandomNumber r = (start, end) -> new Random().nextInt(start) + end - start;
        System.out.println(r.getRandomNumber(10, 29));
    }
}
package com.Sonnet.lambda;

import java.util.Random;

public class RandomNumberTest {

    public static void main(String[] args) {

        Random random = new Random();

//        RandomNumber number = new RandomNumber() {
//            @Override
//            public int getRandomNumber(int start, int end) {
//                return (int) (Math.random() * (end - start) + start);
//            }
//        };

        RandomNumber number = (start, end) -> (int) (Math.random() * (end - start) + start);
        System.out.println(number.getRandomNumber(10, 29));


        Random random1 = new Random();
        int result = random1.nextInt(10) + 20;

        RandomNumber r = (start, end) -> new Random().nextInt(start) + end - start;
        System.out.println(r.getRandomNumber(10, 29));
    }
}

```

