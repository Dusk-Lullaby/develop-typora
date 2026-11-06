# 01_springboot2基础入门

## 1. springboot入门案例

https://docs.spring.io/spring-boot/docs/2.7.18/reference/html/getting-started.html#getting-started.first-application.pom

1.   创建maven工程
2.   导入依赖
3.   编写启动类
4.   编写Cotroller
5.   允许springboot项目（直接启动main方法或者通过maven插件将springbooot项目打成jar包，通过java -jar命令启动）
6.   如有必要，可以定义springboot的配置文件`application.properties`或者`application.yml`

### 1.1 引入依赖

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.sonnet</groupId>
    <artifactId>springboot-day01-demo1</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.18</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <!--插件-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

### 1.2 创建主程序

```java
package com.sonnet;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication // 标识当前类是一个启动类
public class App {

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}

// localhost:8080/hello
```

### 1.3 编写业务

```java
package com.sonnet.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController// @Controller + @ResponseBody
public class Hello {

    @RequestMapping("/hello")
    public String hello() {
        return "hello, springboot";
    }
}
```

>   @RestController 等价于 @Controller + @ResponseBody

### 1.4 定义配置文件

定义配置文件`application.properties`。

```properties
# 定义web服务器的端口号
server.port=8888
```



## 2. springboot 依赖管理

![1.springboot依赖传递和自动配置](img/1.springboot依赖传递和自动配置.png)

## 3. springboot自动配置属性

1.   如果定义了某一个场景的启动器，那么在项目加载的时候，那么会开启对这个启动器的自动配置。会将这个起订器所属依赖里面的相关组件全部交给IOC容器进行管理
2.   如果我们自己写的组件 HelloController是通过@ComponentScan注解进行包扫描。这个注解@SpringBootApplication核心注解的内部，它的扫描规则是当前注解所属类（启动类）的目录及其子目录
3.   springboot不会将所有的场景的资源都进行自动配置，只有将导入对应场景的启动器，那么相关的组件才会被管理到IOC容器中