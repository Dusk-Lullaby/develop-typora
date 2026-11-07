# 10_springboot高级特性

## 10.1 profile环境切换

### 10.1.1 配置文件环境的切换

*   引入依赖

    ```xml
    <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.4.0</version>
    </parent>
    <dependencies>
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
      <dependency>
        <groupId>org.junit.vintage</groupId>
        <artifactId>junit-vintage-engine</artifactId>
        <scope>test</scope>
        <exclusions>
          <exclusion>
            <groupId>org.hamcrest</groupId>
            <artifactId>hamcrest-core</artifactId>
          </exclusion>
        </exclusions>
      </dependency>
    </dependencies>
    <build>
      <plugins>
        <plugin>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
      </plugins>
    </build>
    ```

*   编写启动器

    ```java
    package com.sonnet;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    /**
     * Hello world!
     *
     */
    @SpringBootApplication
    public class App 
    {
        public static void main( String[] args )
        {
            SpringApplication.run(App.class, args);
        }
    }
    ```

*   编写控制器

    ```java
    package com.sonnet.controller;
    
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    @RestController
    public class HelloController {
    
        @Value("${person.name:李四}") // 如果没有获取到指定的值，就用默认值
        public String name;
    
        @RequestMapping("/hello")
        public String hello() {
            return "Hello " + name;
        }
    
    }
    // localhost:8080/hello
    ```

*   编写配置文件

    默认配置文件 application.properties：

    ```properties
    server.port=8080
    ```

    指定环境配置文件 application-{env}.yml

    比如开发环境配置文件： dev application-dev.yml

    生产环境配置文件：prod application-prod.yml

    测试环境配置文件： test application-test.yml

    模拟定义生产环境配置文件( application-prod.yml)：

    ```yml
    person:
      name: pord-张三
    
    server:
      port: 8000
    ```

    测试环境配置文件(application-test.yml):

    ```yml
    person:
      name: test-张三
    
    server:
      port: 7000
    ```

*   获取指定配置文件上的name值

    在springboot默认配置文件中，也就是在application.properties中定义相关的配置信息:

    ```properties
    server.port=8080
    
    # 指定加载prod环境中的配置文件
    spring.profiles.active=test
    ```

### 10.1.2 @Profile注解

*   定义接口

    ```java
    package com.sonnet.pojo;
    
    public interface Person {
        String getName();
    
        Integer getAge();
    }
    ```

*   定义pojo

    ```java
    package com.sonnet.pojo;
    
    import lombok.Data;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.annotation.Profile;
    import org.springframework.stereotype.Component;
    
    @Profile("prod")
    @Component
    @ConfigurationProperties("person")
    @Data
    public class Boss implements Person{
        String name;
        Integer age;
    }
    ```

    ```java
    package com.sonnet.pojo;
    
    import lombok.Data;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.annotation.Profile;
    import org.springframework.stereotype.Component;
    
    @Profile("test")
    @Component
    @ConfigurationProperties("person")
    @Data
    public class Worker implements Person{
    
        private Integer age;
        private String name;
    
    }
    ```

*   定义Controller

    ```java
    @Autowired
    Person person;
    
    @RequestMapping("person")
    public String getPerson() {
        return person.getClass().toString();
    }
    ```

*   定义配置文件

    `application.properties`

    ```properties
    server.port=8080
    
    # 指定加载prod环境中的配置文件
    spring.profiles.active=prod
    ```

    `application-prod.yml`

    ```yml
    person:
      name: pord-张三
      age: 12
    
    server:
      port: 8000
    ```

    `application-test.yml`

    ```yml
    person:
      name: test-张三
      age: 20
    
    server:
      port: 7000
    ```

### 10.1.3 profile分组

也可以将配置文件分组，多个配置文件一同加载。

`application.properties`

```properties
server.port=8080

# 指定加载prod环境中的配置文件
#spring.profiles.active=prod

# 加载指定组的配置文件
spring.profiles.active=myprod

#myprod组里面放置prod的配置文件
spring.profiles.group.myprod[0]=prod
#myprod组里面放置test的配置文件
spring.profiles.group.myprod[1]=test
```

`application-prod.yml`

```yml
person:
  name: pord-张三
#  age: 12

server:
  port: 8000
```

`application-dev.yml`

```yml
person:
#  name: test-张三
  age: 20

#server:
#  port: 7000
```

定义controller

```java
@Autowired
Emp emp;

@RequestMapping("/emp")
public Emp getEmp() {
    return emp;
}
```



## 10.2 springboot配置文件加载优先级

springboot 启动会扫描以下位置的application.properties或者application.yml文件作为Spring boot的默认配置文件

*   file:./config/ 项目根目录下面的config文件夹 优先级最高
*   file:./ 项目根目录下面
*   classpath:/config/ resources文件夹下面的config文件夹
*   classpath:/ resources文件夹下面 优先级最低

优先级由高到低，高优先级的配置会覆盖低优先级的配置；

SpringBoot会从这四个位置全部加载主配置文件；互补配置；



## 10.3 springboot自定义starter

**第一步：创建空工程**

**第二步：导入依赖**

在`springboot-customize-autoconfig`中导入依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.4.0</version>
    <relativePath/>
</parent>

<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.2</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>
</dependencies>
```

在`springboot-customize-boot-starter`中导入依赖

```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.2</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>com.sonnet</groupId>
        <artifactId>springboot-cusyomize-aotuconfig</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>
```

**第三步：编写业务代码**

*   编写pojo

    ```java
    package com.sonnet.sonnet.pojo;
    
    import org.springframework.boot.context.properties.ConfigurationProperties;
    
    @ConfigurationProperties("sonnet.hello")
    public class HelloProperties {
    
        private String prefix;
        private String suffix;
    
        public String getPrefix() {
            return prefix;
        }
    
        public void setPrefix(String prefix) {
            this.prefix = prefix;
        }
    
        public String getSuffix() {
            return suffix;
        }
    
        public void setSuffix(String suffix) {
            this.suffix = suffix;
        }
    }
    ```

*   编写业务类

    ```java
    package com.sonnet.sonnet.service;
    
    import com.sonnet.sonnet.pojo.HelloProperties;
    import org.springframework.beans.factory.annotation.Autowired;
    
    public class HelloService {
    
        @Autowired
        HelloProperties helloProperties;
    
        public String sayHello(String username) {
            return helloProperties.getPrefix() + " <---> " + username + "<--->" + helloProperties.getSuffix();
        }
    }
    ```

    注意：这个业务类不要直接使用@Component注解管理，因为用户要使用这个业务类。是需要开启自动配置，这个bean才会被放在ioc容器里面去的

*   编写自动配置类

    开启对业务代码的自动配置。一般这个类我们都名为为XxxAutoConfiguration

    ```java
    package com.sonnet.sonnet.config;
    
    import com.sonnet.sonnet.pojo.HelloProperties;
    import com.sonnet.sonnet.service.HelloService;
    import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
    import org.springframework.boot.context.properties.EnableConfigurationProperties;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    
    @Configuration
    @EnableConfigurationProperties(HelloProperties.class)
    public class HelloServiceAutoConfiguration {
    
        @ConditionalOnMissingBean(HelloService.class)
        @Bean
        public HelloService helloService() {
            return new HelloService();
        }
    }
    ```

**第四步：编写spring.factories文件**

springboot项目启动之后，启动器里面的资源要生效，都是通过加载spring.factories配置文件才会生效

```factories
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.sonnet.sonnet.config.HelloServiceAutoConfiguration
```

最后分别clean，再intall。将打成的jar包发布到本地仓库即可。

**第五步：使用自定义的starter**

打开一个新项目，引入自定义的启动器依赖。

*   引入依赖

    ```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.0</version>
    </parent>
    
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    
        <dependency>
            <groupId>com.sonnet</groupId>
            <artifactId>springboot-customize-boot-starter</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
    </dependencies>
    ```

*   编写controller

    ```java
    package com.sonnet.controller;
    
    import com.sonnet.sonnet.service.HelloService;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    @RestController
    public class HelloController {
    
        @Autowired
        HelloService helloService;
    
        @RequestMapping("/hello")
        public String hello() {
            return helloService.sayHello("jiayi");
        }
    
    }
    ```

*   编写配置文件

    ```yml
    sonnet:
      hello:
        prefix: hello
        suffix: springboot
    ```
