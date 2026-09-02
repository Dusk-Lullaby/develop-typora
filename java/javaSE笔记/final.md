# final

final 修饰方法：

​	表明该方法是最终方法，不能被重写

final 修饰类：

​	表明改类是最终类，不能被继承

final 修饰变量：

​	叫做常量，只能被赋值一次

​	

​	**常量记录的数据是不能发生改变的，**

​	**基本数据类型记录的是数据，**

​	**引用型数据类型记录的是地址**

```java
final int a = 10;
System.out.println(a);
```

​		~~a  = 20;~~  	因为是常量，不能被再次赋值



## 常量

在实际开发中，常量一般作为系统的配置信息，方便维护，提高可读性



**命名规范：**

* 单个单词：全部大写
* 多个单词：全部大写，单词之间用下划线间隔



## 细节

* final 修饰的变量是基本类型：那么变量存储的**数据值**不能发生改变

* final 修饰的变量是引用类型：那么变量存储的**地址值**不能发生改变，对象内部的属性值还可以发生改变

  ```java
  public class Students {
      private String name;
      private int age;
  
  
      public Students() {
      }
  
      public Students(String name, int age) {
          this.name = name;
          this.age = age;
      }
  
      /**
       * 获取
       * @return name
       */
      public String getName() {
          return name;
      }
  
      /**
       * 设置
       * @param name
       */
      public void setName(String name) {
          this.name = name;
      }
  
      /**
       * 获取
       * @return age
       */
      public int getAge() {
          return age;
      }
  
      /**
       * 设置
       * @param age
       */
      public void setAge(int age) {
          this.age = age;
      }
  
      public String toString() {
          return "Students{name = " + name + ", age = " + age + "}";
      }
  }
  
  ```

  ```java
      public static void main(String[] args) {
  
          final double PI = 3.14;
  
          final Students s = new Students("zhangsan", 23);
          
          //s = new Students("lisi", 82);	此写法错误	
          
          System.out.println(s.getName());	//zhangsan
          System.out.println(s.getAge());		//23
  
          s.setName("lisi");
          s.setAge(82);
          System.out.println(s.getName());	//lisi
          System.out.println(s.getAge());		//82
      }
  
  //虽然Students被加了final，但因为是引用类型，不会改变的地址值
  //因此对其内部的属性值进行改变依旧有效
  ```
  
  
  
  在以数组为例子，即使加了final，依旧可以改变其数组中的内容
  
  ```java
          final int[] arr = {1, 2, 3, 4, 5};
          System.out.println(arr[0]);		//1
          System.out.println(arr[1]);		//2
   
  		//arr = new int[5]; 	//报错
  
          arr[0] = 10;
          arr[1] = 20;
          System.out.println(arr[0]);		//10
          System.out.println(arr[1]);		//20
  
  ```



 **因此常量记录的数据不能发生改变**

​	**基本数据类型记录的是数据**

​	**引用数据类型记录的是地址**

