# 多态和Object类

## 多态

### 1. 概念

继承、接口就是多态的具体体现方式



### 2. 编译时多态

方法重载在编译时就已经确定如何调用，因此方法重载属于编译时多态。

> 类似于方法重载

示例：

```java
public class Calculator {

    public double calculator(double a, double b) {
        return a + b;
    }

    public int calculator(int a, int b) {
        return a + b;
    }

    public float calculator(float a, float b) {
        return a + b;
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Calculator c = new Calculator();
        int result1 = c.calculator(1, 2);
        double result2 = c.calculator(1.0, 2.0);
    }
}

```



### 3. 运行时多态

运行时多态：

Java虚拟机为每个变量中引用的对象调用适当的方法。

它不会调用由变量类型定义的方法。

这种行为成为虚拟方法的调用。



示例：

```java
public class father {

    public void show() {
        System.out.println("father show");
    }
}
```

```java
public class child extends father{

    @Override
    public void show() {
        System.out.println("child show");
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        //变量f的类型是father-----它不会调用由变量类型定义的方法。
        father f = new child();
        //f 调用show方法时， 不会调用father定义的方法
        f.show();
    }
}
```



案例：

王者中英雄都有名字，都会攻击，分为物理攻击和法术攻击

```java
public abstract class Hero {

     String name;

    public Hero(String name) {
        this.name = name;
    }

    //抽象类
    public abstract  void attack();
}

```

```java
public class PhysicalHero extends Hero{

    public PhysicalHero(String name) {
        super(name);
    }

    @Override
    public void attack() {
        System.out.println("物理攻击");
    }
}

```

```java
public class SpellHero extends Hero{
    @Override
    public void attack() {
        System.out.println("法术攻击");
    }

    public SpellHero(String name) {
        super(name);
    }
}

```

```java
public class HeroTest {

    public static void main(String[] args) {
        Hero hero1 = new PhysicalHero("李白");
        hero1.attack();
        Hero hero2 = new SpellHero("小乔");
        hero2.attack();
    }
}

```

```
输出结果：

物理攻击
法术攻击
```



应用场景：

动物园中有老虎，熊猫，猴子等动物，每种动物吃的东西不一样，老虎吃肉，熊猫吃竹叶，猴子吃水果，动物管理员会给它们喂食物



分析：老虎，熊猫，猴子都是动物，它们的动作都是吃，动物管理员喂食

```java
public class Animals {
    String name;

    public void eat() {
        System.out.println("chi");
    }
}

```

```java
public class Monkey extends Animals{

    public Monkey() {
        super();
    }

    @Override
    public void eat() {
        System.out.println("猴子吃水果");
    }
}

```

```java
public class Panda extends Animals{

    public Panda() {
        super();
    }

    @Override
    public void eat() {
        System.out.println("熊猫吃竹叶");
    }
}

```

```java
public class Tiger extends Animals {

    public Tiger () {
        super();
    }

    @Override
    public void eat() {
        System.out.println("老虎吃肉");
    }
}

```

```java
public class Zookeeper {

    public void feetTiger(Tiger tiger) {
        tiger.eat();
    }

    public void feetPanda(Panda panda) {
        panda.eat();
    }

    public void feetMonkey(Monkey monkey) {
        monkey.eat();
    }
}

```

```java
public class ZooTest {

    public static void main(String[] args) {
        Zookeeper zk = new Zookeeper();

        zk.feetMonkey(new Monkey());
        zk.feetPanda(new Panda());
        zk.feetTiger(new Tiger());
    }
}

```



> 现在每一种动物对应一种喂食方法，当拥有大量动物，动物管理员就需要添加大量方法，这种设计方法，存在缺陷，可以通过多态来进行改进

```java
public class Zookeeper {

//    public void feetTiger(Tiger tiger) {
//        tiger.eat();
//    }
//
//    public void feetPanda(Panda panda) {
//        panda.eat();
//    }
//
//    public void feetMonkey(Monkey monkey) {
//        monkey.eat();
//    }

    public void feetAnimals(Animals animals) {
        animals.eat();
    }
}

```

```java
public class ZooTest {

    public static void main(String[] args) {
        Zookeeper zk = new Zookeeper();

        //利用多态
        zk.feetAnimals(new Monkey());
        zk.feetAnimals(new Panda());
        zk.feetAnimals(new Tiger());
    }
}

```



> 细节：在调用方法时，采取的是编译看左边，运行看右边原则
>
> 在编译时：编译器先检查引用类型（Animals）有没有这个方法，来决定是否调用
>
> 如果编译时检测出Animals没有这个方法会直接报错
>
> 在运行时：JVM执行时调用实际对象类型（new monkey)的方法
>
> 
>
> 注意：成员变量没有多态，静态方法没有多态，私有方法没有多态

示例：

```java
public class Animals {
    String name;

    public void eat() {
        System.out.println("chi");
    }
}

```

```java
public class Lion extends Animals{

    @Override
    public void eat() {
        System.out.println("狮子在吃饭");
    }

    //子类特有的方法
    public void strolling() {
        System.out.println("狮子在漫步");
    }
}

```

```java
public class ZooTest {

    public static void main(String[] args) {
        Animals a = new Lion();
        
        /*
            调用方法时，编译看左边，运行看右边
            因为Animals有eat方法，所有a调用eat方法可以通过
            但Animals没有strolling方法所以，strolling无法通过编译
         */
        a.eat();
        //a.strolling();
    }
}

```



> 那么如果一定想要调用strolling方法怎么办？可以通过强制类型转换实现

```java
public class ZooTest {

    public static void main(String[] args) {
        Animals a = new Lion();

        /*
            调用方法时，编译看左边，运行看右边
            因为Animals有eat方法，所有a调用eat方法可以通过
            但Animals没有strolling方法所以，strolling无法通过编译
         */
        a.eat();
        //a.strolling();


        //如果想要实现strolling方法？强制类型转换
        ((Lion)a).strolling();
    }
}

```



> 需求：如果现在要在吃的同时，让狮子漫步，则代码实现如

```java
public class Zookeeper {

    public void keep(Animals animals) {
        animals.eat();
        //强制类型转载
        ((Lion)animals).strolling();
    }
}

```

```java
public class ZooTest {

    public static void main(String[] args) {
        Zookeeper zk = new Zookeeper();
        Animals a1 = new Lion();
        Animals a2 = new Tiger();

        zk.keep(a1);
        zk.keep(a2);
        
    }
}
```

> 发现这段代码报错了，为什么呢？
>
> 原因是，将Tiger赋值给了a2，而strolling是狮子的特有方法
>
> 只能将Animals强转，无法将Tiger强制类型转换成Lion
>
> 对于这个现象，我们可以通过instanceof运算符来进行优化



### 4.instanceof运算符

instanceof本身意思表示的是什么什么的一个实例。主要应用在类型的强制转换上，在使用强制类型转换时，如果使用不正确，在运行时会报错。而instanceof运算符对转换的目标进行检测，如果是，则进行强制转换。这样可以保证程序的正常运行。



语法：

```java
对象名 instanceof 类名： //表示检测对象是否是指定类型的一个实例，返回值类型为boolean类型
```



> 通过instanceof运算符对之前代码的优化:

```java
public class Zookeeper {

    public void keep(Animals animals) {
        animals.eat();
//        ((Lion)animals).strolling();

        //如果animals对象是一个Tiger类的实例对象
        if (animals instanceof Lion) {
            ((Lion) animals).strolling();
        } else if (animals instanceof Tiger) {
            System.out.println("老虎不能漫步");
        }
    }
}


```

```java
public class ZooTest {

    public static void main(String[] args) {
        Zookeeper zk = new Zookeeper();
        Animals a1 = new Lion();
        Animals a2 = new Tiger();

        zk.keep(a1);
        zk.keep(a2);

    }
}

```

```
输出：

狮子在吃饭
狮子在漫步
老虎吃肉
老虎不能漫步
```



练习：

某商城有电视机、电风扇、空调等电器设备展示。现有质检人员对这些电器设备一一检测，如果是电视机，就播放视频检测，如果是电风扇就启动风扇，如果是空调就进行制冷操作

> 分析：
>
> 电器：电视机，电风扇，空调
>
> 质检人员调用方法时，通过不同的对象使用对应的方法

```java
public class ElectricalAppliances {

}

```

```java
public class AirConditioner extends ElectricalAppliances{

    public void turnAir() {
        System.out.println("开启空调");
    }
}

```

```java
public class Fan extends ElectricalAppliances{

    public void turnFan() {
        System.out.println("开启风扇");
    }
}

```

```java
public class TV extends ElectricalAppliances{
    public void playVideo() {
        System.out.println("电视播放视频");
    }
}

```

```java
public class Person {

    public void check(ElectricalAppliances ea) {
        if (ea instanceof AirConditioner) {
            ((AirConditioner)ea).turnAir();
        } else if (ea instanceof Fan) {
            ((Fan)ea).turnFan();
        } else if (ea instanceof TV) {
            ((TV)ea).playVideo();
        }
    }
}

```

```java
public class Test {
    public static void main(String[] args) {
        Person p = new Person();
        p.check(new AirConditioner());
        p.check(new Fan());
        p.check(new TV());
    }
}

```

```java
输出：

开启空调
开启风扇
电视播放视频

```



> `instanceof`运算符的作用就算检测某个对象是否是某个类型的一个实例。主要用来进行下向转型（强制类型转换），它能够确保向下转型的正确使用。`insatcneof`运算符只能用在继承关系的类别中



## Object类常用方法

Object类中定义的方法大多数是属于native方法，native表示的是本地方法，实现是在C++中



###  1. `getClass()`

`getClass()` 是Java中 `Object` 类的一个核心方法，用于获取对象的运行时类信息



示例：

``` java
class TV {}


public class ObjectTest {

    public static void main(String[] args) {
        TV tv = new TV();
        //获取TV的信息
        Class clazz = tv.getClass();
        //获取类名
        String name = clazz.getSimpleName();
        System.out.println(name);
        //获取类的全限定名(包名 + 类名)
        String className = clazz.getName();
        System.out.println(className);
        //获取父类的定义信息
        Class superClass = clazz.getSuperclass();
        //获取父类的名称
        String superName = clazz.getSuperclass().getSimpleName();
        String superName2 = superClass.getSimpleName();
        System.out.println(superName);
        System.out.println(superName2);
        //获取父类的全限定名
        String superClassName = clazz.getSuperclass().getName();
        String superClassName2 = superClass.getName();
        System.out.println(superClassName);
        System.out.println(superClassName2);

        //String类
        String s = "admin";
        //获取String类的信息
        Class StringClass = s.getClass();
        //获取String类的接口，因为接口有多个所以需要数组
        Class[] interfaceClasses = StringClass.getInterfaces();
        //打印
        for (int i = 0; i < interfaceClasses.length; i++) {
            //获取接口信息
            Class interfaceClass = interfaceClasses[i];
            //获取接口名字
            String interfaceName = interfaceClass.getSimpleName();
            System.out.println(interfaceName);
            //获取接口全限定名
            String interfaceClassName = interfaceClass.getName();
            System.out.println(interfaceClassName);
        }
    }
}

```



### 2. `hashCode()`

返回值是对象的哈希码，即对象的内存地址（十六进制）

如果两个对象相等，则他们的哈希码也必须相等。

如果重写equals（）方法，则会更改两个对象的相等方式，并且object的hashCode（）实现不在有效。因此如果重写equals（）方法，则必须重写hashCode（）方法。



1. **Object类中的hashCode（）方法返回的就是对象的内存地址。一旦重写了hashCode（）方法，那么Object类中的hashCode（）方法就失效了，此时hashCode（）方法返回值不在是内存地址。**

2. **根据定义，如果两个对象相等，则他们的哈希码也必须相等，反之则不然**。

3. **两个对象使用equals（）方法比较，如果返回true，则它们的哈希码一定相同。**

4. **重写了equals（）方法，就必须要重写hashCode（）方法**



示例：

```java
public class Student {

    private String name;
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    //hashCode()方法被重写了之后，返回的值就不是内存地址了
    @Override
    public int hashCode() {
        return 1;
    }
}



public class StudentTest {

    public static void main(String[] args) {
        Student s1 = new Student("zhangsan", 1);
        Student s2 = new Student("zhangsan", 1);

    }
}

```

> 因为是new，所以s1和s2存储的地址并不一样，可一旦重写了hashCode（）方法，得到的值就一样了



### 3. `equals(object obj)`

equals()方法比较两个对象是否相等，如果相等返回true。object类中提供的equals（）方法使用身份运算符（==）来确定两个对象是否相等。对于原始数据类型，这将给出正确的结果，但是对于对象，则不是，地址值并不相同

如果要测试两个对象在等效性上是否相等（包含的信息相等），必须重写equals（）方法



两个对象的地址不同，内容相同，equals返回false

```java
public class StudentTest {

    public static void main(String[] args) {
        Student s1 = new Student("zhangsan", 1);
        Student s2 = new Student("zhangsan", 1);

        System.out.println(s1.equals(s2));
    }
}


输出结果：
    false
```



为了让输出结果为true，即比较的是对象的内容，而并不是地址值，就要重写equals方法

```java
public class Student {

    private String name;
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }


    @Override
    public boolean equals(Object obj) {
        //地址相等返回true
        if (this == obj) return true;
        //比较类的定义，类型不相等返回false
        if (this.getClass() != obj.getClass()) return false;
        //类的定义一致，那么对象obj就可以被强制转换为Student
        Student other = (Student)obj;
        return this.name.equals(other.name) && this.age == other.age;
    }
}



public class StudentTest {

    public static void main(String[] args) {
        Student s1 = new Student("zhangsan", 1);
        Student s2 = new Student("zhangsan", 1);

        System.out.println(s1.equals(s2));
    }
}



输出结果：
    true
```



> 但是，此时此刻两个对象的哈希码并不相同，
>
> 根据定义，如果两个对象相等，它们的哈希码必须相等
>
> 因此我们需要重写hashCode（）方法

```java
    @Override
    public int hashCode() {
        //调用String类的hashCode
        return name.hashCode() + age;
    }

```



> ```java
> 重写equals方法步骤：
> 1. 比较内存地址
> 2. 检测是否同一类型
> 3. 检测属性是否相同
> ```



> ```java
> 如果两个对象相等，那他们的哈希码就一定相等
> 如果重写了equals方法，那么就一定要重写hashCode方法
> 因为不重写hashCode方法，就会调用Object类中的hashCode方法
> 这样得到的是内存地址值，而不同对象的内存地址是不同的
> equals方法重写后，得到的不是地址，而是内部信息
> 这样，就会造成不同对象相等，但却拥有不同的哈希码
>     
> 所以重写了equals方法，就必须要重写hashCode方法
> ```



面试题：请描述==和equals方法的区别

基本数据类型使用==比较的就是两个数据的字面量，引用数据类型比较的是内粗地址。

equals（）方法，来自Object类，本身使用实现的就是==，此时没有区别。

但是Object类中的equals方法可能被重写，此时比较就需要重写逻辑来进行。



### 4. `toString()`

应该始终考虑在类中重写toString方法

重写了Object的toString（）方法返回该对象的String表达方式，这对调试非常有用。

> Object类中的toString()方法，最后输出的就是所属类的全限定名 + @ + 哈希码
>
> 我们可以通过重写toString() 方法，使其输出该类的内容



示例：

不重写toString（）

```java
public class StudentTest {

    public static void main(String[] args) {
        Student s1 = new Student("zhangsan", 1);
        Student s2 = new Student("zhangsan", 1);

        System.out.println(s1.toString());
        System.out.println(s2.toString());
    }
}


/*
	输出：
	hashCode.Student@4eec7777
	hashCode.Student@3b07d329
*/
```



重写toString() 方法，使其返回该类的内容

```java
public class Student {

    private String name;
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return name + "/t" + age;
    }
}



public class StudentTest {

    public static void main(String[] args) {
        Student s1 = new Student("zhangsan", 1);
        Student s2 = new Student("zhangsan", 1);

        //会自动调用s的toString方法
       	System.out.println(s1);
        System.out.println(s1.toString());
        System.out.println(s2);
        System.out.println(s2.toString());
    }
}

```

> 其输出结果为：
>
> zhangsan/t1
> zhangsan/t1
>
> zhangsan/t1
> zhangsan/t1



### 5. `finalize()`

Object类提供了一个回调方法finalize( ) ，当该对象变为垃圾时可以在该对象上调用方法。Object类的finalize( )实现不执行任何操作，你可以覆盖finalize( )，进行清理，例如释放资源



示例：

```java
public class Student {

    private String name;
    private int age;

    //当一个Student对象变为垃圾时，可能会被调用
    @Override
    protected void finalize() throws Throwable {
        //变为垃圾
        this.name = null;
        System.out.println("所有资源已经释放，可以进行清理");
    }

        public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

}



public class StudentTest {

    public static void main(String[] args) {
        show();
        System.out.println("这是最后一行代码了");
    }

    public static void show() {
        //s对象的作用范围，只是在show方法中，一旦方法执行完毕，
        //那么s对象就应该消亡，释放内存
        Student s = new Student("lisi", 24);
        System.out.println(s);
    }
}
```

> 输出：
>
> hashCode.Student@4eec7777
> 这是最后一行代码了



> 为什么没有执行finalize？
>
> 程序运行太快，s还没有被回收，程序就运行结束了

验证：

```java
public class StudentTest {

    public static void main(String[] args) {
        show();
        //调用系统的垃圾回收器进行垃圾回收
        //gc就是garbage collector
        System.gc();
        System.out.println("这是最后一行代码了");
    }

    public static void show() {
        //s对象的作用范围，只是在show方法中，一旦方法执行完毕，
        //那么s对象就应该消亡，释放内存
        Student s = new Student("lisi", 24);
        System.out.println(s);
    }
}
```

> 输出：
>
> hashCode.Student@4eec7777
> 这是最后一行代码了
> 所有资源已经释放，可以进行清理



> 注意：
>
> 只有堆上的内存才可能产生垃圾
>
> 当栈上没有引用时，就会变成垃圾
>
> 垃圾回收器，并不是时时回收，垃圾可能还会存在一段时间
