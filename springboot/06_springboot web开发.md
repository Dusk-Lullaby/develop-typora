# 06_springboot web开发

## 1. 静态资源规则与定制化

springboot对静态资源的映射规则：

在类路径下面定义目录：

resources:

public:

static:

META_INF/resources:

访问静态资源的路径是：项目根目录+静态资源的名称：localhost:8080/a.html

需要注意：

在springboot中，如果项目中存在同名的静态资源和同名的动态资源，那么我们会优先去访问动态资源。如果动态资源不存在，然后再去访问对应的静态资源，如果静态资源也找不到，就报404异常

如果我们想自定义静态资源的映射规则，比如我们可以静态给静态资源的路径添加前缀

```yml
# 自定义静态资源映射规则（配置前缀）
spring:
  mvc:
    static-path-pattern: /hello/**
```

也可以给静态资源设置自定义的存放目录

```yml
# 自定义静态资源的存放目录
spring:
  web:
    resources:
      static-locations: [classpath:/hello/]
```



## 2. springboot常用参数注解使用

在springboot的controller中常用的参数注解有：@PathVariable、@RequestHeader、@ModelAttribute、@RequestParam、@MatrixVariable、@CookieValue、@RequestBody。

### 2.1 @PathVariable注解

通过 @PathVariable 可以将 URL 中占位符参数绑定到控制器处理方法的入参中：URL 中的www.xxx.com/user/{xxx}/num/{bbb} 占位符可以通过@PathVariable(“xxx“)。

```java
package com.sonnet.controller;

import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * springboot中常用参数注解的使用
 * localhost:8080/aa/10010/mm/123
 */
@RestController
public class ParamController {

    @RequestMapping("aa/{userId}/mm/{num}")
    public String testPathVariable(@PathVariable("userId") String userId, @PathVariable("num") Integer num) {
        System.out.println(userId);
        System.out.println(num);
        return "hello";
    }
}
```

### 2.2 @RequestParam注解

用户在前端拼接参数例如：key=value1&key2=value2参数列表。通过注解@RequestParam可以轻松的将URL中的参数绑定到处理函数方法的变量中。

```java
/**
 * 测试@Request注解
 * http://localhost:8080/testRequestParam?username=eric&age=12&address=USA
 * @return
 */
@RequestMapping("testRequestParam")
public String testRequestParam(@RequestParam("username") String name,
                               @RequestParam("age") Integer age,
                               @RequestParam("address") String address) {
    System.out.println(name);
    System.out.println(age);
    System.out.println(address);
    return "hello";
}
```

### 2.3 @RequestBody注解

定义表单

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="getData" method="post">
        <input type="text" name="username"> <br>
        <input type="password" name="password"> <br>
        <input type="submit" value="提交">
    </form>
</body>
</html>
```

定义控制器方法

```java
/**
 * 在同步情况下或者表单的实体内容
 * @param data
 * @return
 */
@RequestMapping("/getData")
public String getData(@RequestBody String data) {
    return data;
}
```

### 2.4 @CookieValue注解

```java
/**
 * 获取指定的Cookie值
 * @param cookieValue
 * @return
 */
@RequestMapping("/testCookieValue")
public String testCookieValue(@CookieValue("Idea-10501acc") String cookieValue) {
    System.out.println(cookieValue);
    return "hello";
}
```

### 2.5 @RequestHeader注解

```java
/**
 * 测试请求头
 * @param header
 * @return
 */
@RequestMapping("/testRequestHeader")
public String testRequestHeader(@RequestHeader("Accept-Encoding") String header) {
    System.out.println(header);
    return "success";
}
```

### 2.6 @RequestAttribute注解

用来标注在接口的参数上，参数的值来源于 request 作用域。

```java
package com.sonnet.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

import javax.servlet.http.HttpServletRequest;

/**
 * springboot中常用参数注解的使用
 * localhost:8080/aa/10010/mm/123
 */
//@RestController
@Controller
public class ParamController {

    @RequestMapping("/test1")
    public String test1(HttpServletRequest request) {
        request.setAttribute("username", "eric");
        return "forward:/test2";
    }

    /**
     * @RequestAttribute注解，用来获取Request作用域里面的值。修饰控制器方法的参数
     * @param username
     * @return
     */
    @RequestMapping("/test2")
    public String test2(@RequestAttribute("username") String username) {
        System.out.println(username);
        return username;
    }
}
```

注意：上面的是@Controller注解，千万不能写成@RestController。

### 2.7 @MatrixVariable注解

请求风格有3种

*   queryString请求方式(？拼接的方式)： /request?username=admin&password=123456&age=20
*   rest风格请求：/request/admin/123456/20
*   矩阵变量请求风
*   格：/request/path;username=admin;password=123456;age=20,21,22

在springboot中，矩阵变量的使用默认是不开启的。如果使用需要手动开启

```java
package com.sonnet.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.PathMatchConfigurer;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
import org.springframework.web.util.UrlPathHelper;

@Configuration
public class MyConfig implements WebMvcConfigurer {

    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        UrlPathHelper pathHelper = new UrlPathHelper();
        // 开启矩阵变量的使用权限
        pathHelper.setRemoveSemicolonContent(false);
        configurer.setUrlPathHelper(pathHelper);
    }
}
```

*   编写HTML

    ```html
    <a href="test/matrix/param;color=red,green,blue">测试矩阵变量</a>
    ```

*   编写Controller

    ```java
    //test/matrix/color=red,green,blue
    @RequestMapping("test/matrix/{param}")
    public @ResponseBody String testMatrix(@MatrixVariable(pathVar = "param", value = "color") String[] strs) {
        for (String str : strs) {
            System.out.println(str);
        }
        return "hello";
    }
    ```
