# 03_SpringMVC

## 1. SpringMVC的基本概述

### 1.1 三层架构的基本概述

```tex
我们的开发架构一般都是基于两种形式，一种是c/s架构，也就是客户端/服务器。另一种是b/s架构，也就是浏览器/服务器。在JavaEE开发中，几乎全部是基于b/s架构的开发。那么在b/s架构中，系统的标准三层架构包括：表现层、业务层、持久层。三层架构在我们实际开发中使用的非常多,所以我们课程中的案例也是基于三层架构设计的。

三层架构，每层架构各司其职，接下来我们说说每层架构都有哪些作用：

表现层：

也就是我们常说的web层。它负责接收客户端的请求，向客户端响应结果，通常客户端使用http协议请求web层，web需要接收http请求，完成http响应。表现层包含展示层和控制层，控制层负责接收前端请求，展示层负责结果的展示。表现层依赖业务层，接收到客户端请求一般会调用业务层进行业务处理，并将处理结果响应给客户端。表现层的设计一般都使用MVC模型。

业务层：

也就是我们常说的service层。它负责业务逻辑的处理，和我们开发项目的需求息息相关。web层依赖业务层，但是业务层不依赖web层。业务层在业务处理时可能会依赖持久层，如果要对数据持久化需要保证事务一致性(也就是我们说的事务应该放在业务层来控制)。

持久层：

也就是我们常说的dao层。负责数据持久化，包括数据层即数据库和数据访问层。数据库是对数据进行持久化的载体，数据访问层是业务层和持久层交互的接口，业务层需要通过对数据访问层将数据持久化到数据库中，通俗的讲，持久层就是和数据库交互，对数据表进行增删改查的。
```

### 1.2 MVC架构

```tex
MVC全名是Model View Controller.是模型(Model) 视图(view) 控制器(controller)的缩写。是一种用于创建web应用程序表现层的模式。MVC中每个部分各司
其职：
Model(模型):
通常指的就是我们的数据模型,用来封装数据的。
View(视图):
通常指的是我们的jsp或者html。一般用来展示数据的，通常视图是依据模型数据创建的。
Controller(控制器):
是应用程序中处理用户交互的部分。用于接收前端请求。比如获取前端请求传递的参数，并负责将请求交给业务层进行处理。业务层将业务结果处理完成以后，控制器接收业务层的业务结果，并将业务数据存储在域对象，实现页面的跳转。
```

### 1.3 什么是springMVC

```tex
SpringMVC是一种基于Java的实现MVC设计模型的请求驱动类型的轻量级web框架，属于Spring FrameWork的后续产品，已经融合在Spring Web Flow里面。Spring框架提供了构建web应用程序的全功能MVC模块。使用Spring可插入的MVC架构，从而在使用Spring进行web开发时，可以选择使用Spring的Spring MVC框架或集成其他MVC开发框
架，比如Struts2等。
SpringMVC已经成为目前最主流的MVC框架之一，并且随着Spring 3.0的发布，全面超越Struts2,成为最优秀的web框架。它通过一套注解，让一个简单的Java类成为处理请求的控制器，而无需事先任何接口，同时它还支持Restful编程风格。
```

![](imgs/什么是springMVC.png)

### 1.4 SpringMVC的优势

```tex
1. 清晰的角色划分：
前端控制器(DispatcherServlet)
处理器映射器(HandlerMapping)
处理器适配器(HandlerAdpater)
视图解析器(ViewResolver)
控制器(Controller)
验证器(Validator)
命令对象(Command 请求参数绑定到的对象就叫做命令对象)
表单对象(Form Object 提供给表单展示和提交的对象就叫表单对象)
2. 分工明确，而且扩展点相当灵活，很容易进行功能扩展。
3. 由于命令对象就是一个POJO，无需继承框架特定的API,可以使用命令对象直接作为业
务对象。
4. 和Spring其他框架无缝集成，是其他web框架不具备的。
5. 可适配，通过HandlerAdapter可以支持任意的类作为处理器。
6. 可定制性，HandlerMapping ViewResolver等能够非常简单的定制。
7. 功能强大的数据验证、格式化、绑定机制。
```

## 2. SpringMVC入门

### 2.1 SpringMVC入门案例的搭建

#### 2.1.1 导入依赖

```xml
<properties>
    <spring.version>5.3.39</spring.version>
    <servlet.version>4.0.1</servlet.version>
    <jsp.version>2.3.3</jsp.version>
    <tomcat.version>9.0.123</tomcat.version>
    <cargo.version>1.10.29</cargo.version>
</properties>
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>${servlet.version}</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>javax.servlet.jsp-api</artifactId>
        <version>${jsp.version}</version>
        <scope>provided</scope>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.4.0</version>
        </plugin>
        <plugin>
            <groupId>org.codehaus.cargo</groupId>
            <artifactId>cargo-maven3-plugin</artifactId>
            <version>${cargo.version}</version>
            <configuration>
                <container>
                    <containerId>tomcat9x</containerId>
                    <type>installed</type>
                    <artifactInstaller>
                        <groupId>org.apache.tomcat</groupId>
                        <artifactId>tomcat</artifactId>
                        <version>${tomcat.version}</version>
                        <type>zip</type>
                    </artifactInstaller>
                </container>
                <configuration>
                    <type>standalone</type>
                    <home>${project.build.directory}/cargo/configuration</home>
                    <properties>
                        <cargo.servlet.port>8088</cargo.servlet.port>
                    </properties>
                </configuration>
                <deployables>
                    <deployable>
                        <groupId>${project.groupId}</groupId>
                        <artifactId>${project.artifactId}</artifactId>
                        <type>war</type>
                        <properties>
                            <context>/</context>
                        </properties>
                    </deployable>
                </deployables>
            </configuration>
        </plugin>
    </plugins>
</build>
```

#### 2.1.2 配置前端控制器

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <!--配置前端控制器-->
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--加载springmvc配置文件-->
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <!--提前DispatcherServlet前端控制器的加载时机-->
    <load-on-startup>1</load-on-startup>
  </servlet>

  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
</web-app>
```

#### 2.1.3 编写springmvc.xml配置文件

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--开启包扫描-->
    <context:component-scan base-package="com.sonnet"></context:component-scan>

    <!--配置视图解析器-->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/pages/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>

    <!--开启处理器映射器 处理器适配器的自动配置-->
    <mvc:annotation-driven></mvc:annotation-driven>
</beans>
```

#### 2.1.4 编写jsp页面

```jsp
<%--
  Created by IntelliJ IDEA.
  User: sonnet
  Date: 2026/10/22
  Time: 17:25
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h4>这是一个成功页面</h4>
</body>
</html>
```

#### 2.1.5 编写控制器代码

```java
package com.sonnet.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HelloController {

    @RequestMapping("/hello")
    public String hello() {
        return "success";
    }
}
```

#### 2.1.6 SpringMVC入门程序分析

![](imgs/mvc流程.png)

>   第一步：发起请求到前端控制器(DispatcherServlet)
>
>   第二步：前端控制器请求HandlerMapping查找 Handler （可以根据xml配置、注解进行查找）
>
>   第三步：处理器映射器HandlerMapping向前端控制器返回Handler，HandlerMapping会把请求映射为HandlerExecutionChain对象（包含一个Handler处理器（页面控制器）对象，多个HandlerInterceptor拦截器对象），通过这种策略模式，很容易添加新的映射策略
>
>   第四步：前端控制器调用处理器适配器去执行Handler
>
>   第五步：处理器适配器HandlerAdapter将会根据适配的结果去执行Handler
>
>   第六步：Handler执行完成给适配器返回ModelAndView
>
>   第七步：处理器适配器向前端控制器返回ModelAndView（ModelAndView是springmvc框架的一个底层对象，包括 Model和view）
>
>   第八步：前端控制器请求视图解析器去进行视图解析 （根据逻辑视图名解析成真正的视图(jsp)），通过这种策略很容易更换其他视图技术，只需要更改视图解析器即可
>
>   第九步：视图解析器向前端控制器返回View
>
>   第十步：前端控制器进行视图渲染 （视图渲染将模型数据(在ModelAndView对象中)填充到request域）
>
>   第十一步：前端控制器向用户响应结果

![](imgs/流程.png)

### 2.2 SpringMVC核心组件

*   DispatcherServlet 前端控制器

    用户请求到达前端控制器，它就相当于mvc模式中的c，dispatcherServlet是整个流程控制的中心，由它调用其它组件处理用户的请求，dispatcherServlet 的存在降低了组件之间的耦合性。

*   HandlerMapping 处理器映射器

    HandlerMapping负责根据用户请求找到 Handler 即处理器，SpringMVC提供了不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等。

*   Handler 处理器

    它就是我们开发中要编写的具体业务控制器。由DispatcherServlet 把用户请求转发到 Handler。由Handler对具体的用户请求进行处理。

*   HandlAdapter 处理器适配器

    通过 HandlerAdapter 对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。

*   View Resolver 视图解析器

    View Resolver负责将处理结果生成View 视图，View Resolver首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成 View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。

*   View 视图

    SpringMVC框架提供了很多View视图类型的支持，包括：jstlView、freemarkerView、pdfView等。我们最常用的视图就是jsp。一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展示给用户，需要由程序员根据业务需求开发具体的页面。
