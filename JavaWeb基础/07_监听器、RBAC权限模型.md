# 监听器、`RBAC`权限模型

## 1. 监听器

### 1.1 什么是监听器

监听器顾名思义就是监听某种事件的发生，一旦监听的事件触发，那么监听器就将开始执行。例如：在上课的时候，老师会观察每一位学生的听课情况，如果有学生上课打瞌睡，那么老师就会提醒他。这个场景中，老师就是一个监听器，监听的是学生是否打瞌睡，一旦学生出现打瞌睡的情况，监听器就开始执行（老师提醒学生）

### 1.2` ServletContextListener`

`ServletContextListener` 是 `Servlet` 上下文的监听器，该监听器主要监听的是`Servlet`上下文的初始化和销毁。一旦 `Servlet` 上下文初始化或者销毁`ServletContextListener` 就执行响应的操作。

```java
public interface ServletContextListener extends EventListener {
    //Servlet上下文初始化
    default void contextInitialized(ServletContextEvent sce) {
    }
    //Servlet上下文销毁
    default void contextDestroyed(ServletContextEvent sce) {
    }
}
```

<font color = "blue">示例</font>

```java
package com.sonnet.jsp.listener;

import com.sonnet.jsp.jdbc.JdbcUtil;
import jakarta.servlet.ServletContext;
import jakarta.servlet.ServletContextEvent;
import jakarta.servlet.ServletContextListener;
import jakarta.servlet.annotation.WebListener;

import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

@WebListener // 表明这是一个监听器
public class ApplicationContextListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("Servlet上下文初始化");
        // 获取上下文
        ServletContext context = sce.getServletContext();
        String jdbcConfig = context.getInitParameter("jdbcConfig");
        InputStream is = this.getClass().getResourceAsStream(jdbcConfig);
        Properties properties = new Properties();
        try {
            properties.load(is);
            // 对数据源进行初始化操作
            JdbcUtil.initDataSource(properties);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }


    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("Servlet上下文销毁");
        JdbcUtil.destroyDataSource();
    }
}
```

### 1.3  `DruidDataSource`

`DruidDataSource`是阿里巴巴开发的一款高性能的数据源。利用`Servlet`上下文监听器建立工程中需要的数据源

`web.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--这里以数据库配置为例-->
    <context-param>
        <param-name>jdbcConfig</param-name>
        <param-value>/jdbc.properties</param-value>
    </context-param>
</web-app>
```

`index.jsp`

```jsp
<%--
  Created by IntelliJ IDEA.
  User: sonnet
  Date: 2026/9/11
  Time: 18:27
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>查询</title>
</head>
<body>
    <input type="button" value="查询" id="searchBtn">
</body>
<script type="text/javascript" src="js/jquery-3.1.1.js"></script>
<script type="text/javascript">
    $(function () {
        $("#searchBtn").click(function () {
            $.ajax( {
                url:"search",
                type: "get",
                data: {},
                success: function (resp) {
                    console.log(resp);
                }
            })
        })
    })
</script>
</html>
```

`jdbc.properties`

```properties
druid.url=jdbc:mysql://localhost:3306/javaweb_lesson?serverTimezone=UTC
druid.driverClassName=com.mysql.cj.jdbc.Driver
druid.username=root
druid.password=root
```

`JdbcUtil`

```java
// 声明当前类所在的包
package com.sonnet.jsp.jdbc;

// 导入 Druid 数据源类
import com.alibaba.druid.pool.DruidDataSource;
// 导入自定义的结果集处理器接口
import com.sonnet.jsp.handler.ResultHandler;

// 导入数据库连接接口
import java.sql.Connection;
// 导入预编译 SQL 语句接口
import java.sql.PreparedStatement;
// 导入数据库查询结果集接口
import java.sql.ResultSet;
// 导入 SQL 异常类
import java.sql.SQLException;
// 导入属性配置集合类
import java.util.Properties;

// 定义 JDBC 工具类
public class JdbcUtil {

    // 创建整个应用程序共享的 Druid 数据源对象
    private static final DruidDataSource dataSource = new DruidDataSource();

    /**
     * 初始化数据源。
     *
     * @param properties 数据库和连接池配置
     */
    // 定义初始化数据源的静态方法
    public static void initDataSource(Properties properties) {
        // 将配置文件中的属性设置到 Druid 数据源中
        dataSource.configFromProperties(properties);
    }

    /**
     * 关闭数据源。
     */
    // 定义关闭数据源的静态方法
    public static void destroyDataSource() {
        // 关闭 Druid 数据源并释放连接池资源
        dataSource.close();
    }

    /**
     * 执行通用查询。
     *
     * @param sql 查询 SQL
     * @param handler 结果集处理器
     * @param params SQL 参数
     * @return 查询结果
     * @param <T> 查询结果类型
     */
    // 定义一个可以返回任意类型查询结果的静态泛型方法
    public static <T> T query(String sql, ResultHandler<T> handler, Object...params) {
        // 开始捕获 JDBC 操作可能出现的 SQL 异常
        try {
            // 从 Druid 连接池中获取一个数据库连接
            Connection connection = dataSource.getConnection();
            // 根据传入的 SQL 创建预编译语句对象
            PreparedStatement preparedStatement = connection.prepareStatement(sql);
            // 判断调用者是否传入了 SQL 占位符参数
            if (params != null && params.length > 0) {
                // 遍历所有 SQL 参数
                for (int i = 0; i < params.length; i++) {
                    // 将参数依次设置到 SQL 的问号占位符中，JDBC 参数下标从 1 开始
                    preparedStatement.setObject(i + 1, params[i]);
                }
            }
            // 获取预编译语句执行后产生的查询结果集
            ResultSet resultSet = preparedStatement.executeQuery();
            // 使用调用者传入的处理器将结果集转换为指定类型
            T t = handler.handle(resultSet);
            // 关闭结果集资源
            resultSet.close();;
            // 关闭预编译语句资源
            preparedStatement.close();
            // 关闭连接，将连接归还给 Druid 连接池
            connection.close();
            return t;
        // 捕获数据库操作过程中出现的 SQL 异常
        } catch (SQLException e) {
            // 将受检的 SQL 异常包装成运行时异常并继续抛出
            throw new RuntimeException(e);
        }
    }

    /**
     * 万能更新
     * @param sql
     * @param params
     * @return
     */
    public static int update(String sql, Object...params) {
        Connection connection = null;
        // 开始捕获 JDBC 操作可能出现的 SQL 异常
        try {
            // 从 Druid 连接池中获取一个数据库连接
            connection = dataSource.getConnection();
            // 根据传入的 SQL 创建预编译语句对象
            PreparedStatement preparedStatement = connection.prepareStatement(sql);
            // 判断调用者是否传入了 SQL 占位符参数
            if (params != null && params.length > 0) {
                // 遍历所有 SQL 参数
                for (int i = 0; i < params.length; i++) {
                    // 将参数依次设置到 SQL 的问号占位符中，JDBC 参数下标从 1 开始
                    preparedStatement.setObject(i + 1, params[i]);
                }
            }
            int affectedRows = preparedStatement.executeUpdate();
            connection.commit();
            preparedStatement.close();
            connection.close();
            return affectedRows;
            // 捕获数据库操作过程中出现的 SQL 异常
        } catch (SQLException e) {
            if (connection != null) {
                try {
                    connection.close();
                } catch (SQLException ex) {
                    throw new RuntimeException(ex);
                }
            }
            // 将受检的 SQL 异常包装成运行时异常并继续抛出
            throw new RuntimeException(e);
        }
    }
}
```

`ResultHandler`

```java
package com.sonnet.jsp.handler;

import java.sql.ResultSet;
import java.sql.SQLException;

/**
 * 对查询的结果集进行处理，具体怎么处理需要用户实现
 * @param <T>
 */
public interface ResultHandler<T> {

    T handle(ResultSet resultSet) throws SQLException;
}
```

`SingleHandler`

```java
// 声明当前类所在的包
package com.sonnet.jsp.handler;

// 导入 Apache Commons BeanUtils 工具类
import org.apache.commons.beanutils.BeanUtils;

// 导入数据库查询结果集接口
import java.sql.ResultSet;
// 导入结果集元数据接口
import java.sql.ResultSetMetaData;
// 导入 SQL 异常类
import java.sql.SQLException;
// 导入 HashMap 集合类
import java.util.HashMap;
// 导入 Map 集合接口
import java.util.Map;

// 定义用于处理单条查询结果的结果集处理器
public class SingleHandler<T> implements ResultHandler<T> {

    // 保存需要将查询结果转换成的 JavaBean 类型
    private final Class<T> clazz;

    /**
     * 创建单条结果处理器。
     */
    // 接收需要转换成的 JavaBean 类型
    public SingleHandler(Class<T> clazz) {
        // 将传入的类型保存到成员变量中
        this.clazz = clazz;
    }

    /**
     * 将一条查询结果封装成 JavaBean。
     */
    // 实现 ResultHandler 接口中处理结果集的方法
    @Override
    public T handle(ResultSet resultSet) throws SQLException {
        // 保存最终封装完成的 JavaBean 对象，没有查询结果时保持为 null
        T result = null;
        // 记录结果集中已经读取的数据条数
        int count = 0;

        // 让结果集游标向后移动，并判断当前是否还有一条数据
        while (resultSet.next()) {
            // 每成功读取一条数据，结果数量加一
            count++;
            // 单条结果处理器不允许查询出两条或更多数据
            if (count > 1) {
                // 查询结果超过一条时抛出异常，提醒调用者检查 SQL 条件
                throw new RuntimeException("查询结果存在多条数据：" + count);
            }

            // 捕获创建对象和设置属性时可能出现的异常
            try {
                // 调用无参构造方法创建一个 JavaBean 对象
                T bean = clazz.getDeclaredConstructor().newInstance();
                // 创建 Map，用于保存“列名和列值”的对应关系
                Map<String, Object> values = new HashMap<>();
                // 获取结果集的元数据，通过元数据可以得到列的数量和名称
                ResultSetMetaData metaData = resultSet.getMetaData();
                // 获取本次查询结果的列数
                int columnCount = metaData.getColumnCount();

                // JDBC 的列下标从 1 开始，因此这里从 1 遍历到列数
                for (int columnIndex = 1; columnIndex <= columnCount; columnIndex++) {
                    // 获取当前列的标签，SQL 使用别名时得到的是别名
                    String columnLabel = metaData.getColumnLabel(columnIndex);
                    // 根据当前列的下标获取这一列的数据
                    Object columnValue = resultSet.getObject(columnIndex);
                    // 将列标签作为键、列值作为值存入 Map
                    values.put(columnLabel, columnValue);
                }

                // 根据 Map 中的属性名和属性值，为 JavaBean 调用对应的 setter 方法
                BeanUtils.populate(bean, values);
                // 保存已经封装完成的 JavaBean 对象
                result = bean;
            // 捕获反射创建对象或 BeanUtils 设置属性时产生的异常
            } catch (Exception exception) {
                // 将受检异常包装成运行时异常并继续抛出
                throw new RuntimeException(exception);
            }
        }

        // 返回封装完成的对象；查询不到数据时返回 null
        return result;
    }
}
```

`MultiResultHandler`

```java
package com.sonnet.jsp.handler;

import org.apache.commons.beanutils.BeanUtils;

import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class MultiResultHandler<T> implements  ResultHandler<List<T>>{

    private Class<T> clazz;

    public MultiResultHandler(Class<T> clazz) {
        this.clazz = clazz;
    }

    @Override
    public List<T> handle(ResultSet resultSet) throws SQLException {
        List<T> dataList = new ArrayList<>();
        while (resultSet.next()) {
            try {
                // 调用无参构造方法创建一个 JavaBean 对象
                T bean = clazz.getDeclaredConstructor().newInstance();
                // 创建 Map，用于保存“列名和列值”的对应关系
                Map<String, Object> values = new HashMap<>();
                // 获取结果集的元数据，通过元数据可以得到列的数量和名称
                ResultSetMetaData metaData = resultSet.getMetaData();
                // 获取本次查询结果的列数
                int columnCount = metaData.getColumnCount();

                // JDBC 的列下标从 1 开始，因此这里从 1 遍历到列数
                for (int columnIndex = 1; columnIndex <= columnCount; columnIndex++) {
                    // 获取当前列的标签，SQL 使用别名时得到的是别名
                    String columnLabel = metaData.getColumnLabel(columnIndex);
                    // 根据当前列的下标获取这一列的数据
                    Object columnValue = resultSet.getObject(columnIndex);
                    // 将列标签作为键、列值作为值存入 Map
                    values.put(columnLabel, columnValue);
                }
                // 使用工具类将对我们的对象的属性值进行注入
                BeanUtils.populate(bean, values);
                dataList.add(bean);
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        }
        return dataList;
    }
}
```

`Student`

```java
package com.sonnet.jsp.pojo;

public class Student {

    private Integer id;

    private String name;

    private  String sex;

    private Integer age;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```

`StudentDao`

```java
package com.sonnet.jsp.dao;

import com.sonnet.jsp.pojo.Student;

import java.util.List;

public interface StudentDao {

    List<Student> searchStudents();
}
```

`StudentDaoImpl`

```java
package com.sonnet.jsp.dao.impl;

import com.sonnet.jsp.dao.StudentDao;
import com.sonnet.jsp.handler.MultiResultHandler;
import com.sonnet.jsp.jdbc.JdbcUtil;
import com.sonnet.jsp.pojo.Student;

import java.util.List;

public class StudentDaoImpl implements StudentDao {

    @Override
    public List<Student> searchStudents() {
        String sql = "SELECT id,name,sex,age FROM student";
        return JdbcUtil.query(sql, new MultiResultHandler<>(Student.class));
    }
}
```

`StudentService`

```java
package com.sonnet.jsp.service;

import com.sonnet.jsp.pojo.Student;

import java.util.List;

public interface StudentService {

    List<Student> searchStudents();
}
```

`StudentServiceImpl`

```java
package com.sonnet.jsp.service.impl;

import com.sonnet.jsp.dao.StudentDao;
import com.sonnet.jsp.dao.impl.StudentDaoImpl;
import com.sonnet.jsp.pojo.Student;
import com.sonnet.jsp.service.StudentService;

import java.util.List;

public class StudentServiceImpl implements StudentService {

    private StudentDao studentDao = new StudentDaoImpl();

    @Override
    public List<Student> searchStudents() {
        return studentDao.searchStudents();
    }
}
```

`StudentServlet`

```java
package com.sonnet.jsp.servlet;

import com.alibaba.fastjson.JSONObject;
import com.sonnet.jsp.pojo.Student;
import com.sonnet.jsp.service.impl.StudentServiceImpl;
import com.sonnet.jsp.service.StudentService;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.List;

@WebServlet("/search")
public class SearchServlet extends HttpServlet {

    private StudentService studentService = new StudentServiceImpl();

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("application/json;charset=UTF-8");
        List<Student> students = studentService.searchStudents();
        Object json = JSONObject.toJSON(students);
        PrintWriter writer = resp.getWriter();
        writer.print(json);
        writer.flush();
        writer.close();

    }
}
```

## 2. `RBAC` 权限模型

### 2.1 什么是 `RBAC`

`RBAC`全称为`Role-Based Access Control`，表示基于角色的访问控制。在`RBAC`中，有三个最常用的术语：

*   用户：系统资源的操作者
*   角色：具有一类相同操作权限的用户的总称
*   权限：能够访问资源的资格

*   资源：服务器上的一切数据都是资源，比如静态文件，查询的动态数据等。

`RBAC`的设计主要是控制服务器端的资源访问。

`RBAC`怎么与用户建立联系？服务器感知用户是通过`session`来感知的，因此，`RBAC`的实现需要与`session`配合。前提是用户需要登录，登录后将用户信息存储在`session`中，这样才能在`session`中获取用户的信息

### 2.2` RBAC`简单结构图

![](img/RBAC简单结构图.png)

### 2.3 `RBC` 案例

`StudentDaoImpl`

```java
package com.sonnet.jsp.dao.impl;

import com.sonnet.jsp.dao.StudentDao;
import com.sonnet.jsp.handler.MultiResultHandler;
import com.sonnet.jsp.jdbc.JdbcUtil;
import com.sonnet.jsp.pojo.Student;

import java.util.List;

public class StudentDaoImpl implements StudentDao {

    @Override
    public List<Student> searchStudents() {
        String sql = "SELECT id,name,sex,age FROM student";
        return JdbcUtil.query(sql, new MultiResultHandler<>(Student.class));
    }


    @Override
    public int updateStudent(String id, String name, String sex, String age) {
        String sql = "UPDATE student SET name=?, sex=?, age=? WHERE id=?";
        return JdbcUtil.update(sql, name, sex, age, id);
    }

}
```

`UserDaoImpl`

```java
package com.sonnet.jsp.dao.impl;

import com.sonnet.jsp.dao.UserDao;
import com.sonnet.jsp.handler.SingleHandler;
import com.sonnet.jsp.jdbc.JdbcUtil;
import com.sonnet.jsp.pojo.User;

public class UserDaoImpl implements UserDao {


    @Override
    public User getUserByUsername(String username) {
        String sql = "SELECT username, password, name FROM user WHERE username=?";
        return JdbcUtil.query(sql, new SingleHandler<>(User.class), username);
    }

    @Override
    public Integer getUrlCount(String username, String url) {
        String sql = "SELECT COUNT(*) FROM user_role a INNER JOIN role b ON a.role_id=b.id\n" +
                "INNER JOIN role_permission c ON b.id=c.role_id\n" +
                "INNER JOIN permission d ON c.permission_id=d.id WHERE a.username=? AND d.url=?";

        return JdbcUtil.query(sql, new SingleHandler<>(Integer.class), username, url);
    }

}
```

`SudentDao`

```java
package com.sonnet.jsp.dao;

import com.sonnet.jsp.pojo.Student;

import java.util.List;

public interface StudentDao {

    List<Student> searchStudents();

    int updateStudent(String id, String name, String sex, String age);
}
```

`UserDao`

```java
package com.sonnet.jsp.dao;

import com.sonnet.jsp.pojo.User;

public interface UserDao {

    User getUserByUsername(String username);

    Integer getUrlCount(String username, String url);
}
```

`PermissionFilter`

```java
// 声明当前过滤器所在的包
package com.sonnet.jsp.filter;

// 导入用户业务层接口
import com.sonnet.jsp.service.UserService;
// 导入用户业务层实现类
import com.sonnet.jsp.service.impl.UserServiceImpl;
// 导入过滤器链对象
import jakarta.servlet.FilterChain;
// 导入 Servlet 异常类
import jakarta.servlet.ServletException;
// 导入过滤器注解
import jakarta.servlet.annotation.WebFilter;
// 导入支持 HTTP 请求的过滤器基类
import jakarta.servlet.http.HttpFilter;
// 导入 HTTP 请求对象
import jakarta.servlet.http.HttpServletRequest;
// 导入 HTTP 响应对象
import jakarta.servlet.http.HttpServletResponse;
// 导入 HTTP 会话对象
import jakarta.servlet.http.HttpSession;

// 导入输入输出异常类
import java.io.IOException;
// 导入响应输出流对象
import java.io.PrintWriter;

// 拦截当前 Web 应用中的所有请求
@WebFilter("/*")
// 定义权限过滤器并继承 HTTP 过滤器基类
public class PermissionFilter extends HttpFilter {

    // 创建用户业务对象，用于检查用户访问权限
    private UserService userService = new UserServiceImpl();

    // 重写过滤器的请求处理方法
    @Override
    protected void doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain chain) throws IOException, ServletException {
        // 获取请求的完整 URI
        String requestURI = request.getRequestURI();
        // 去掉项目上下文路径，只保留项目内部的请求路径
        String uri = requestURI.replace(request.getContextPath(), "");
        // 判断当前请求是否属于不需要登录验证的公开资源
        if ("/".equals(uri) || "/login".equals(uri) || uri.endsWith(".jsp") || uri.startsWith("/js")) {
            // 公开资源直接放行给后续过滤器或 Servlet 处理
            chain.doFilter(request, response);
        } else {
            // 获取当前请求对应的会话对象
            HttpSession session = request.getSession();
            // 从会话中获取登录时保存的用户名
            String username = (String) session.getAttribute("username");
            // 判断当前用户是否没有登录或登录会话已经失效
            if (username == null) {
                // 在控制台输出登录超时提示
                System.out.println("登录超时");
            } else {
                // 这里就应该去查询当前登录用户是否应用访问这个url的权限
                // 检查当前用户是否具有访问请求路径的权限
                if (userService.hasPermission(uri, username)) {
                    // 用户具有权限时放行当前请求
                    chain.doFilter(request, response);
                } else {
                    // 设置响应内容的字符编码，避免中文出现乱码
                    response.setCharacterEncoding("UTF-8");
                    // 获取向浏览器输出内容的字符流
                    PrintWriter writer = response.getWriter();
                    // 向浏览器输出无权访问提示
                    writer.print("没有访问权限");
                    // 刷新输出流，确保内容发送给浏览器
                    writer.flush();
                    // 关闭输出流并释放资源
                    writer.close();
                }
            }
        }
    }
}
```

`MultiResultHandler`

```java
package com.sonnet.jsp.handler;

import org.apache.commons.beanutils.BeanUtils;

import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class MultiResultHandler<T> implements  ResultHandler<List<T>>{

    private Class<T> clazz;

    public MultiResultHandler(Class<T> clazz) {
        this.clazz = clazz;
    }

    @Override
    public List<T> handle(ResultSet resultSet) throws SQLException {
        List<T> dataList = new ArrayList<>();
        while (resultSet.next()) {
            try {
                // 调用无参构造方法创建一个 JavaBean 对象
                T bean = clazz.getDeclaredConstructor().newInstance();
                // 创建 Map，用于保存“列名和列值”的对应关系
                Map<String, Object> values = new HashMap<>();
                // 获取结果集的元数据，通过元数据可以得到列的数量和名称
                ResultSetMetaData metaData = resultSet.getMetaData();
                // 获取本次查询结果的列数
                int columnCount = metaData.getColumnCount();

                // JDBC 的列下标从 1 开始，因此这里从 1 遍历到列数
                for (int columnIndex = 1; columnIndex <= columnCount; columnIndex++) {
                    // 获取当前列的标签，SQL 使用别名时得到的是别名
                    String columnLabel = metaData.getColumnLabel(columnIndex);
                    // 根据当前列的下标获取这一列的数据
                    Object columnValue = resultSet.getObject(columnIndex);
                    // 将列标签作为键、列值作为值存入 Map
                    values.put(columnLabel, columnValue);
                }
                // 使用工具类将对我们的对象的属性值进行注入
                BeanUtils.populate(bean, values);
                dataList.add(bean);
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        }
        return dataList;
    }
}
```

`ResultHandler`

```java
package com.sonnet.jsp.handler;

import java.sql.ResultSet;
import java.sql.SQLException;

/**
 * 对查询的结果集进行处理，具体怎么处理需要用户实现
 * @param <T>
 */
public interface ResultHandler<T> {

    T handle(ResultSet resultSet) throws SQLException;
}
```

`SingleHandler`

```java
// 声明当前类所在的包
package com.sonnet.jsp.handler;

// 导入 Apache Commons BeanUtils 工具类
import org.apache.commons.beanutils.BeanUtils;

// 导入数据库查询结果集接口
import java.sql.ResultSet;
// 导入结果集元数据接口
import java.sql.ResultSetMetaData;
// 导入 SQL 异常类
import java.sql.SQLException;
// 导入 HashMap 集合类
import java.util.HashMap;
// 导入 Map 集合接口
import java.util.Map;

// 定义用于处理单条查询结果的结果集处理器
public class SingleHandler<T> implements ResultHandler<T> {

    // 保存需要将查询结果转换成的 JavaBean 类型
    private final Class<T> clazz;

    /**
     * 创建单条结果处理器。
     */
    // 接收需要转换成的 JavaBean 类型
    public SingleHandler(Class<T> clazz) {
        // 将传入的类型保存到成员变量中
        this.clazz = clazz;
    }

    /**
     * 将一条查询结果封装成 JavaBean。
     */
    // 实现 ResultHandler 接口中处理结果集的方法
    @Override
    public T handle(ResultSet resultSet) throws SQLException {
        // 保存最终封装完成的 JavaBean 对象，没有查询结果时保持为 null
        T result = null;
        // 记录结果集中已经读取的数据条数
        int count = 0;

        // 让结果集游标向后移动，并判断当前是否还有一条数据
        while (resultSet.next()) {
            // 每成功读取一条数据，结果数量加一
            count++;
            // 单条结果处理器不允许查询出两条或更多数据
            if (count > 1) {
                // 查询结果超过一条时抛出异常，提醒调用者检查 SQL 条件
                throw new RuntimeException("查询结果存在多条数据：" + count);
            }

            // 捕获创建对象和设置属性时可能出现的异常
            try {
                if (clazz.isPrimitive() || Integer.class == clazz || Long.class == clazz) {
                    return resultSet.getObject(1, clazz);
                }
                // 调用无参构造方法创建一个 JavaBean 对象
                T bean = clazz.getDeclaredConstructor().newInstance();
                // 创建 Map，用于保存“列名和列值”的对应关系
                Map<String, Object> values = new HashMap<>();
                // 获取结果集的元数据，通过元数据可以得到列的数量和名称
                ResultSetMetaData metaData = resultSet.getMetaData();
                // 获取本次查询结果的列数
                int columnCount = metaData.getColumnCount();

                // JDBC 的列下标从 1 开始，因此这里从 1 遍历到列数
                for (int columnIndex = 1; columnIndex <= columnCount; columnIndex++) {
                    // 获取当前列的标签，SQL 使用别名时得到的是别名
                    String columnLabel = metaData.getColumnLabel(columnIndex);
                    // 根据当前列的下标获取这一列的数据
                    Object columnValue = resultSet.getObject(columnIndex);
                    // 将列标签作为键、列值作为值存入 Map
                    values.put(columnLabel, columnValue);
                }

                // 根据 Map 中的属性名和属性值，为 JavaBean 调用对应的 setter 方法
                BeanUtils.populate(bean, values);
                // 保存已经封装完成的 JavaBean 对象
                result = bean;
            // 捕获反射创建对象或 BeanUtils 设置属性时产生的异常
            } catch (Exception exception) {
                // 将受检异常包装成运行时异常并继续抛出
                throw new RuntimeException(exception);
            }
        }

        // 返回封装完成的对象；查询不到数据时返回 null
        return result;
    }
}
```

`JdbcUtil`

```java
// 声明当前类所在的包
package com.sonnet.jsp.jdbc;

// 导入 Druid 数据源类
import com.alibaba.druid.pool.DruidDataSource;
// 导入自定义的结果集处理器接口
import com.sonnet.jsp.handler.ResultHandler;

// 导入数据库连接接口
import java.sql.Connection;
// 导入预编译 SQL 语句接口
import java.sql.PreparedStatement;
// 导入数据库查询结果集接口
import java.sql.ResultSet;
// 导入 SQL 异常类
import java.sql.SQLException;
// 导入属性配置集合类
import java.util.Properties;

// 定义 JDBC 工具类
public class JdbcUtil {

    // 创建整个应用程序共享的 Druid 数据源对象
    private static final DruidDataSource dataSource = new DruidDataSource();

    /**
     * 初始化数据源。
     *
     * @param properties 数据库和连接池配置
     */
    // 定义初始化数据源的静态方法
    public static void initDataSource(Properties properties) {
        // 将配置文件中的属性设置到 Druid 数据源中
        dataSource.configFromProperties(properties);
    }

    /**
     * 关闭数据源。
     */
    // 定义关闭数据源的静态方法
    public static void destroyDataSource() {
        // 关闭 Druid 数据源并释放连接池资源
        dataSource.close();
    }

    /**
     * 执行通用查询。
     *
     * @param sql 查询 SQL
     * @param handler 结果集处理器
     * @param params SQL 参数
     * @return 查询结果
     * @param <T> 查询结果类型
     */
    // 定义一个可以返回任意类型查询结果的静态泛型方法
    public static <T> T query(String sql, ResultHandler<T> handler, Object...params) {
        // 开始捕获 JDBC 操作可能出现的 SQL 异常
        try {
            // 从 Druid 连接池中获取一个数据库连接
            Connection connection = dataSource.getConnection();
            // 根据传入的 SQL 创建预编译语句对象
            PreparedStatement preparedStatement = connection.prepareStatement(sql);
            // 判断调用者是否传入了 SQL 占位符参数
            if (params != null && params.length > 0) {
                // 遍历所有 SQL 参数
                for (int i = 0; i < params.length; i++) {
                    // 将参数依次设置到 SQL 的问号占位符中，JDBC 参数下标从 1 开始
                    preparedStatement.setObject(i + 1, params[i]);
                }
            }
            // 获取预编译语句执行后产生的查询结果集
            ResultSet resultSet = preparedStatement.executeQuery();
            // 使用调用者传入的处理器将结果集转换为指定类型
            T t = handler.handle(resultSet);
            // 关闭结果集资源
            resultSet.close();;
            // 关闭预编译语句资源
            preparedStatement.close();
            // 关闭连接，将连接归还给 Druid 连接池
            connection.close();
            return t;
        // 捕获数据库操作过程中出现的 SQL 异常
        } catch (SQLException e) {
            // 将受检的 SQL 异常包装成运行时异常并继续抛出
            throw new RuntimeException(e);
        }
    }

    /**
     * 万能更新
     * @param sql
     * @param params
     * @return
     */
    public static int update(String sql, Object...params) {
        Connection connection = null;
        // 开始捕获 JDBC 操作可能出现的 SQL 异常
        try {
            // 从 Druid 连接池中获取一个数据库连接
            connection = dataSource.getConnection();
            // 根据传入的 SQL 创建预编译语句对象
            PreparedStatement preparedStatement = connection.prepareStatement(sql);
            // 判断调用者是否传入了 SQL 占位符参数
            if (params != null && params.length > 0) {
                // 遍历所有 SQL 参数
                for (int i = 0; i < params.length; i++) {
                    // 将参数依次设置到 SQL 的问号占位符中，JDBC 参数下标从 1 开始
                    preparedStatement.setObject(i + 1, params[i]);
                }
            }
            int affectedRows = preparedStatement.executeUpdate();
            preparedStatement.close();
            connection.close();
            return affectedRows;
            // 捕获数据库操作过程中出现的 SQL 异常
        } catch (SQLException e) {
            if (connection != null) {
                try {
                    connection.close();
                } catch (SQLException ex) {
                    throw new RuntimeException(ex);
                }
            }
            // 将受检的 SQL 异常包装成运行时异常并继续抛出
            throw new RuntimeException(e);
        }
    }
}
```

`Student`

```java
package com.sonnet.jsp.pojo;

public class Student {

    private Integer id;

    private String name;

    private  String sex;

    private Integer age;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```

`User`

```java
package com.sonnet.jsp.pojo;

public class User {

    private String username;

    private String password;

    private String name;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

`StudentServiceImpl`

```java
package com.sonnet.jsp.service.impl;

import com.sonnet.jsp.dao.StudentDao;
import com.sonnet.jsp.dao.impl.StudentDaoImpl;
import com.sonnet.jsp.pojo.Student;
import com.sonnet.jsp.service.StudentService;

import java.util.List;

public class StudentServiceImpl implements StudentService {

    private StudentDao studentDao = new StudentDaoImpl();

    @Override
    public List<Student> searchStudents() {
        return studentDao.searchStudents();
    }

    @Override
    public int updateStudent(String id, String name, String sex, String age) {
        return studentDao.updateStudent(id, name, sex, age);
    }
}
```

`UserServiceImpl`

```java
package com.sonnet.jsp.service.impl;

import com.sonnet.jsp.dao.UserDao;
import com.sonnet.jsp.dao.impl.UserDaoImpl;
import com.sonnet.jsp.pojo.User;
import com.sonnet.jsp.service.UserService;

public class UserServiceImpl implements UserService {

    private UserDao userDao = new UserDaoImpl();

    @Override
    public int login(String username, String password) {
        User user = userDao.getUserByUsername(username);
        if (user == null) return -1;

        return user.getPassword().equals(password) ? 1 : 0;
    }

    @Override
    public boolean hasPermission(String uri, String username) {
        return userDao.getUrlCount(username, uri) > 0;
    }
}
```

`StudentService`

```java
package com.sonnet.jsp.service;

import com.sonnet.jsp.pojo.Student;

import java.util.List;

public interface StudentService {

    List<Student> searchStudents();

    int updateStudent(String id, String name, String sex, String age);
}
```

`UserService`

```java
package com.sonnet.jsp.service;

public interface UserService {

    int login(String username, String password);

    boolean hasPermission(String uri, String username);
}
```

`LoginServlet`

```java
package com.sonnet.jsp.servlet;

import com.sonnet.jsp.service.UserService;
import com.sonnet.jsp.service.impl.UserServiceImpl;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/login")
public class LoginServlet extends HttpServlet {

    private UserService userService = new UserServiceImpl();

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        int result = userService.login(username, password);
        if (result == 1) {
            req.getSession().setAttribute("username", username);
        }
        PrintWriter writer = resp.getWriter();
        writer.print(result);
        writer.flush();
        writer.close();
    }
}
```

`SearchServlet`

```java
package com.sonnet.jsp.servlet;

import com.alibaba.fastjson.JSONObject;
import com.sonnet.jsp.pojo.Student;
import com.sonnet.jsp.service.impl.StudentServiceImpl;
import com.sonnet.jsp.service.StudentService;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.List;

@WebServlet("/search")
public class SearchServlet extends HttpServlet {

    private StudentService studentService = new StudentServiceImpl();

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("application/json;charset=UTF-8");
        List<Student> students = studentService.searchStudents();
        Object json = JSONObject.toJSON(students);
        PrintWriter writer = resp.getWriter();
        writer.print(json);
        writer.flush();
        writer.close();

    }
}
```

`UpdateServlet`

```java
package com.sonnet.jsp.servlet;

import com.sonnet.jsp.service.StudentService;
import com.sonnet.jsp.service.impl.StudentServiceImpl;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/update")
public class UpdateServlet extends HttpServlet {

    StudentService studentService = new StudentServiceImpl();

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String id = req.getParameter("id");
        String name = req.getParameter("name");
        String sex = req.getParameter("sex");
        String age = req.getParameter("age");
        int result = studentService.updateStudent(id, name, sex, age);
        PrintWriter writer = resp.getWriter();
        writer.print(result);
        writer.flush();
        writer.close();
    }
}
```

`web.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--这里以数据库配置为例-->
    <context-param>
        <param-name>jdbcConfig</param-name>
        <param-value>/jdbc.properties</param-value>
    </context-param>
</web-app>
```

`login.jsp`

```jsp
<%--
  Created by IntelliJ IDEA.
  User: sonnet
  Date: 2026/9/11
  Time: 18:27
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>登录</title>
</head>
<body>
    <input type="text" id="username">
    <input type="text" id="password">
    <input type="button" value="查询" id="searchBtn">
</body>
<script type="text/javascript" src="js/jquery-3.1.1.js"></script>
<script type="text/javascript">
    $(function () {
        $("#searchBtn").click(function () {
            $.ajax( {
                url:"login",
                type: "post",
                contentType: "application/x-www-form-urlencoded;charset=UTF-8",
                data: {
                    username: $("#username").val(),
                    password: $("#password").val()
                },
                success: function (resp) {
                    if (resp === "1") {
                        window.location.href = "main.jsp";
                    } else if (resp == "-1") {
                        alert("账号不存在");
                    } else {
                        alert("账号或密码错误");
                    }
                }
            })
        })
    })
</script>
</html>
```

`main.jsp`

```jsp
<%--
  Created by IntelliJ IDEA.
  User: sonnet
  Date: 2026/9/11
  Time: 18:27
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>主页面</title>
</head>
<body>
    <input type="button" value="查询" id="searchBtn">
    <input type="button" value="修改" id="updateBtn">
</body>
<script type="text/javascript" src="js/jquery-3.1.1.js"></script>
<script type="text/javascript">
    $(function () {
        $("#searchBtn").click(function () {
            $.ajax( {
                url:"search",
                type: "get",
                data: {},
                success: function (resp) {
                    console.log(resp);
                }
            })
        })
    })

    $(function () {
        $("#updateBtn").click(function () {
            $.ajax( {
                url:"update",
                type: "post",
                contentType: "application/x-www-form-urlencoded;charset=UTF-8",
                data: {
                    id: 3,
                    name: "王五",
                    sex: "女",
                    age: 30
                },
                success: function (resp) {
                    alert(resp);
                }
            })
        })
    })
</script>
</html>
```
