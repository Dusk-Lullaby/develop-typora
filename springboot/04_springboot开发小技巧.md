# 04_springboot开发技巧

## 1. lombok简化开发



导入依赖

```xml
<!--引入Lombok-->
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
</dependency>
```

*   简化javaBean

    ```java
    package com.sonnet.pojo;
    
    import lombok.AllArgsConstructor;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    
    @Data // get set toString 方法都不需要自己写
    @AllArgsConstructor // 定义所有带参数的构造函数
    @NoArgsConstructor // 定义无参数的构造函数
    public class Emp {
    
        private String username;
    
        private Integer age;
    }
    ```

*   简化日志开发

    ```java
    @RestController
    @Slf4j
    public class HelloController {
        @RequestMapping("hello")
        public String hello(){
            log.info("hello方法执行了......");
            return "大家好";
        }
    }
    ```

    

## 2. dev-tools热部署工具

在实际项目开发中，开发的过程中一定会修改代码，如果每次修改代码都需要重新启动下，那会让人吐血的。这里我们使用Spring-boot-devtools进行热部署。

*   引入部署依赖

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
    ```

*   引入插件

    ```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>
    ```