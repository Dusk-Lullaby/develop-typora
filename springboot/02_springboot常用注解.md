# 02_springboot常用注解（具体见PDF）

## 1. 组件添加的注解

### 1.1 @configuration注解

以前，如果我们想将bean交给spring容器管理，我们会怎么办? 我们可以使用xml或者注解的方式进配置。在springboot中还有一种非常常见的方式管理bean。那就是使用配置类的方式管理bean。

*   定义两个pojo类

    ```java
    public class User {
        private String username;
        private Integer age;
        public String getUsername() {
            return username;
        }
        public void setUsername(String username) {
            this.username = username;
        }
        public Integer getAge() {
            return age;
        }
        public void setAge(Integer age) {
            this.age = age;
        }
        @Override
        public String toString() {
            return "User{" +
                "username='" + username + '\'' +
                ", age=" + age +
                '}';
        }
    }
    ```

    ```java
    public class Cat {
        private String name;
        private String color;
        public String getName() {
            return name;
        }
        public void setName(String name) {
            this.name = name;
        }
        public String getColor() {
            return color;
        }
        public void setColor(String color) {
            this.color = color;
        }
        @Override
        public String toString() {
            return "Cat{" +
                "name='" + name + '\'' +
                ", color='" + color + '\'' +
                '}';
        }
    }
    ```

*   使用java配置类来管理

    ```java
    @Configuration
    public class MyConfig {
        @Bean
        public User user(){
            User user = new User();
            user.setUsername("eric");
            user.setAge(18);
            return user;
        }
        @Bean
        public Cat cat(){
            Cat cat = new Cat();
            cat.setName("大橘");
            cat.setColor("橘色");
            return cat;
        }
    }
    ```

步骤：

1.   定义一个类，使用@Configuration注解修饰这个类

     @Configuration：告诉springboot，这个类就是一个配置类，等同于spring项目中的xml配置文件

     @Bean：向ioc容器添加组件。它修饰一个方法，方法的返回值类型就是添加到容器中的组件的数据类型，作用等同于bean标签

2.   配置类本身也被交给了IOC容器管理。因为@Configuration内部又集成了@Component注解

*   允许启动器

    ```java
    @SpringBootApplication
    public class App {
        public static void main(String[] args) {
            //获得ioc容器
            ConfigurableApplicationContext context =
                SpringApplication.run(App.class, args);
            //获取所有bean的名称的集合
            String[] beanDefinitionNames =
                context.getBeanDefinitionNames();
            //循环获取bean的名称
            for(String name : beanDefinitionNames){
                System.out.println(name);
            }
        }
    }
    ```

总结：（具体见[pdf](D:\Typora\develop\springboot\pdf\Springboot2核心技术.pdf)）

proxyBeanMethods：代理bean的方法

*   Full(proxyBeanMethods = true)、【保证每个@Bean方法被调用多少次返回的组件都是单实例的】
*   Lite(proxyBeanMethods = false)【每个@Bean方法被调用多少次返回的组件都是新创建的】
*   组件依赖必须使用Full模式默认。其他默认是否Lite模式

### 1.2 @Import注解

@Import 向容器中导入一个组件

1.   导入普通组件

     ```java
     @Configuration(proxyBeanMethods = true) // 标识当前类是一个配置类 它的作用类似于spring中的xml配置文件
     @Import(value = {Animal.class}) // 向容器中导入一个普通类的组件 bean的名称默认是类的权限定名
     public class MyConfig {
     }
     ```

     向容器中导入Animal类型的组件，组件的名称就是Animal的权限定名

2.   导入@Configuration注释修饰的类

3.   ImportSelector接口实现的类

4.   ImportBeanDefinitionRegistrar接口实现的类

### 1.3 @Conditional注解

```tex
@ConditionalOnBean
仅仅在当前上下文中存在某个对象时，才会实例化一个Bean

@ConditionalOnClass
某个class位于类路径上，才会实例化一个Bean

@ConditionalOnExpression
当表达式为true的时候，才会实例化一个Bean

@ConditionalOnMissingBean
仅仅在当前上下文中不存在某个对象时，才会实例化一个Bean

@ConditionalOnMissingClass
某个class类路径上不存在的时候，才会实例化一个Bean

@ConditionalOnNotWebApplication
不是web应用

@ConditionalOnProperty
指在配置里配置的属性是否为true,才会实例化一个Bean)

@ConditionalOnResource
如果我们要加载的 bean 依赖指定资源是否存在于classpath 中，那么我们就可以使用这个注解

@ConditionalOnJndi
只有指定的资源通过 JNDI 加载后才加载 bean

@ConditionalOnCloudPlatform
只有运行在指定的云平台上才加载指定的 bean，CloudPlatform 是org.springframework.boot.cloud 下一个 enum 类型的类
```



## 2. 原生配置文件引入的注解之@ImportResource

导入原生的xml配置文件到springboot项目中



## 3. 配置信息自动绑定的注解@ConfigurationProperties

配置属性的自动绑定

1.   使用@Value注解进行手动绑定
2.   通过@ConfigurationProperties + @Component注解进行属性的自动绑定
3.   @ConfigurationProperties + @EnableConfigurationProperties注解进行属性的绑定，在配置类上定义



