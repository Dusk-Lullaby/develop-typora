# 常见API



## 1. Math

 

方法：

```java
public static int abs(int a);	//获取参数绝对值

public static double ceil(double a);	//向上取整

public static double floor(double a);	//向下取整

public static int round(float f);	//四舍五入

public static int max(int a, int b);	//获取两个int值中的较大值

public static double pow(double a, double b);	//返回a的b次幂的值

public static double random();	//返回double的随机值，范围为[0.0, 1.0)

public static double sqrt(double a);	//返回a的平方根

public static double cqrt(double a);	//返回a的立方根
```

> 如何获取a的n次方根？
>
> 通过调用pow方法
>
> eg：
>
> 5次方根 ： Math.pow(15625, 1.0 / 6.0);



* 练习1：判断一个数是否为一个质数

  * ```java
    package com.sonnet.demo_1218;
    
    import java.util.Scanner;
    
    public class Example2 {
        public static void main(String[] args) {
    
            System.out.println(isPrime(new Scanner(System.in).nextInt()));
    
        }
    
        public static boolean isPrime(int a) {
            if (a == 0) return false;
            for (int i = 2; i * i <= a; i++) {
                if (a % i == 0) return false;
            }
            return true;
        }
    }
    
    ```

    

* 练习2： 自幂数，  一个n位自然数等于自身个数位上数字的n次幂之和，三位自幂数，也叫做水仙花数，统计一共有多少个水仙花数。

  * ```java
    package com.sonnet.demo_1218;
    
    import java.util.Scanner;
    
    public class Example3 {
        public static void main(String[] args) {
            Scanner sc = new Scanner(System.in);
    
            int a = sc.nextInt();
            System.out.println(getCount(a));
        }
    
        public static int getCount(int n) {
    
            int cnt = 0;
            for (double i = Math.pow(10, n - 1); i < Math.pow(10, n); i++) {
                int a = (int)i;
                double sum = 0;
                for (int j = 0; j < n; j++) {
                    int b = a % 10;
                    a = a / 10;
                    sum = sum + Math.pow(b, n);
                }
                if (sum == (int)i) cnt++;
            }
            return cnt;
        }
    }
    
    ```



## 2. System



方法：

```java
public static void exit(int status);	//终止当前运行的虚拟机
    
public static long currentTimeMillis();	//返回当前系统的时间的毫秒值形式
    
public static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length);	//数组拷贝，（数据资源组， 起始索引， 目的地数组， 起始索引， 拷贝个数）
```

> ```java
> package com.sonnet.demo_1219;
> 
> public class Example1 {
>     public static void main(String[] args) {
>         int[] arr1 = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
>         int[] arr2 = new int[10];
>         /*
>             拷贝数组：
>             把arr1的数组内容拷贝arr2中
>             参数1： 数据源，要拷贝的数据从哪个数组而来
>             参数2： 从数据源数组中的第几个索引开始拷贝
>             参数3： 目的地，我要把数据拷贝到哪个数组中
>             参数4： 目的地数组的索引
>             参数5： 拷贝的个数
>          */
>         System.arraycopy(arr1, 0, arr2, 0, 10);
>         for (int i = 0; i < arr2.length; i++) {
>             System.out.print(arr2[i] + " ");
>         }
>     }
> }
> 
> ```
>
> > ```tex
> > 1 2 3 4 5 6 7 8 9 10 
> > ```



练习：

arr2 ： 0 0 0 0  1 2 3 0 0 0 

```java
package com.sonnet.demo_1219;

public class Example2 {
    public static void main(String[] args) {
        int[] arr1 = {1, 2, 3, 4, 5, 6, 7, 8, 9 ,10};
        int[] arr2 = new int[10];
        System.arraycopy(arr1, 0, arr2, 4, 3);
        for (int i = 0; i < arr2.length; i++) {
            System.out.print(arr2[i] + " ");
        }
    }
}

```



> 注意： 
>
> 1. 使用currentTimeMillis时，时间原点是1970年1月1日0：0：0， 我国在东八区，有八小时时差。
> 2. 使用arraycopy时， 如果数据资源数组和目的地数组都是基本数据类型，那么两者的数据类型必须保持一致，否则会报错。
> 3. 在拷贝的时候需要考虑数组的长度， 如果超出范围会报错
> 4. 如果数据资源组和目的地数组都是引用数据类型，那么子类类型就可以赋值给父类类型



## 3. Runtime



Runtime表示当前虚拟机的运行环境



方法：

```java
public static Runtime getRuntime();	//当前系统的运行环境对象

public void exit(int status);	//停止虚拟机

public int availableProcessors();	//获得CPU的线程数

public long maxMemory();	//JVM能从系统中获取总内存的大小（单位byte）

public long totalMemory();	//JVM已经从系统中获取总内存的大小（单位byte）

public long freeMemory();	//JVM剩余内存大小（单位byte）

public Process exec(String command);	//运行cmd命令
```



```java
package com.sonnet.demo_1219;

import java.io.IOException;

public class Example3 {
    public static void main(String[] args) throws IOException {


        //1. 获取Runtime对象
        Runtime r1 = Runtime.getRuntime();
        Runtime r2 = Runtime.getRuntime();
        //两次获取的是同一个对象
        System.out.println(r1 == r2);

        //2. 停止虚拟机, 与System.exit()相同
//        Runtime.getRuntime().exit(0);

        //3. 获取cpu线程数
        System.out.println(Runtime.getRuntime().availableProcessors());

        //4. 总内存大小, 单位是byte
        System.out.println(Runtime.getRuntime().maxMemory() / 1024 / 1024);

        //5. 已经获取的总内存
        System.out.println(Runtime.getRuntime().totalMemory() / 1024 / 1024);

        //6. 剩余内存大小
        System.out.println(Runtime.getRuntime().freeMemory() / 1024 / 1024);

        //7. 运行cmd命令
        Runtime.getRuntime().exec("notepad");
    }
}

```

```tex
true
20
3934
246
243

```



## 4. Object



Object类是Java中的顶级父类。所有的类都直接或间接的继承于Object类。

Object类中的方法可以被所有子类访问，所以我们要学习Object类和其中的方法。



方法：

```java
public Object();	//空参构造

public String toString();	//返回对象的字符串表示形式

public boolean equals(Object obj);	//比较两个对象是否相等

protected Object clone(int a);	//对象克隆
```



> x 1package com.lullaby.user;2​3import java.io.IOException;4import java.net.Socket;5​6public class UserClient {7​8    private Socket client;9​10    public UserClient(String ip, int port) throws IOException {11        this.client = new Socket(ip, port);12    }13​14    public void sendMessage(Message<User> message) throws IOException {15        MessageUtil.sendMessage(client, message);16    }17​18    public String receiveMsg() throws IOException, ClassNotFoundException {19        Message<String> msg = MessageUtil.receiveMessage(client);20        return msg.getData();21    }22​23    public static void main(String[] args) {24        try {25            UserClient client = new UserClient("localhost", 8888);26            client.sendMessage(new Message<>("login", new User("admin", "123456")));27            String backMsg = client.receiveMsg();28            System.out.println(backMsg);29        } catch (IOException e) {30            throw new RuntimeException(e);31        } catch (ClassNotFoundException e) {32            throw new RuntimeException(e);33        }34    }35}java
>
> ```java
> package com.sonnet.demo_1219;
> 
> import java.util.StringJoiner;
> 
> /*
>     Cloneable
>     如果一个接口里面没有抽象方法
>     表示当前接口是一个标记接口
>     现在Cloneable表示一旦实现了，那么当前类就可以被克隆
>     如果没有被实现，当前类的对象就不能被克隆
>  */
> public class User implements Cloneable{
>     private String username;    //用户名
>     private int id; //游戏角色
>     private String password;    //密码
>     private String path;    //游戏图片
>     private int[] data; //游戏进度
> 
> 
>     public User() {
>     }
> 
>     public User(String username, int id, String password, String path, int[] data) {
>         this.username = username;
>         this.id = id;
>         this.password = password;
>         this.path = path;
>         this.data = data;
>     }
> 
>     /**
>      * 获取
>      * @return username
>      */
>     public String getUsername() {
>         return username;
>     }
> 
>     /**
>      * 设置
>      * @param username
>      */
>     public void setUsername(String username) {
>         this.username = username;
>     }
> 
>     /**
>      * 获取
>      * @return id
>      */
>     public int getId() {
>         return id;
>     }
> 
>     /**
>      * 设置
>      * @param id
>      */
>     public void setId(int id) {
>         this.id = id;
>     }
> 
>     /**
>      * 获取
>      * @return password
>      */
>     public String getPassword() {
>         return password;
>     }
> 
>     /**
>      * 设置
>      * @param password
>      */
>     public void setPassword(String password) {
>         this.password = password;
>     }
> 
>     /**
>      * 获取
>      * @return path
>      */
>     public String getPath() {
>         return path;
>     }
> 
>     /**
>      * 设置
>      * @param path
>      */
>     public void setPath(String path) {
>         this.path = path;
>     }
> 
>     /**
>      * 获取
>      * @return data
>      */
>     public int[] getData() {
>         return data;
>     }
> 
>     /**
>      * 设置
>      * @param data
>      */
>     public void setData(int[] data) {
>         this.data = data;
>     }
> 
>     public String toString() {
>         return "User{username = " + username + ", id = " + id + ", password = " + password + ", path = " + path + ", data = " + arrToString() + "}";
>     }
> 
> 
>     public String arrToString() {
>         StringJoiner sj = new StringJoiner("");
> 
>         for (int i = 0; i < data.length; i++) {
>             sj.add(data[i] + " ");
>         }
>         return sj.toString();
>     }
> 
>     @Override
>     protected Object clone() throws CloneNotSupportedException {
>         //调用父类中的Clone方法
>         //相当于Java帮我们克隆一个对象，并把克隆之后的对象返回出去
>         return super.clone();
>     }
> }
> 
> 
> 
> 
> package com.sonnet.demo_1219;
> 
> public class Example4 {
>     public static void main(String[] args) throws CloneNotSupportedException {
> 
>         //对象克隆，把A对象的属性完全拷贝给B对象，也叫对象拷贝，对象复制
> 
>         //1. 创建一个对象
>         int[] data = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 0};
>         User u1 = new User("zhangsan", 1, "123abc", "girl11", data);
> 
>         //2. 克隆对象
>         //方法在底层会帮我们创建一个对象，并把对象的数据拷贝进去
>         //书写细节：
>         //1. 重写Object中的clone方法
>         //2. 让javabean类实现Cloneable接口
>         //3. 创建原对象并调用clone就可以了
>         Object u2 = (User)u1.clone();
> 
>         //会调用toString方法
>         System.out.println(u1);
>         System.out.println();
>         System.out.println(u2);
>     }
> }
> 
> ```
>
> > ```tex
> > User{username = zhangsan, id = 1, password = 123abc, path = girl11, data = 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 0 }
> > 
> > User{username = zhangsan, id = 1, password = 123abc, path = girl11, data = 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 0 }
> > 
> > ```



> 浅克隆：不管对象内部的属性是基本数据类型还是引用数据类型，都完全拷贝过来
>
> 深克隆：基本数据类型拷贝过来，字符串复用，引用数据类型会重新创建新的

> 那么Object中的clone方法是浅克隆还是深克隆呢？
>
> 浅克隆，验证：
>
> ```java
> package com.sonnet.demo_1219;
> 
> public class Example4 {
>     public static void main(String[] args) throws CloneNotSupportedException {
> 
>         //对象克隆，把A对象的属性完全拷贝给B对象，也叫对象拷贝，对象复制
> 
>         //1. 创建一个对象
>         int[] data = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 0};
>         User u1 = new User("zhangsan", 1, "123abc", "girl11", data);
> 
>         //2. 克隆对象
>         //方法在底层会帮我们创建一个对象，并把对象的数据拷贝进去
>         //书写细节：
>         //1. 重写Object中的clone方法
>         //2. 让javabean类实现Cloneable接口
>         //3. 创建原对象并调用clone就可以了
>         Object u2 = (User)u1.clone();
> 
>         //会调用toString方法
>         System.out.println(u1);
>         System.out.println();
>         System.out.println(u2);
>         System.out.println();
> 
>         //验证Object中的clone是浅克隆
>         int[] arr = u1.getData();
>         arr[1] = 100;
> 
>         //如果u1中的值也一起改变，则为浅克隆
>         System.out.println(u1.arrToString());
>     }
> }
> 
> ```
>
> ```tex
> User{username = zhangsan, id = 1, password = 123abc, path = girl11, data = 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 0 }
> 
> User{username = zhangsan, id = 1, password = 123abc, path = girl11, data = 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 0 }
> 
> 1 100 3 4 5 6 7 8 9 10 11 12 13 14 15 0 
> 
> ```



> 实现深克隆
>
> ```java
> package com.sonnet.demo_1219;
> 
> import java.util.StringJoiner;
> 
> /*
>     Cloneable
>     如果一个接口里面没有抽象方法
>     表示当前接口是一个标记接口
>     现在Cloneable表示一旦实现了，那么当前类就可以被克隆
>     如果没有被实现，当前类的对象就不能被克隆
>  */
> public class User implements Cloneable{
>     private String username;    //用户名
>     private int id; //游戏角色
>     private String password;    //密码
>     private String path;    //游戏图片
>     private int[] data; //游戏进度
> 
> 
>     public User() {
>     }
> 
>     public User(String username, int id, String password, String path, int[] data) {
>         this.username = username;
>         this.id = id;
>         this.password = password;
>         this.path = path;
>         this.data = data;
>     }
> 
>     /**
>      * 获取
>      * @return username
>      */
>     public String getUsername() {
>         return username;
>     }
> 
>     /**
>      * 设置
>      * @param username
>      */
>     public void setUsername(String username) {
>         this.username = username;
>     }
> 
>     /**
>      * 获取
>      * @return id
>      */
>     public int getId() {
>         return id;
>     }
> 
>     /**
>      * 设置
>      * @param id
>      */
>     public void setId(int id) {
>         this.id = id;
>     }
> 
>     /**
>      * 获取
>      * @return password
>      */
>     public String getPassword() {
>         return password;
>     }
> 
>     /**
>      * 设置
>      * @param password
>      */
>     public void setPassword(String password) {
>         this.password = password;
>     }
> 
>     /**
>      * 获取
>      * @return path
>      */
>     public String getPath() {
>         return path;
>     }
> 
>     /**
>      * 设置
>      * @param path
>      */
>     public void setPath(String path) {
>         this.path = path;
>     }
> 
>     /**
>      * 获取
>      * @return data
>      */
>     public int[] getData() {
>         return data;
>     }
> 
>     /**
>      * 设置
>      * @param data
>      */
>     public void setData(int[] data) {
>         this.data = data;
>     }
> 
>     public String toString() {
>         return "User{username = " + username + ", id = " + id + ", password = " + password + ", path = " + path + ", data = " + arrToString() + "}";
>     }
> 
> 
>     public String arrToString() {
>         StringJoiner sj = new StringJoiner("");
> 
>         for (int i = 0; i < data.length; i++) {
>             sj.add(data[i] + " ");
>         }
>         return sj.toString();
>     }
> 
>     @Override
>     protected Object clone() throws CloneNotSupportedException {
>         //调用父类中的Clone方法
>         //相当于Java帮我们克隆一个对象，并把克隆之后的对象返回出去
> 
>         //深克隆：
>         //先把被克隆对象中的数组取出来
>         int[] data = this.data;
>         //创建新的数组
>         int[] newData = new int[data.length];
>         //拷贝数组中的数据
>         for (int i = 0; i < data.length; i++) {
>             newData[i] = data[i];
>         }
>         //调用父类中的方法克隆对象
>         User u = (User)super.clone();
>         //因为父类中的方法是浅克隆，替换克隆出来对象中的数组地址值
>         u.data = newData;
>         return u;
> 
>     }
> }
> 
> 
> 
> 
> package com.sonnet.demo_1219;
> 
> public class Example4 {
>     public static void main(String[] args) throws CloneNotSupportedException {
> 
>         //对象克隆，把A对象的属性完全拷贝给B对象，也叫对象拷贝，对象复制
> 
>         //1. 创建一个对象
>         int[] data = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 0};
>         User u1 = new User("zhangsan", 1, "123abc", "girl11", data);
> 
>         //2. 克隆对象
>         //方法在底层会帮我们创建一个对象，并把对象的数据拷贝进去
>         //书写细节：
>         //1. 重写Object中的clone方法
>         //2. 让javabean类实现Cloneable接口
>         //3. 创建原对象并调用clone就可以了
>         Object u2 = (User)u1.clone();
> 
>         //会调用toString方法
>         System.out.println(u1);
>         System.out.println(u2);
> 
>         //验证Object中的clone是浅克隆
>         int[] arr = u1.getData();
>         arr[1] = 100;
> 
>         //如果u1中的值也一起改变，则为浅克隆
>         System.out.println();
>         System.out.println(u1.arrToString());
>         System.out.println(((User)u2).arrToString());
>     }
> }
> 
> ```
>
> ````tex
> User{username = zhangsan, id = 1, password = 123abc, path = girl11, data = 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 0 }
> User{username = zhangsan, id = 1, password = 123abc, path = girl11, data = 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 0 }
> 
> 1 100 3 4 5 6 7 8 9 10 11 12 13 14 15 0 
> 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 0 
> 
> ````



## 5. Objects



Objects是一个工具类，提供了一些方法去完成一些功能。



方法：

```java
public static boolean equals(Object a, Object b);	//先做非空判断，比较两个对象

public static boolean isNull(Object obj);	///判断对象是否为null，为null返回true， 反之

public static boolean nonNull(Object obj);	//判断对象是否为null，根isNUll的结果相反
```



示例：

```java
package com.sonnet.demo_1219;

import java.util.Objects;

public class Example5 {

    public static void main(String[] args) {
        Student s1 = null;
        Student s2 = new Student(23, "zhangsan");

//        //NUllPointerException 空指针异常
//        try {
//            System.out.println(s1.equals(s2));
//        } catch(NullPointerException e) {
//            System.out.println("不能为空");
//        }
//
//        System.out.println("仍会执行");

        //细节：
        //1. 方法的底层会判断s1是否为null，如果为null，方法直接返回false
        //2. 如果s1不为null，那么就利用s1再次调用equals方法
        //3. 此时s1是Student类型，所以最终还是会再次调用Student中的equals方法
        //如果没有重写比较地址，如果重写比较属性
        System.out.println(Objects.equals(s1, s2));
        
        System.out.println(Objects.isNull(s1));

        System.out.println(Objects.nonNull(s1));
    }

}

```

```tex
false
true
false

```



## 6. BigInteger

> 大整数



在math包中

在java中，整数有4种类型：byte， short， int， long

在底层占用字节个数：byte1个字节、 short2个字节、 int4个字节、 long8个字节



方法：

```java
public BigInteger(int num, Random rnd);	//获取随机大整数，范围：[0~2d的num次方 - 1]

public Biginteger(String val);	//获取指定的大整数

public BigInteger(String val, int radix);	//获取指定进制的大整数

public static BigInteger valueOf(long val);	//静态方法获取BigInteger的对象，内部有优化
```

> 注意：对象一旦创建，内部记录的值不能发生改变



示例

```java
package com.sonnet.demo_1219;

import java.math.BigInteger;
import java.util.Random;

public class Example6 {
    public static void main(String[] args) {

        //1. 获取随机大整数
        BigInteger bg1 = new BigInteger(4, new Random());
        System.out.println(bg1);

        //2. 获取指定大整数
        //字符串中必须是整数，否则会报错
        BigInteger bg2 = new BigInteger("100");
        System.out.println(bg2);

        //3. 获取指定进制的大整数
        //字符串中的数字必须是整数
        //字符串中的数字必须和进制符合
        BigInteger bg3 = new BigInteger("100", 2);
        System.out.println(bg3);

        //4. 静态方法获取BigInteger的对象，内部有优化
        //1. 表示的范围比较小，在long的取值范围内
        //2. 在内部对常用的数字： -16 ~ 16进行了优化
        //  提前把-16 ~ 16 先创建好BigInteger的对象，如果多次获取不会重新创建
        BigInteger bg4 = BigInteger.valueOf(100);
        System.out.println(bg4);

        BigInteger bg5 = BigInteger.valueOf(8);
        BigInteger bg6 = BigInteger.valueOf(8);
        BigInteger bg7 = BigInteger.valueOf(82);
        BigInteger bg8 = BigInteger.valueOf(82);

        System.out.println(bg5 == bg6);
        System.out.println(bg7 == bg8);
    }
}

```

```tex
10
100
4
100
true
false

```



> BinInteger构造方法总结：
>
> 1. 如果BigInteger表示的数字**没有超出**long的范围，可以用静态方法获取。
> 2. 如果BigInteger表示的数字**超出**long的 范围，可以用构造方法获取。
> 3. 对象一旦创建， BigInteger内部记录的值不能发生改变。
> 4. 只要进行计算都会产生一个新的BigInteger对象。



方法：

```java
public BigInteger add(BigInteger val);	//加法

public BigInteger subtract(BigInteger val);	//减法

public BigInteger multiply(BigInteger val);	//乘法

public BigInteger divide(BigInteger val);	//除法， 获取商

public BigInteger[] divideAndRemainder(BigInteger val);	//除法， 获取商和余数

public boolean equals(Object x);	//比较是否相同

public BigInteger pow(int exponent);	//次幂

public BigInteger max/min(BigInteger val); //返回较大值/较小值

public int intValue();	//转为int类型整数，超出范围数据有误
```



示例：

```java
package com.sonnet.demo_1219;

import java.math.BigInteger;

public class Example7 {

    public static void main(String[] args) {

        //1. 创建两个对象
        BigInteger bg1 = BigInteger.valueOf(8);
        BigInteger bg2 = BigInteger.valueOf(2);

        //2. 加法
        System.out.println(bg1.add(bg2));

        //3. 除法获取商和余数
        BigInteger[] arr = bg1.divideAndRemainder(bg2);
        System.out.println(arr.length);
        System.out.println(arr[0]);
        System.out.println(arr[1]);

        //4. 比较是否相同
        BigInteger bg3 = BigInteger.valueOf(8);
        BigInteger bg4 = BigInteger.valueOf(8);
        System.out.println(bg3.equals(bg4));

        //5. 次幂
        System.out.println(bg1.pow(2));

        //6. max / min
        System.out.println(bg1.max(bg2));
        System.out.println(bg1.min(bg2));

        //7. 变成基本数据类型
        //  long double
        System.out.println(bg1.intValue());
        System.out.println(bg1.longValue());
        System.out.println(bg1.doubleValue());
    }
}

```

```tex
10
2
4
0
true
64
8
2
8
8
8.0

```



## 7. BigDecimal



位于java.math包下



方法：

```java
public BigDecimal(double val);	//通过传递double类型小数创建对象，不精确

public BigDecimal(String val);	//通过传递字符串表示的小数来创建对象，精确

public static Bigdecimal valueOf(double val / long val);	//通过静态方法获取对象 
```



示例：

```java
package com.sonnet.demo_1220;

import java.math.BigDecimal;

public class Example1 {
    public static void main(String[] args) {


        //1. 通过传递double类型的小数来创建对象
        //细节：这种方式可能不精确，不建议使用
        BigDecimal bd1 = new BigDecimal(0.01);
        BigDecimal bd2 = new BigDecimal(0.09);
        System.out.println(bd1);
        System.out.println(bd2);

        //2. 通过传递字符串表示的小数来创建对象
        //精确
        BigDecimal bg3 = new BigDecimal("0.06");
        BigDecimal bg4 = new BigDecimal("0.82");
        System.out.println(bg3);
        System.out.println(bg4);

        //3. 通过静态方法获取对象
        BigDecimal bg5 = BigDecimal.valueOf(0.82);
        System.out.println(bg5);


    }
}

```

```tex
0.01000000000000000020816681711721685132943093776702880859375
0.0899999999999999966693309261245303787291049957275390625
0.06
0.82
0.82
```



> 注意：
>
> 1. 如果表示的数字不大，没有超出double的表示范围，建议使用静态方法
>
> 2. 如果表示的数字比较大，超出了double的表示范围，建议使用构造方法
>
> 3. 如果我们传递的是 0 ~ 10之间的**整数**，包含0，包含10， 那么方法会返回已经创建好的对象，不会直接new
>
>    验证：
>
>    ```java
>    package com.sonnet.demo_1220;
>                      
>    import java.math.BigDecimal;
>                      
>    public class Example1 {
>        public static void main(String[] args) {
>                      
>            BigDecimal bd6 = BigDecimal.valueOf(8.2);
>            BigDecimal bd7 = BigDecimal.valueOf(8.2);
>            BigDecimal bd8 = BigDecimal.valueOf(82);
>            BigDecimal bd9 = BigDecimal.valueOf(82);
>            BigDecimal bd10 = BigDecimal.valueOf(8);
>            BigDecimal bd11 = BigDecimal.valueOf(8);
>    
>    
>            //BigDecimal已经重写了equals方法，比较数值和精度是否相等
>            System.out.println(bd6.equals(bd7));
>            System.out.println(bd8.equals(bd9));
>                            
>            System.out.println(bd6 == bd7);
>            System.out.println(bd8 == bd9);
>            System.out.println(bd11 == bd10);
>    
>    
>        }
>    }
>    
>    ```
>
>    > ```tex
>    > true
>    > true
>    > false
>    > false
>    > true
>    > 
>    > ```



常见方法：

```java
public static BigDecimal valueOf(double val);	//获取对象

public BigDecimal add(BigDecimal val);	//加法

public BigDecimal subtract(BigDecimal val);	//减法

public BigDecimal multiply(BigDecimal val);	//乘法

public BigDecimal divide(BigDecimal val);	//除法

public BigDecimal divide(BigDeciaml val, int scale, int roundingMode);	//除法，（BigDecimalval，精确几位， 舍入模式） 
```



示例：

```java
package com.sonnet.demo_1220;

import java.math.BigDecimal;
import java.math.RoundingMode;

public class Example2 {
    public static void main(String[] args) {

        BigDecimal bd1 = BigDecimal.valueOf(8.2);
        BigDecimal bd2 = BigDecimal.valueOf(8.2);

        //1. 加法
        System.out.println(bd1.add(bd2));

        //2. 减法
        System.out.println(bd1.multiply(bd2));

        //3. 除法
        System.out.println(bd1.divide(bd2));
        System.out.println(bd1.divide(bd2, 2, RoundingMode.UP));
    }
}

```

```tex
16.4
67.24
1
1.00

```

> 舍入模式：
>
> 1. UP： 远离0方向舍入的舍入模式
> 2. DOWN： 向0方向舍入的舍入方式
> 3. CEILNG： 向正无限大方向舍入的舍入模式
> 4. FLOOR： 向负无限大的方向舍入的舍入模式
> 5. HALF_UP: 四舍五入



## 8. 正则表达式



正则表达式可以校验字符串是否满足一定的规则，并用来校验数据格式的合法性。



作用：

1. 校验字符串是否满足规则
2. 在一段文本中查找满足要求的内容



需求：假如现在要求校验一个qq号码是否正确。

规则：6位以及20位之内，0不能再开头，必须全部是数字



```java
package com.sonnet.demo_1220;

public class Example3 {
    public static void main(String[] args) {
        String str1 = "123456789";
        String str2 = "abc123456";
        boolean flag1 = str1.matches("[1-9][0-9]{5,19}");
        boolean flag2 = str2.matches("[1-9][0-9]{5,19}");
        System.out.println(flag1);
        System.out.println(flag2);

    }
}

```

```tex
true
false

```



### 校验

```tex
字符类(只匹配一个字符)：
[abc]	//只能是a, b, c
[^abc]	//除了abc之外的任何字符
[a-zA-Z]	//包括a-z，A-Z
[a-d[m-p]]	//a-d, 或m-p
[a-z&&[def]]	//a-z和def的交集：d，e，f
[a-z&&[^bc]]	//a-z和非bc的交集，等同于[ad-z]
[a-z&&[^m-p]]	//a-z和除了m-p的交集，等同于[a-lq-z]

预定义字符(只匹配一个字符)：
.	//任何字符
\d	//一个数字[0-9]
\D	//非数字[^0-9]
\s	//一个空白字符[\t\n\x0B\f\r]
\S	//非空白字符[^\s]
\w	//英文，数字，下划线[a-zA-Z_0-9]
\W	//一个非单词字符[^\w]

数量词：
X？	//X，1次或0次
X*	//X，0次或多次
X+	//X，1次或多次
X{n}	//X, 正好n次
X{n,}	//X， 至少n次
X{n,m}	//X, 至少n次但不超过m次
```

> \表示转义字符



示例：

```java
package com.sonnet.demo_1220;

public class Example4 {
    public static void main(String[] args) {
        //  \\d表示任意一个字符
        //  \\d只能是任意一个字符
        //简单记法：两个\，表示一个\
        System.out.println("3".matches("[\\d]"));
    }
}

```



练习1：请编写正确的表达式去验证用户输入的手机号码是否满足需求

练习2： 请编写正确的表达式验证用户输入的座机号码是否满足要求

练习3： 请编写正确的表达式验证用户输入的邮箱号是否满足要求

```java
package com.sonnet.demo_1220;

public class Example5 {
    public static void main(String[] args) {
        String regex1 = "18820060802";
        System.out.println(regex1.matches("1[3-9]\\d{9}"));
        String regex2 = "0200-60802";
        System.out.println(regex2.matches("0\\d{2,3}-?[1-9]\\d{4,9}"));

        //邮箱：
        //思路：
        //第一部分：@的左边 \\w+
        //第二部分：@
        //第三部分：[\\w&&[^-]]{2,6}(\\.[a-zA-Z]{2,3}){1,2}
        String regex3 = "32333233@qq.com";
        System.out.println(regex3.matches("\\w+@[\\w&&[^-]]{2,6}(\\.[a-zA-Z]{2,3}){1,2}"));
    }
}

```

```tex
true
true
true
```



> 在实际开发中，很少会自己去写正则表达式
>
> 一般百度一个类似，在自己修改



### 爬虫



#### 爬虫

有如下文本，请按照要求爬取数据

Java自从95年问世以来，经历了很多版本，目前企业中用最多的是Java8和Java11，

因为这两个版本是长期支持版本，下一个长期支持版本是Java17，相信在未来不久Java17也会逐渐登上历史舞台

要求：找出里面所有的JavaXX

提示：Pattern：表示正则表达式

Matcher：文本匹配器，作用按照正则表达式的规则去读取字符串，从头开始读取。在大串中去找符合匹配规则的子串。



```java
package com.sonnet.demo_1220;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class Example6 {
    public static void main(String[] args) {

        String str = "Java自从95年问世以来，经历了很多版本，目前企业中用最多的是Java8和Java11，\n" +
                "\n" +
                "因为这两个版本是长期支持版本，下一个长期支持版本是Java17，相信在未来不久Java17也会逐渐登上历史舞台";

//        method1(str);

        //1. 获取正则表达式的对象
        Pattern p = Pattern.compile("Java\\d{0,2}");

        //2. 获取文本匹配器的对象
        //拿着m去读取str，找符合p规则的子串
        Matcher m = p.matcher(str);

        //3. 利用循环获取
        while (m.find()) {
            String s = m.group();
            System.out.println(s);
        }
    }

    private static void method1(String str) {
        //获取正则表达式的对象
        Pattern p = Pattern.compile("Java\\d{0,2}");

        //获取文本匹配器的对象
        //m: 文本匹配器的对象
        //str： 大串
        //p： 规则
        //m要在str中找符合p规则的小串
        Matcher m = p.matcher(str);

        //拿着文本匹配器从头开始读取，寻找是否有满足规则的子串
        //如果没有，方法返回false
        //如果有，返回true，在底层记录子串的起始索引和结束索引 + 1；
        boolean b = m.find();

        //方法底层会根据find方法记录的索引开始截取
        //subString(起始索引， 结束索引)；左闭右开
        String s1 = m.group();
        System.out.println(s1);
    }
}

```

```tex
Java
Java8
Java11
Java17
Java17

```



#### 带条件爬虫



带条件爬虫练习：

需求1：爬取版本号为8， 11， 17的Java文本，但只要Java，不要版本号
需求2：爬取版本号为8， 11， 17的Java文本，正确爬取结果：Java8， Java11， Java17
需求3：爬取除了版本号为8， 11， 17的Java文本

```java
package com.sonnet.demo_1221;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class Example1 {
    public static void main(String[] args) {

        //需求1：爬取版本号为8， 11， 17的Java文本，但只要Java，不要版本号
        //需求2：爬取版本号为8， 11， 17的Java文本，正确爬取结果：Java8， Java11， Java17
        //需求3：爬取除了版本号为8， 11， 17的Java文本

        String str = "Java自从95年问世以来，经历了很多版本，目前企业中用最多的是Java8和Java11，\n" +
                "\n" +
                "因为这两个版本是长期支持版本，下一个长期支持版本是Java17，相信在未来不久Java17也会逐渐登上历史舞台\n" +
                "\n";

        //1. 定义正则表达式
        //?i表示忽略大小写
        // 问号可以理解为前面的数据Java
        // =表示在Java后面要跟随的数据
        // 但是在获取的时候，只获取前半部分
        String regex = "((?i)Java)(?=8|11|17)";

        Pattern p = Pattern.compile(regex);
        Matcher m = p.matcher(str);
        while (m.find()) {
            String s = m.group();
            System.out.println(s);
        }


        String regex2 = "((?i)Java)(?:8|11|17)";
        Pattern p2 = Pattern.compile(regex2);
        Matcher m2 = p2.matcher(str);
        while (m2.find()) {
            String s2 = m2.group();
            System.out.println(s2);
        }


        String regex3 = "((?i)Java)(?!8|11|17)";
        Pattern p3 = Pattern.compile(regex3);
        Matcher m3 = p3.matcher(regex3);
        while (m3.find()) {
            String s3 = m3.group();
            System.out.println(s3);
        }
    }
}

```

```tex
Java
Java
Java
Java
Java8
Java11
Java17
Java17
Java
```



#### 贪婪和非贪婪



* 贪婪爬取：

​	在爬取的数据的时候尽可能的多爬取数据，默认是贪婪爬取



* 非贪婪爬取：

​	在爬取数据的时候尽可能的少爬取

​	在数量词 + 的后面加上？，那么就是非贪婪爬取



贪婪爬取和非贪婪爬取练习：

有如下文本，请按照要求爬取数据。

Java自从95年问世以来，abbbbbbbbbbbaaaaaaaaaaaaaaaaaaa

经历了很多版本，目前企业中用的最多的是Java8和Java11，因为这两个版本是长期支持版本，下一个长期支持版本是Java17，相信在不久的Java17也会逐渐登上历史的舞台

需求1：按照ab+的方式爬取ab，b尽可能多获取

需求2：按照ab+的方式爬取ab，b尽可能少获取

```java
package com.sonnet.demo_1221;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class Example2 {
    public static void main(String[] args) {

        String str = "Java自从95年问世以来，abbbbbbbbbbbaaaaaaaaaaaaaaaaaaa\n" +
                "\n" +
                "经历了很多版本，目前企业中用的最多的是Java8和Java11，因为这两个版本是长期支持版本，下一个长期支持版本是Java17，相信在不久的Java17也会逐渐登上历史的舞台";

        String regex1 = "ab+";
        Pattern p1 = Pattern.compile(regex1);
        String regex2 = "ab+?";
        Pattern p2 = Pattern.compile(regex2);
        Matcher m1 = p1.matcher(str);
        Matcher m2 = p2.matcher(str);
        while (m1.find()) {
            System.out.println(m1.group());
        }
        while (m2.find()) {
            System.out.println(m2.group());
        }
    }
}

```

```tex
abbbbbbbbbbb
ab

```



### 正则表达式方法



正则表达式在字符串方法中的使用：

```java 
public String[] matches(String regex);	//判断字符串是否满足正则表达式

public String replaceAll(String regex, String newStr);	//按照正则表达式进行替换

public String[] split(String regex);	//按照正则表达式的规则切割字符串   
```



示例：

```java
package com.sonnet.demo_1221;

public class Example3 {
    public static void main(String[] args) {

        String regex = "[a-z]+[0-9]+";
        String str = "落几页诗asdfghjkl123夹心饼干qwertyuiop345嫁衣";
        System.out.println(str.matches(regex));

        //方法在底层跟之前一样也会创建文本解析器的对象
        //然后从头开始去读取字符串中的内容，只要有满足的，那么就用第二个参数去替换
        System.out.println(str.replaceAll(regex, "喜欢"));

        String[] s = str.split(regex);
        for (int i = 0; i < s.length; i++) {
            System.out.println(s[i]);
        }
    }
}

```

```tex
false
落几页诗喜欢夹心饼干喜欢嫁衣
落几页诗
夹心饼干
嫁衣

```



### 分组



> 其实就是小括号



每组都有组号，也就是序号

规则1： 从1开始，连续不间断

规则2： 从左括号为基准，最左边的是第一组，其次为第二组，以此类推

​	eg：` (\\d+)(\\d+)(\\d+)`   



#### 捕获分组

捕获分组就是把这一组的数据捕获出来，再用一次



需求1：判断一个字符串的开始部分和结束部分是否一致？只考虑一个字符

需求2：判断一个字符串的开始部分和结束部分是否一致？可以有多个字符

需求3：判断一个字符串的开始部分和结束部分是否一致？开始部分内部每个字符也需要一致

```java
package com.sonnet.demo_1221;

public class Example4 {
    public static void main(String[] args) {

        // \\组号表示把这个组号的数据在用一次
        String regex1 = "(.).+\\1";
        System.out.println("a12311a".matches(regex1));
        System.out.println("a12311b".matches(regex1));
        System.out.println("a123111".matches(regex1));
        System.out.println("----------------");

        String regex2 = "(.+).+\\1";
        System.out.println("abc12345hjacbabc".matches(regex2));
        System.out.println("abc12345hjacb".matches(regex2));
        System.out.println("abc12345".matches(regex2));
        System.out.println("-------------------");

        // (.) 表示把首字母看成一组
        // \\2 把首字母拿出来再次利用
        // * 作用于\\2， 出现0次或多次
        String regex3 = "((.)\\2*).+\\1";
        System.out.println("aaa123aaa".matches(regex3));
        System.out.println("aaa123cada".matches(regex3));
        System.out.println("aaa123abc".matches(regex3));
    }
}

```

```tex
true
false
false
----------------
true
false
false
-------------------
true
true
false
```



如果后续还要继续使用本组的数据。

正则内部使用：\\\组号

正则外部使用：$组号



```tex
口吃替换 需求：
将字符串：我要学学编编编编程程程程程程程程程
替换为：我要学编程
```

```java
package com.sonnet.demo_1221;

public class Example5 {
    public static void main(String[] args) {

        String str = "我要学学编编编编程程程程程程程程程";

        System.out.println(str.replaceAll("(.)\\1+", "$1"));
    }
}

```

```tex
我要学编程
```



#### 非捕获分组

分组之后不需要再使用本组的数据，仅仅是把数据括起来



| 符号      | 含义                       | 举例              |
| --------- | -------------------------- | ----------------- |
| (?:正则)  | 获取所有                   | Java(?:8\|11\|17) |
| (?=正则)  | 获取前面部分               | Java(?=8\|11\|17) |
| (? !正则) | 获取不是指定内容的前面部分 | Java(?!8\|11\|17) |



```tex
举例：
身份证号的简易正则表达式
[1-9]//d{16}(?:|x|X)
```



> 非捕获分组不会占用组号



## 9. 时间相关类

1秒 = 1000毫秒



### JDK7以前时间相关类

| 类               | 说明       |
| ---------------- | ---------- |
| Date             | 时间       |
| SimpleDataFormat | 格式化时间 |
| Calendar         | 日历       |



#### Date

Date类是一个JDK写好的Javabean类，用来描述时间，精确到毫秒。

利用空参构造创建的对象，默认表示系统当前的时间。

利用有参构造创建的对象，表示指定的时候。



示例：

```java
package com.sonnet.demo_1221;

import java.util.Date;


public class Example6 {
    public static void main(String[] args) {

        //1. 创建一个时间
        Date d = new Date();
        System.out.println(d);

        //2. 表示指定的时间
        Date d2 = new Date(0L);
        System.out.println(d2);

        //3. setTime,修改时间
        d2.setTime(1000L);
        System.out.println(d2);

        //4. getTime, 获取时间
        System.out.println(d2.getTime());
    }
}

```

```tex
Sun Dec 21 16:08:26 CST 2025
Thu Jan 01 08:00:00 CST 1970
Thu Jan 01 08:00:01 CST 1970
1000

```



```tex
练习：
需求1：打印时间原点开始一年之后的时间
需求2：定义任意两个Date对象，比较一下哪个时间在前，哪个时间在后
```

```java
package com.sonnet.demo_1221;

import java.util.Date;

public class Example7 {
    public static void main(String[] args) {
        System.out.println(new Date(365L * 24 * 60 * 60 * 1000));

        Date d1 = new Date(82);
        Date d2 = new Date(27);
        if (d1.getTime() > d2.getTime()) {
            System.out.println(d1.getTime());
        } else System.out.println(d2.getTime());
    }
}

```

```tex
Fri Jan 01 08:00:00 CST 1971
82

```



#### SimpleDateFormat



* SimpleDateFormat作用：
  * 格式化：把时间变成我们喜欢的格式
  * 解析：把字符串表示的时间变成Date对象



| 构造方法                                  | 说明                                       |
| ----------------------------------------- | ------------------------------------------ |
| `public SimpleDateFormat()`               | 构造一个`SimpleDateFormat`， 使用默认格式  |
| `public SimpledateFormat(String pattern)` | 构造一个`SimpleDateFormat`, 使用指定的格式 |



| 常用方法                                | 说明                         |
| --------------------------------------- | ---------------------------- |
| `public final String format(Date date)` | 格式化（日期对象 -> 字符串） |
| `public Date parse(String source)`      | 解析（字符串 -> 日期对象）   |



示例：

```java
package com.sonnet.demo_1224;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class Example1 {
    public static void main(String[] args) throws ParseException {

        //1. 利用空参格式构造（默认）
        SimpleDateFormat sdf1 = new SimpleDateFormat();
        Date d1 = new Date();
        String str1 = sdf1.format(d1);
        System.out.println(str1);

        //2. 利用带参构造
        SimpleDateFormat sdf2 = new SimpleDateFormat("yyyy年MM月dd日：HH:mm:ss");
        System.out.println(sdf2.format(d1));

        //3. 定义字符表示时间
        String str3 = "2025-08-02 05:20:00";
        SimpleDateFormat sdf3 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date date = sdf3.parse(str3);
        System.out.println(date.getTime());
    }
}

```

```tex
2025/12/24 下午7:30
2025年12月24日：19:30:21
1754083200000
```



练习：

假设你的生日为：2000-11-11

请用字符串表示这个数据，并将其转换为：2000年11月11日

```java
package com.sonnet.demo_1224;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class Example2 {
    public static void main(String[] args) throws ParseException {

        String str = "2000-11-11";
        SimpleDateFormat sdf2 = new SimpleDateFormat("yyyy-MM-dd");
        Date date = sdf2.parse(str);
        //格式化
        SimpleDateFormat sdf3 = new SimpleDateFormat("yyyy年MM月dd日");
        String result = sdf3.format(date);
        System.out.println(result);
    }
}

```



#### Calendar

* Calendaer代表了系统当前的日历对象，可以单独修改、获取时间中的年，月，日
* 细节：Calendar是一个抽象类，不能直接创建对象



获取Calendar日历类对象的方法：

`public static Calendar getInstance()`	获取当前时间的日历对象



Calendar的常用方法：

| 方法名                                     | 说明                        |
| ------------------------------------------ | --------------------------- |
| `public final Date getTime()`              | 获取日期对象                |
| `public final setTime(Date date)`          | 给日历设置日期对象          |
| `public long getTimeInMillis()`            | 拿到时间毫秒值              |
| `public void setTimeInMillis(long millis)` | 给日历设置时间毫秒值        |
| `public int get(int field)`                | 取日历中某个字段信息        |
| `public void set(int field, int value)`    | 修改日历的某个字段信息      |
| `public void add(int field, int amount)`   | 为某个字段增加/减少指定的值 |

