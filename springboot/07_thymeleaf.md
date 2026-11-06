# 07_thymeleaf

在springmvc中，我们进行视图跳转(重定向、转发)都需要借助于jsp才能实现。但是在springboot中默认不支持jsp。所以需要引入第三方模板引擎技术实现页面渲染

thymeleaf官网: https://www.thymeleaf.org/

thymeleaf也有自己的缺点，比如在高并发的场景下面，性能问题就比较突出。一般在高并发的场景下面，我们会采用前后端分离的技术。在单体项目中，使用thymeleaf就比较合适。

## 1. thymeleaf基本语法

*   表达式

    | 表达式名字 | 语法    | 用途                               |
    | ---------- | ------- | ---------------------------------- |
    | 变量取值   | ${...}  | 获取请求域、session域、对象等值    |
    | 选择变量   | *{...}  | 获取上下文对象值                   |
    | 消息       | \#{...} | 获取国际化等值                     |
    | 链接       | @{...}  | 生成链接                           |
    | 片段表达式 | ~{...}  | jsp:include 作用，引入公共页面片段 |

*   字面量
    *   文本值：'one text' , 'Another one!' ；数字： 0 , 34 , 3.0 , 12.3 ；布尔值：true , false
    *   空值：null
    *   变量： one，two，.... 变量不能有空格
*   文本操作
    *   字符串拼接： +
    *   变量替换： |The name is ${name}|
*   数学运算
    *   运算符： + , - , * , / , %

*   布尔运算

    *   运算符： and , or

    *   一元运算： ! , not

*   比较运算
    *   比较： > , < , >= , <= ( gt , lt , ge , le )等式: == , != ( eq , ne )
*   条件运算
    *   If-then： (if) ? (then)
    *   If-then-else： (if) ? (then) ： (else)
    *   Default: (value) ?： (defaultvalue)

## 2. 使用thymeleaf

*   引入启动器

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    
    ```

自动配好的策略：

1.   所有thymeleaf的配置值都在 ThymeleafProperties
2.   配置好了 SpringTemplateEngine
3.   配好了 ThymeleafViewResolver
4.   我们只需要直接开发页面

*   使用Thymeleaf，必须引入Thymeleaf命名空间：

    ```html
    <html lang="zh-CN" xmlns:th="http://www.thymeleaf.org">
    ```

*   编写Controller

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
    
        @RequestMapping("aa/{userId}/mm/{num}")
        public String testPathVariable(@PathVariable("userId") String userId, @PathVariable("num") Integer num) {
            System.out.println(userId);
            System.out.println(num);
            return "hello";
        }
    
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
    
        /**
         * 在同步情况下或者表单的实体内容
         * @param data
         * @return
         */
        @RequestMapping("/getData")
        public String getData(@RequestBody String data) {
            return data;
        }
    
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
    
        //test/matrix/color=red,green,blue
        @RequestMapping("test/matrix/{param}")
        public @ResponseBody String testMatrix(@MatrixVariable(pathVar = "param", value = "color") String[] strs) {
            for (String str : strs) {
                System.out.println(str);
            }
            return "hello";
        }
    }
    ```

*   编写html

    ```html
    <!DOCTYPE html>
    <!--引入thymeleaf的命名空间，只有引入了这个域名，才可以使用thymeleaf的表达式-->
    <html lang="zh-CN" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
        <h3>这是一个成功页面...</h3>
        <span th:text = "${msg}"></span>
        <ul>
            <li th:each = "str: ${list}" th:text = "${str}"></li>
        </ul>
    </body>
    </html>
    ```

## 3. 搭建thymeleaf案例--web后台管理案例

*   编写控制器代码

    ```java
    package com.sonnet.controller;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PostMapping;
    
    @Controller
    public class LoginController {
    
        // 跳转到登录页面
        @GetMapping({"/", "/login"})
        public String loginPage() {
            return "login";
        }
    
        // 登录，用户登录成功后，要跳转到主页（重定向， 避免表单的重复提交）
        @PostMapping("/login")
        public String login() {
            return "redirect:/main.html";
        }
    
        // 跳转到主页的控制器方法
        @GetMapping("/main.html")
        public String mainPage() {
            return "main";
        }
    }
    ```

*   完善登录逻辑

    ```java
    package com.sonnet.controller;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.GetMapping;
    
    @Controller
    public class TableController {
    
        // 跳转到basic_table.html
        @GetMapping("/basic_table")
        public String basic_table() {
            return "table/basic_table";
        }
    
        // 跳转到dynamic_table.html
        @GetMapping("/dynamic_table")
        public String dynamic_table() {
            return "table/dynamic_table";
        }
    
        // 跳转到response_table.html
        @GetMapping("/responsive_table")
        public String responsive_table() {
            return "table/responsive_table";
        }
    
        // 跳转到editable_table.html
        @GetMapping("/editable_table")
        public String editable_table() {
            return "table/editable_table";
        }
    }
    ```

    ```java
    package com.sonnet.controller;
    
    import com.sonnet.pojo.User;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PostMapping;
    
    import javax.servlet.http.HttpSession;
    
    @Controller
    public class LoginController {
    
        // 跳转到登录页面
        @GetMapping({"/", "/login"})
        public String loginPage() {
            return "login";
        }
    
        // 登录，用户登录成功后，要跳转到主页（重定向， 避免表单的重复提交）
        @PostMapping("/login")
        public String login(User user, HttpSession session, Model model) {
            // 只要用户名为admin 密码为123456 就可以认证成功
            if (user.getUserName().equals("admin") && user.getPassword().equals("123456")) {
                // 登录成功
                session.setAttribute("loginUser", user);
                return "redirect:/main.html";
            } else {
                // 登录不成功 转发到登录页面
                model.addAttribute("msg", "用户名输入错误");
                return "login";
            }
        }
    
        // 跳转到主页的控制器方法
        @GetMapping("/main.html")
        public String mainPage(HttpSession session, Model model) {
            // 避免用户直接访问主页
            Object loginUser = session.getAttribute("loginUser");
            if (loginUser != null) {
                return "main";
            } else {
                // 转发到我们的登录页面
                model.addAttribute("msg", "请先登录");
                return "login";
            }
        }
    }
    ```

### 3.1 拦截器

*   定义拦截器

    ```java
    package com.sonnet.controller;
    
    import com.sonnet.pojo.User;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PostMapping;
    
    import javax.servlet.http.HttpSession;
    
    @Controller
    public class LoginController {
    
        // 跳转到登录页面
        @GetMapping({"/", "/login"})
        public String loginPage() {
            return "login";
        }
    
        // 登录，用户登录成功后，要跳转到主页（重定向， 避免表单的重复提交）
        @PostMapping("/login")
        public String login(User user, HttpSession session, Model model) {
            // 只要用户名为admin 密码为123456 就可以认证成功
            if (user.getUserName().equals("admin") && user.getPassword().equals("123456")) {
                // 登录成功
                session.setAttribute("loginUser", user);
                return "redirect:/main.html";
            } else {
                // 登录不成功 转发到登录页面
                model.addAttribute("msg", "用户名输入错误");
                return "login";
            }
        }
    
        // 跳转到主页的控制器方法
        @GetMapping("/main.html")
        public String mainPage(HttpSession session, Model model) {
    /*
            // 避免用户直接访问主页
            Object loginUser = session.getAttribute("loginUser");
            if (loginUser != null) {
                return "main";
            } else {
                // 转发到我们的登录页面
                model.addAttribute("msg", "请先登录");
                return "login";
            }
    */
            return "main";
        }
    }
    ```

*   注册拦截器

    ```java
    package com.sonnet.config;
    
    import com.sonnet.interceptor.LoginInterceptor;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
    import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
    
    @Configuration
    public class MyConfig implements WebMvcConfigurer {
    
        // 注册拦截器
        @Override
        public void addInterceptors(InterceptorRegistry registry) {
    
            // addInterceptor 注册拦截器的方法
            // addPathPatterns 拦截器需要拦截哪些请求
            // excludePathPatterns 设置哪些资源不能拦截
            registry.addInterceptor(new LoginInterceptor()).addPathPatterns("/**").
                    excludePathPatterns("/login", "/", "/css/**", "/js/**", "/images/**", "/fonts/**", "/fonts/**", "/images/**", "/js/**");
        }
    }
    ```

*   修改跳转到主页的逻辑

    ```java
        // 跳转到主页的控制器方法
        @GetMapping("/main.html")
        public String mainPage(HttpSession session, Model model) {
    /*
            // 避免用户直接访问主页
            Object loginUser = session.getAttribute("loginUser");
            if (loginUser != null) {
                return "main";
            } else {
                // 转发到我们的登录页面
                model.addAttribute("msg", "请先登录");
                return "login";
            }
    */
            return "main";
        }
    ```


### 3.2 文件管理

*   文件上传

    ```java
    package com.sonnet.controller;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.multipart.MultipartFile;
    
    import java.io.File;
    import java.nio.file.Files;
    
    @Controller
    public class FileUploadController {
    
        // 跳转到文件上传页面
        @GetMapping("/form_layouts")
        public String formLayouts() {
            return "form/form_layouts";
        }
    
        // 进行文件上传，如果是多文件上传，使用MultipartFile[]
        @PostMapping("/upload")
        public String upload(String email, String username, MultipartFile headerImg, MultipartFile[] photos) throws Exception {
            File uploadDirectory = new File("D:\\upload");
            Files.createDirectories(uploadDirectory.toPath());
    
            // 单文件上传
            if (headerImg != null && !headerImg.isEmpty()) {
                // 获取原始文件的名称
                String originalFilename = new File(headerImg.getOriginalFilename()).getName();
                // 执行文件的上传的操作
                headerImg.transferTo(new File(uploadDirectory, originalFilename));
            }
    
            // 多文件上传
            if (photos != null) {
                for (MultipartFile photo : photos) {
                    if (photo != null && !photo.isEmpty()) {
                        String originalFilename = new File(photo.getOriginalFilename()).getName();
                        photo.transferTo(new File(uploadDirectory, originalFilename));
                    }
                }
            }
            return "main";
        }
    }
    ```

### 3.3 异常处理

### 3.4 在springboot中使用原生的web组件

*   使用ServletRegistrationBean 注册Servlet组件

    ```java
    @Configuration
    public class MyConfig {
    
        // 注册Servlet到springboot中
        @Bean
        public ServletRegistrationBean<ServletDemo1> servletRegistrationBean() {
            return new ServletRegistrationBean<>(new ServletDemo1(), "/demo1");
        }
    }
    ```

*   注册Filter

    ```java
    package com.sonnet.filter;
    
    import javax.servlet.*;
    import java.io.IOException;
    
    public class MyFilter implements Filter {
    
        // 执行过滤业务的方法
        @Override
        public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
            System.out.println("filter过滤器方法执行了...");
            filterChain.doFilter(servletRequest, servletResponse);
        }
    }
    ```

*   ```java
    // 注册filter到springboot中
    @Bean
    public FilterRegistrationBean filterRegistrationBean() {
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
        filterRegistrationBean.setFilter(new MyFilter());
        filterRegistrationBean.setUrlPatterns(Arrays.asList("/demo1"));
        return filterRegistrationBean;
    }
    ```

*   注册Listen组件

    ```java
    package com.sonnet.listener;
    
    import javax.servlet.ServletContextEvent;
    import javax.servlet.ServletContextListener;
    
    public class MyListener implements ServletContextListener {
    
        // 监听servlet容器创建的方法
        @Override
        public void contextInitialized(ServletContextEvent sce) {
            System.out.println("ServletContext对象创建了");
        }
    
        // 监听servlet容器销毁的方法
        @Override
        public void contextDestroyed(ServletContextEvent sce) {
            System.out.println("ServletContext对象销毁了");
        }
    }
    ```

    ```java
    // 注册listener到springboot中
    @Bean
    public ServletListenerRegistrationBean servletListenerRegistrationBean() {
        ServletListenerRegistrationBean<MyListener> servletListenerRegistrationBean = new ServletListenerRegistrationBean<>(new MyListener());
        return servletListenerRegistrationBean;
    }
    ```