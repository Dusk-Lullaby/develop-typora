# 09_springboot整合数据持久层

## 9.1 整合jdbc

*   `pom.xml`

    ```xml
    <!--导入jdbc启动器-->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <!--导入数据库驱动-->
    <dependency>
      <groupId>com.mysql</groupId>
      <artifactId>mysql-connector-j</artifactId>
      <version>8.0.33</version>
    </dependency>
    <!--整合junit-->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
    </dependency>
    ```

*   `application.yml`

    ```yml
    spring:
      datasource:
        type: com.zaxxer.hikari.HikariDataSource  #描述数据源的类型
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://localhost:3306/mybatis_lesson
        username: root
        password: root
    ```

*   `test`

    ```java
    package com.sonnet;
    
    import org.junit.jupiter.api.Test;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.test.context.SpringBootTest;
    import org.springframework.jdbc.core.JdbcTemplate;
    
    import javax.sql.DataSource;
    
    @SpringBootTest(classes = App.class)
    public class TestDataSource {
    
        @Autowired
        DataSource dataSource;
    
        @Autowired
        JdbcTemplate jdbcTemplate;
    
        @Test
        public void testDataSource() {
            System.out.println(dataSource);
        }
    
        @Test
        public void testJdbcTemplate() {
            System.out.println(jdbcTemplate.queryForObject("select count(*) from account", Integer.class));
        }
    }
    ```

## 9.2 整合druid数据源

springboot底层整合的是HikariDataSource数据源。但是在企业开发中，还有一个第三方的数据源也使用比较多。这个数据源就是Druid数据源。

如何使用Druid数据源呢。有两种方式，一种是使用自定义的方式整合druid数据源。第二种是使用starter的方式整合druid数据源。

### 9.2.1 使用自定义的方式整合druid数据源

*   导入druid数据源依赖

    ```xml
    <!--导入druid数据源依赖-->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.17</version>
    </dependency>
    ```

*   定义配置类，将Druid数据源注入到IOC容器里面

    ```java
        // 将Druid数据源配置到ioc容器中
        @ConfigurationProperties("spring.datasource")
        @Bean
        public DataSource dataSource() {
            DruidDataSource dataSource = new DruidDataSource();
    /*
            dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
            dataSource.setUrl("jdbc:mysql://localhost:3306/mybatis_lesson");
            dataSource.setUsername("root");
            dataSource.setPassword("root");
    */
            return dataSource;
        }
    ```

*   配置druid数据源监控

    ```java
    // 配置druid监控页的功能
    @Bean
    public ServletRegistrationBean servletRegistrationBean1() {
        StatViewServlet statViewServlet = new StatViewServlet();
        ServletRegistrationBean<Servlet> servletServletRegistrationBean =
                new ServletRegistrationBean<>(statViewServlet, "/druid/*");
        servletServletRegistrationBean.addInitParameter("loginUsername", "admin");
        servletServletRegistrationBean.addInitParameter("loginPassword", "123456");
        return servletServletRegistrationBean;
    }
    
    // 配置过滤器，用于采集用户操作数据库的数据
    @Bean
    public FilterRegistrationBean webStatFilter() {
        WebStatFilter webStatFilter = new WebStatFilter();
        FilterRegistrationBean<WebStatFilter> bean =
                new FilterRegistrationBean<>(webStatFilter);
        bean.setUrlPatterns(Arrays.asList("/*"));
        bean.addInitParameter("exclusions","*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*");
        return bean;
    }
    ```

*   controller

    ```java
    @RequestMapping("query")
    public String query() {
        return jdbcTemplate.queryForObject("select count(*) from account", Long.class).toString();
    }
    ```

*   开启监控

    ```java
        // 将Druid数据源配置到ioc容器中
        @ConfigurationProperties("spring.datasource")
        @Bean
        public DataSource dataSource() throws SQLException {
            DruidDataSource dataSource = new DruidDataSource();
    /*
            dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
            dataSource.setUrl("jdbc:mysql://localhost:3306/mybatis_lesson");
            dataSource.setUsername("root");
            dataSource.setPassword("root");
    */
            // 开启监控
            dataSource.setFilters("stat");
            return dataSource;
        }
    ```

#### 9.2.2 使用starter方式整合druid数据源

*   在pom.xml里面导入druid启动器：

    ```java
    <!--druid数据源启动器-->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid-spring-boot-starter</artifactId>
      <version>1.1.17</version>
    </dependency>
    ```

*   可以对druid进行详细配置

    ```yml
    spring:
      datasource:
        type: com.zaxxer.hikari.HikariDataSource  #描述数据源的类型
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://localhost:3306/mybatis_lesson
        username: root
        password: root
        druid:
          stat-view-servlet:
            enabled: true
            login-username: admin
            login-password: 123456
          web-stat-filter:
            enabled: true
            url-pattern: /*
            exclusions: '*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*'
          filter:
            stat:
              enabled: true
              slow-sql-millis: 1000
              log-slow-sql: true
            wall:
              enabled: true
              config:
                delete-allow: false
    
    ```


## 9.3 整合mybatis

 ### 9.3.1 使用纯注解的方式整合mybatis

*   准备实体类，接口，service，controller

    ```java
    package com.sonnet.pojo;
    
    import lombok.Data;
    
    @Data
    public class Account {
    
        private Integer id;
        private Integer UID;
        private Double money;
    }
    ```

    ```java
    package com.sonnet.service;
    
    import com.sonnet.pojo.Account;
    
    public interface AccountService {
    
        public Account findById(Integer id);
    
    }
    ```

    ```java
    package com.sonnet.service.impl;
    
    import com.sonnet.mapper.AccountMapper;
    import com.sonnet.pojo.Account;
    import com.sonnet.service.AccountService;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;
    
    @Service
    public class AccountServiceImpl implements AccountService {
    
        @Autowired
        AccountMapper accountMapper;
    
        @Override
        public Account findById(Integer id) {
            return accountMapper.findById(id);
        }
    }
    ```

    ```java
    @Autowired
    AccountService accountService;
    
    @RequestMapping("/findById")
    public Account findById(@RequestParam("id") Integer id) {
        return accountService.findById(id);
    }
    ```

*   mybatis核心配置文件

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration
            PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
    </configuration>
    ```

*   mybatis的映射文件

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="com.sonnet.mapper.AccountMapper">
        <select id="findById" parameterType="int"
                resultType="com.sonnet.pojo.Account">
            select * from account where id = #{id}
        </select>
    </mapper>
    ```

*   在application.properties里面定义mybatis的配置文件

    ```yml
    # mybatis
    mybatis:
      config-location: classpath:mybatis/sqlMapConfig.xml
      mapper-locations: classpath:mybatis/mapper/*.xml
    ```

### 9.3.2 使用纯注解方式整合mybatis

*   定义接口

    ```java
    @Select("select * from account")
    public List<Account> findAll();
    ```

*   定义service

    ```java
    public List<Account> findAll();
    ```

    ```java
    @Override
    public List<Account> findAll() {
        return accountMapper.findAll();
    }
    ```

*   定义controller

    ```java
    @RequestMapping("/findAll")
    public List<Account> findAll() {
        return accountService.findAll();
    }
    ```

每次在接口上都要使用@Mapper注解，每个接口都写这个注解很麻烦，我们可以批量开启对接口的扫描。我们可以注释掉接口上的@Mapper注解。在启动类上使用@MapperScan注解。

```java
package com.sonnet;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("com.sonnet.mapper") // 批量mapper接口
public class App {

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

## 9.4 整合mybatisplus

MyBatis-Plus（简称 MP）是一个 MyBatis 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

*   导入依赖

    ```xml
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.4.1</version>
    </dependency>
    ```

*   创建数据表

    ```sql
    CREATE TABLE tb_user
    (
    id BIGINT(20) NOT NULL COMMENT '主键ID',
    `name` VARCHAR(30) NULL DEFAULT NULL COMMENT '姓名',
    age INT(11) NULL DEFAULT NULL COMMENT '年龄',
    email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
    PRIMARY KEY (id)
    );
    ```

*   创建实体类

    ```java
    package com.sonnet.pojo;
    
    import lombok.Data;
    
    @Data
    public class Account {
    
        private Integer id;
        private Integer UID;
        private Double money;
    }
    ```

*   创建接口

    ```java
    package com.sonnet.mapper;
    
    import com.baomidou.mybatisplus.core.mapper.BaseMapper;
    import com.sonnet.pojo.User;
    
    public interface UserMapper extends BaseMapper<User> {
    
    }
    ```

*   测试

    ```java
    @Autowired
    UserMapper userMapper;
    
    @Test
    public void testUserMapper() {
        System.out.println(userMapper.selectById(1));
    }
    ```

## 9.5 整合redis

springboot整合redis，只需要引入redis相关的启动器即可

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

## 9.6 整合junit5

### 9.6.1 junit5简介

Junit5的框架主要有三个部分组成分别是：Junit Platform + Junit Jupiter +Junit Vintage3

*   Junit Platform ：

    其主要作用是在 JVM 上启动测试框架。它定义了一个抽象的TestEngine API 来定义运行在平台上的测试框架；也就是说其他的自动化测试引擎或开发人员⾃⼰定制的引擎都可以接入 Junit 实现对接和执行。同时还支持通过命令行、Gradle 和 Maven 来运行平台（这对于我们做自动化测试至关重要）

*   Junit Jupiter：

    这是 Junit5 的核心，可以看作是承载 Junit4 原有功能的演进，包含了JUnit 5 最新的编程模型和扩展机制；很多丰富的新特性使 JUnit ⾃动化测试更加方便、功能更加丰富和强大。也是测试需要重点学习的地方；Jupiter 本身也是⼀一个基于 Junit Platform 的引擎实现，对 JUnit 5 而言，JUnit Jupiter API 只是另一个 API！。

*   Junit Vintage3

    Junit 发展了10数年，Junit 3 和 Junit 4 都积累了大量的⽤用户，作为新一代框 架，这个模块是对 JUnit3，JUnit4 版本兼容的测试引擎，使旧版本 junit 的⾃动化测试脚本也可以顺畅运行在 Junit5 下，它也可以看作是基于 Junit Platform 实现的引擎范例。

![](img/junit.png)

注意：SpringBoot2.4以上版本移除了默认对Vintage的依赖。如果需要兼容Junit4需要自行引入

```xml
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
```

要在springboot里面使用junit5.我们直接在pom文件里面引入相

关的启动器即可。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

测试：

```java
package com.sonnet;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest(classes = App.class)
public class TestJunit {

    @Test
    public void test01() {
        System.out.println("hello");
    }

}
```

### 9.6.2 junit5常用测试注解

*   @DisplayName：为测试类或者测试方法设置展示名称
*   @BeforeEach：表示在每个单元测试之前执行
*   @AfterEach：表示每个单元测试之后执行
*   @BeforeAll：表示在所有单元测试之前执行
*   @AfterAll：表示在所有单元测试之后执行
*   @Disabled：表示测试类或者测试方法不执行
*   @Timeout：表示测试方法运行如果超过了指定时间将会返回错误

```java
package com.sonnet;

import org.junit.jupiter.api.*;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.concurrent.TimeUnit;

@DisplayName("junit单元测试")
@SpringBootTest(classes = App.class)
public class TestJunit {

    @BeforeEach // 在每个单元测试方法之前执行
    public void before() {
        System.out.println("这是beforeEach方法");
    }

    @AfterEach // 在每个单元测试方法之后执行
    public void after() {
        System.out.println("这是afterEach方法");
    }

    @BeforeAll // 在所有测试方法之前执行
    public static void beforeAll() {
        System.out.println("这是BeforeAll方法");
    }

    @AfterAll // 在所有测试方法之后执行
    public static void afterAll() {
        System.out.println("这是afterAll方法");
    }

    @Test
    public void test01() {
        System.out.println("test01方法");
    }

    @Test
    @Disabled // 指定某一个测试方法不执行
    public void test02() {
        System.out.println("test02方法");
    }

    @Disabled("测试方法是否超时")
    @Test
    @Timeout(value = 500, unit = TimeUnit.MILLISECONDS) // 指定这个方法在500毫秒之内执行，如果超过了指定的时间，就直接报错
    public void test03() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        System.out.println("这是test03");
    }
}
```

### 9.6.3 springboot中的断言机制

断言（assertions）是测试方法中的核心部分，用来对测试需要满足的条件进行验证。这些断言方法都是org.junit.jupiter.api.Assertions 的静态方法

| 方法            | 说明                                 |
| --------------- | ------------------------------------ |
| assertEquals    | 判断两个对象或两个原始类型是否相等   |
| assertNotEquals | 判断两个对象或两个原始类型是否不相等 |
| assertSame      | 判断两个对象引用是否指向同一个对象   |
| assertNotSame   | 判断两个对象引用是否指向不同的对象   |
| assertTrue      | 判断给定的布尔值是否为 true          |
| assertFalse     | 判断给定的布尔值是否为 false         |
| assertNull      | 判断给定的对象引用是否为 null        |
| assertNotNull   | 判断给定的对象引用是否不为 null      |

```java
package com.sonnet;

import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

import java.time.Duration;

@DisplayName("测试断言的测试类")
@SpringBootTest(classes = App.class)
public class TestAssert {

    @DisplayName(("测试assertEquals"))
    @Test
    public void test01() {
        /**
         * assertEquals 判断两个值是否相等
         * 参数1：预期值
         * 参数2：实际值
         * 参数3：预期值和实际值不相等，输出的提示信息
         */
        Assertions.assertEquals(4, getNum(2, 3), "断言失败，预期值和实际值不相等");
        System.out.println("这是test01");
    }

    public int getNum(int num1, int num2) {
        return num1 + num2;
    }

    @DisplayName("测试assertNotEquals")
    @Test
    public void test02() {
        Object o1 = new Object();
        Object o2 = new Object();
        Assertions.assertNotEquals(o1, o2, "两个对象是同一个对象");
    }

    @DisplayName("测试assertSame")
    @Test
    public void test03() {
        Object o1 = new Object();
        Object o2 = new Object();
        Assertions.assertSame(o1, o2, "两个对象指向同一个内存地址");
        System.out.println("这是test02方法");
    }

    @DisplayName("测试AssertTrue")
    @Test
    public void test04() {
        Assertions.assertTrue(2 < 1, "表达式必须为true才会执行测试方法");
        System.out.println("这是test04方法");
    }

    @DisplayName("测试assertArrayEquals数组")
    @Test
    public void test05() {
        int[] arr1 = new int[]{1, 2, 3, 4, 5};
        int[] arr2 = new int[]{1, 2, 3, 4};
        Assertions.assertArrayEquals(arr1, arr2, "数组内容不相等");
        System.out.println("这是test05");
    }

    @DisplayName("测试组合断言")
    @Test
    public void test06() {
        Assertions.assertAll("a",
                ()->Assertions.assertEquals(2, 2, "两个值不相等"),
                ()->Assertions.assertTrue(1 > 8, "预期结果必须为true"));
        System.out.println("test06");
    }

    @DisplayName("测试超时断言")
    @Test
    public void test07() {
        Assertions.assertTimeout(Duration.ofMillis(1000), ()->{
            Thread.sleep(1500);
        }, "执行超时");
        System.out.println("test07");
    }
}
```

### 9.6.4 单元测试--前置条件

junit5中的前置条件（assumption假设）类似于断言，不同之处在于不满足的断言会使测试方法失败，而不满足的前置条件只会使测试方法的执行终止。前置条件可以看成是测试方法执行的前提，当该前提不满足时，就没有继续执行的必要。

```java
@Test
public void test08() {
    Assumptions.assumeTrue(false, "预期结果不为true");
    System.out.println("test08");
}
```

### 9.6.5 单元测试--参数化测试

参数化测试是JUnit5很重要的一个新特性，它使得用不同的参数多次运行测试成为了可能，也为我们的单元测试带来许多便利。

*   @ValueSource：

    为参数化测试指定入参来源，支持八大基础类以及

    String类型,Class类型。

*   @MethodSource：

    表示读取指定方法的返回值作为参数化测试入参

    (注意方法返回需要是一个流)。

```java
package com.sonnet;

import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.MethodSource;
import org.junit.jupiter.params.provider.ValueSource;
import org.junit.platform.commons.util.StringUtils;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.stream.Stream;

@DisplayName("参数化测试")
@SpringBootTest(classes = App.class)
public class TestParameter {

    @ParameterizedTest // 参数化测试的注解
    @ValueSource(strings = {"apple", "orange", "banana"})
    @DisplayName("基于string类型的参数进行参数化设置")
    public void testParameter1(String string) {
        System.out.println(string);
        Assertions.assertTrue(StringUtils.isNotBlank(string));
        System.out.println("testParameter1");
    }
    
    @ParameterizedTest
    @MethodSource("show")
    @DisplayName("基于方法的参数化测试")
    public void testParameter2(String name) {
        System.out.println(name);
        Assertions.assertNotNull(name);
        System.out.println("testParameter2");
    }

    static Stream<String> show() {
        return Stream.of("apple", "orange", "banana");
    }
}
```

## 9.7 springboot监控指标（见PDF）

### 9.7.1 Actuator与endpoint

### 9.7.2 endpoint的开启与禁用

### 9.7.3 Admin Server可视化界面监控

