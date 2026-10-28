# Mybatis

## 1. mybatis 入门

### 1.1 框架的基本概述

#### 1.1.1 什么是框架

框架就是一套解决方案，不同的框架解决不同的问题。框架帮助我们封装了很多细节，使用开发者使用极简单的方式就可以实现功能，大大的提高了开发效率。

简而言之，框架其实就是某种应用的半成品，就是一组组件，供你选用完成你自己的系统。简单说就是使用别人搭好的舞台，你来做表演。而且，框架一般是成熟的，不断升级的软件。

#### 1.1.2 框架要解决的问题

框架要解决的最重要的一个问题是技术整合的问题，在 J2EE 的 框架中，有着各种各样的技术，不同的 软件企业需要从 J2EE 中选择不同的技术，这就使得软件企业最终的应用依赖于这些技术，技术自身的复杂性和技 术的风险性将会直接对应用造成冲击。而应用是软件企业的核心，是竞争力的关键所在，因此应该将应用自身的设计和具体的实现技术解耦。这样，软件企业的研发将集中在应用的设计上，而不是具体的技术实现，技术实现是应 用的底层支撑，它不应该直接对应用产生影响。

![](imgs/框架.png)

#### 1.1.3 传统JDBC代码分析

```java
public class TestJdbc {
    public static void main(String[] args) {
        try {
            //加载数据库驱动
            Class.forName("com.mysql.jdbc.Driver");
            //创建连接对象
            String url = "jdbc:mysql://192.168.10.137:3306/ssm";
            Connection connection =
                DriverManager.getConnection(url, "root", "Admin123!");
            //准备sql语句
            String sql = "select * from account";
            //准备PreparedStatement对象
            PreparedStatement pst =
                connection.prepareStatement(sql);
            //执行sql语句
            ResultSet rs = pst.executeQuery();
            //遍历结果集
            while(rs.next()){
                Integer id = rs.getInt("id");
                String name = rs.getString("name");
                Double money = rs.getDouble("money");
                System.out.println(id + " " + name + " " +
                                   money);
            }
            //关闭资源
            rs.close();
            pst.close();
            connection.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

问题：

1.   数据库连接创建、释放频繁造成系统资源浪费从而影响系统性能，如果使用数据库连接池可解决此问题。

2.   Sql 语句在代码中硬编码，造成代码不易维护，实际应用sql变化的可能较大，sql变动需要改变 java 代码。
3.   使用 preparedStatement 向占有位符号传参数存在硬编码，因为 sql 语句的where 条件不一定，可能多也可能少，修改 sql 还要修改代码，系统不易维护。

4.   对结果集解析存在硬编码（查询列名），sql 变化导致解析代码变化，系统不易维护，如果能将数据库记录封装成 pojo 对象解析比较方便。

#### 1.1.4 mybatis 基本概述

mybatis 是一个优秀的基于 java 的持久层框架，它内部封装了 jdbc，使开发者只需要关注 sql 语句本身， 而不需要花费精力去处理加载驱动、创建连接、创建statement 等繁杂的过程。

mybatis 通过 xml 或注解的方式将要执行的各种 statement 配置起来，并通过 java对象和 statement 中 sql 的动态参数进行映射生成最终执行的 sql 语句，最后由mybatis 框架执行 sql 并将结果映射为 java 对象并返回。

采用 ORM 思想解决了实体和数据库映射的问题，对 jdbc 进行了封装，屏蔽了 jdbc api 底层访问细节，使我们不用与 jdbc api 打交道，就可以完成对数据库的持久化操作。

### 1.2 mybatis 入门案例环境搭建

1.   导入mybatis相关依赖

2.   创建pojo，将pojo和数据表进行关联依赖  定义接口

3.   创建mybatis的核心配置文件sqlMapConfig.xml

4.   创建接口的映射文件

     ​	接口所在的目录和接口文件所在的目录保持一致

5.   编写测试代码

#### 1.2.1 创建数据库

```sql
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
    `id` int(11) NOT NULL auto_increment,
    `username` varchar(32) NOT NULL COMMENT '用户名称',
    `birthday` datetime default NULL COMMENT '生日',
    `sex` char(1) default NULL COMMENT '性别',
    `address` varchar(256) default NULL COMMENT '地址',
    PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
insert into
`user`(`id`,`username`,`birthday`,`sex`,`address`) values (41,'老王','2018-02-27 17:47:08','男','上海'),(42,'小二王','2018-03-02 15:09:37','女','四川成都'),(43,'小二王','2018-03-04 11:34:34','女','中国上海'),(45,'老刘','2018-03-04  12:04:06','男','北京昌平'),(46,'王','2018-03-07 17:37:26','男','北京'),(48,'小
艾','2018-03-08 11:44:00','女','上海');
DROP TABLE IF EXISTS `account`;
CREATE TABLE `account` (
    `ID` int(11) NOT NULL COMMENT '编号',
    `UID` int(11) default NULL COMMENT '用户编号',
    `MONEY` double default NULL COMMENT '金额',
    PRIMARY KEY (`ID`),
    KEY `FK_Reference_8` (`UID`),
    CONSTRAINT `FK_Reference_8` FOREIGN KEY (`UID`) REFERENCES
    `user` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
insert into `account`(`ID`,`UID`,`MONEY`) values
(1,41,1000),(2,45,1000),(3,41,2000);
DROP TABLE IF EXISTS `role`;
CREATE TABLE `role` (
    `ID` int(11) NOT NULL COMMENT '编号',
    `ROLE_NAME` varchar(30) default NULL COMMENT '角色名称',
    `ROLE_DESC` varchar(60) default NULL COMMENT '角色描述',
    PRIMARY KEY (`ID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
insert into `role`(`ID`,`ROLE_NAME`,`ROLE_DESC`) values
(1,'院长','学院领导'),(2,'总裁','公司老板'),(3,'校长','学校负责人');
DROP TABLE IF EXISTS `user_role`;
CREATE TABLE `user_role` (
    `UID` int(11) NOT NULL COMMENT '用户编号',
    `RID` int(11) NOT NULL COMMENT '角色编号',
    PRIMARY KEY (`UID`,`RID`),
    KEY `FK_Reference_10` (`RID`),
    CONSTRAINT `FK_Reference_10` FOREIGN KEY (`RID`) REFERENCES
    `role` (`ID`),
    CONSTRAINT `FK_Reference_9` FOREIGN KEY (`UID`) REFERENCES
    `user` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
insert into `user_role`(`UID`,`RID`) values (41,1),(45,1),
(41,2);
```

#### 1.2.2 创建maven工程，添加对应坐标

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-bom</artifactId>
            <version>2.26.1</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.19</version>
    </dependency>

    <!--junit单元测试-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.2</version>
        <scope>test</scope>
    </dependency>

    <!--数据库驱动-->
    <dependency>
        <groupId>com.mysql</groupId>
        <artifactId>mysql-connector-j</artifactId>
        <version>9.7.0</version>
    </dependency>

    <!--log4j-->
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-api</artifactId>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

#### 1.2.3 编写实体类

```java
package com.sonnet.pojo;

import java.util.Date;

/**
 * pojo 与数据表user形成映射关系
 * 定义的规则：
 *      1. 实体类的字段和数据表中的名称保持一致
 *      2. 实体类的字段的数据类型必须和数据表中的字段的数据类型一样
 */
public class User {

    private Integer id;
    private String username;
    private Date birthday;
    private String sex;
    private String address;


    public User() {
    }

    public User(Integer id, String username, Date birthday, String sex, String address) {
        this.id = id;
        this.username = username;
        this.birthday = birthday;
        this.sex = sex;
        this.address = address;
    }

    /**
     * 获取
     * @return id
     */
    public Integer getId() {
        return id;
    }

    /**
     * 设置
     * @param id
     */
    public void setId(Integer id) {
        this.id = id;
    }

    /**
     * 获取
     * @return username
     */
    public String getUsername() {
        return username;
    }

    /**
     * 设置
     * @param username
     */
    public void setUsername(String username) {
        this.username = username;
    }

    /**
     * 获取
     * @return birthday
     */
    public Date getBirthday() {
        return birthday;
    }

    /**
     * 设置
     * @param birthday
     */
    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

    /**
     * 获取
     * @return sex
     */
    public String getSex() {
        return sex;
    }

    /**
     * 设置
     * @param sex
     */
    public void setSex(String sex) {
        this.sex = sex;
    }

    /**
     * 获取
     * @return address
     */
    public String getAddress() {
        return address;
    }

    /**
     * 设置
     * @param address
     */
    public void setAddress(String address) {
        this.address = address;
    }

    public String toString() {
        return "User{id = " + id + ", username = " + username + ", birthday = " + birthday + ", sex = " + sex + ", address = " + address + "}";
    }
}
```

#### 1.2.4 编写持久层接口

```java
package com.sonnet.dao;

import com.sonnet.pojo.User;

import java.util.List;

public interface UserDao {

    // 查询所有的用户信息
    public List<User> findAll();
}
```

#### 1.2.5 编写mybatis的核心配置文件 sqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--配置mybatis环境-->
    <environments default="mysql">
        <!--id需要和default保持一致，配置连接MySQL的具体信息-->
        <environment id="mysql">
            <!--配置事务类型JDBC-->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源 POOLED UNPOOLED-->
            <dataSource type="POOLED">
                <!--配置数据库的驱动 url 用户名 密码 -->
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis_lesson"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>

    <!--在核心配置文件中导入，接口的映射文件-->
    <mappers>
        <mapper resource="com/sonnet/dao/UserDao.xml"></mapper>
    </mappers>
</configuration>
```

#### 1.2.6 编写持久层接口对应的映射文件 UserDao.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--
    namespace：命名空间 值必须唯一 一般写接口的权限定名即可
-->
<mapper namespace="com.sonnet.dao.UserDao">
    <!--
        select标签 就是一个执行查询操作的标签
            id：我们要写接口的方法名称
            resultType：描述接口的返回值类型（如果接口的返回值是List Set 集合 ，那这个属性值必须定义为接口的泛型）
    -->
    <select id="findAll" resultType="com.sonnet.pojo.User">
        select * from user
    </select>
</mapper>
```

#### 1.2.7 测试

```java
package com.sonnet;

import com.sonnet.dao.UserDao;
import com.sonnet.pojo.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;
import java.util.List;

public class TestMybatis {

    @Test
    public void test() throws Exception{
        // 加载mybatis的核心配置文件
        InputStream inputStream = Resources.getResourceAsStream("sqlMapConfig.xml");
        // 创建SqlSessionFactoryBuilder对象
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        // 创建SqlSessionFactory对象（构建者设计模式，屏蔽了SqlSessionFactory创建的细节）
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
        // 创建SqlSession对象（工厂模式）
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 通过SqlSession 生成接口的代理对象（动态代理）
        UserDao userDao = sqlSession.getMapper(UserDao.class);
        List<User> userList = userDao.findAll();
        userList.forEach(System.out::println);
        // 关闭资源
        sqlSession.close();
        inputStream.close();
    }
}
```

### 1.3 自定义Mybatis框架（XML）

#### 1.3.1 流程分析

![](imgs/自定义mybatis框架.png)

#### 1.3.2 搭建环境

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-bom</artifactId>
            <version>2.26.1</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>

    <!--junit单元测试-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.2</version>
        <scope>test</scope>
    </dependency>

    <!--数据库驱动-->
    <dependency>
        <groupId>com.mysql</groupId>
        <artifactId>mysql-connector-j</artifactId>
        <version>9.7.0</version>
    </dependency>

    <!--log4j-->
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-api</artifactId>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <scope>runtime</scope>
    </dependency>

    <dependency>
        <groupId>dom4j</groupId>
        <artifactId>dom4j</artifactId>
        <version>1.6.1</version>
    </dependency>
    <dependency>
        <groupId>jaxen</groupId>
        <artifactId>jaxen</artifactId>
        <version>2.0.6</version>
    </dependency>
</dependencies>
```

#### 1.3.3 编码实现

*   编写加载类。使用类加载器加载资源

    ```java
    package com.sonnet.io;
    
    import java.io.InputStream;
    
    public class Resources {
    
        public static InputStream getResourceAsStream(String fileName) {
            // 使用类加载器记载指定的配置文件
            return Resources.class.getClassLoader().getResourceAsStream(fileName);
        }
    }
    ```

*   编写SqlSessionFactoryBuilder类

    作用：加载配置资源，并将配置资源封装成Configuration对象，并且将该资源对象传到工厂对象中。

    ```java
    package com.sonnet.builder;
    
    import com.sonnet.cfg.Configuration;
    import com.sonnet.factory.DefaultSqlSessionFactory;
    import com.sonnet.factory.SqlSessionFactory;
    import com.sonnet.utils.XMLConfigBuilder;
    
    import java.io.InputStream;
    
    public class SqlSessionFactoryBuilder {
    
        // 定义一个build方法
        public SqlSessionFactory build(InputStream in) {
            // 通过加载xml配置文件，生成一个Configuration对象
            Configuration configuration = XMLConfigBuilder.loadConfiguration(in);
            return new DefaultSqlSessionFactory(configuration);
        }
    }
    ```

*   创建Configuration配置类

    ```java
    package com.sonnet.cfg;
    
    import java.util.HashMap;
    import java.util.Map;
    
    public class Configuration {
        private String driver;
        private String url;
        private String username;
        private String password;
    
        private Map<String, Mapper> mappers = new HashMap<String, Mapper>();
    
    
        public Configuration() {
        }
    
        public Configuration(String driver, String url, String username, String password, Map<String, Mapper> mappers) {
            this.driver = driver;
            this.url = url;
            this.username = username;
            this.password = password;
            this.mappers = mappers;
        }
    
        public String getDriver() {
            return driver;
        }
    
        public void setDriver(String driver) {
            this.driver = driver;
        }
    
        public String getUrl() {
            return url;
        }
    
        public void setUrl(String url) {
            this.url = url;
        }
    
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
    
        public Map<String, Mapper> getMappers() {
            return mappers;
        }
    
        public void setMappers(Map<String, Mapper> maps) {
            mappers.putAll(maps);
        }
    }
    ```

*   创建Mapper

    ```java
    package com.sonnet.cfg;
    
    public class Mapper {
    
        private String queryString;
        private String resultType;
    
        public String getQueryString() {
            return queryString;
        }
    
        public void setQueryString(String queryString) {
            this.queryString = queryString;
        }
    
        public String getResultType() {
            return resultType;
        }
    
        public void setResultType(String resultType) {
            this.resultType = resultType;
        }
    }
    ```

*   创建SqlSessionFactory

    ```java
    package com.sonnet.factory;
    
    import com.sonnet.sqlSession.SqlSession;
    
    public interface SqlSessionFactory {
    
        // 获取SqlSession对象
        public SqlSession openSession();
    }
    ```

    ```java
    package com.sonnet.factory;
    
    import com.sonnet.cfg.Configuration;
    import com.sonnet.sqlSession.DefaultSqlSession;
    import com.sonnet.sqlSession.SqlSession;
    
    public class DefaultSqlSessionFactory implements SqlSessionFactory{
    
        private Configuration cfg;
    
        public DefaultSqlSessionFactory(Configuration cfg) {
            this.cfg = cfg;
        }
    
        public SqlSession openSession() {
            return new DefaultSqlSession(cfg);
        }
    }
    ```

*   定义SqlSession对象

    ```java
    package com.sonnet.sqlSession;
    
    public interface SqlSession {
    
        // 获取代理对象的方法
        public <T> T getMapper(Class<T> tClass);
    
        // 释放资源的方法
        public void close();
    }
    ```

    ```java
    package com.sonnet.sqlSession;
    
    import com.sonnet.cfg.Configuration;
    import com.sonnet.proxy.ProxyFactory;
    import com.sonnet.utils.DataSourceUtil;
    
    import java.lang.reflect.Proxy;
    import java.sql.Connection;
    import java.sql.SQLException;
    
    public class DefaultSqlSession implements SqlSession{
    
        private Configuration cfg;
    
        private Connection connection;
    
        public DefaultSqlSession(Configuration cfg) {
            this.cfg = cfg;
            this.connection = DataSourceUtil.getConnection(cfg);
        }
    
        @Override
        public <T> T getMapper(Class<T> tClass) {
            // 使用JDK动态代理技术
            return (T) Proxy.newProxyInstance(tClass.getClassLoader(), new Class[]{tClass}, new ProxyFactory(cfg.getMappers(), connection));
        }
    
        @Override
        public void close() {
            try {
                connection.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
    }
    ```

*   定义ProxyFactory类

    ```java
    package com.sonnet.proxy;
    
    import com.sonnet.cfg.Mapper;
    import com.sonnet.utils.Executor;
    
    import java.lang.reflect.InvocationHandler;
    import java.lang.reflect.Method;
    import java.sql.Connection;
    import java.util.Map;
    
    public class ProxyFactory implements InvocationHandler {
    
        private Map<String, Mapper> mappers;
        private Connection connection;
    
        public ProxyFactory(Map<String, Mapper> mappers, Connection connection) {
            this.mappers = mappers;
            this.connection = connection;
        }
    
        // 增强的方法
        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            // 获取方法名称
            String methodName = method.getName();
            // 获取方法所在类的名称
            String className = method.getDeclaringClass().getName();
            // 拼接key值
            String key = className + "." + methodName;
            // 获取Map集合中的Mapper对象
            Mapper mapper = mappers.get(key);
            if (mapper == null) {
                throw new IllegalAccessException("传入的参数有误");
            }
            return new Executor().selectList(mapper, connection);
        }
    }
    ```

#### 1.3.4 总结

![](imgs/自定义mybatis框架总结.png)



## 2. Mybatis基于代理的CRUD操作

### 2.1 搭建mybatis的基础环境

*   引入依赖

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.apache.logging.log4j</groupId>
                <artifactId>log4j-bom</artifactId>
                <version>2.26.1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    
    <dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.19</version>
        </dependency>
    
        <!--junit单元测试-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
    
        <!--数据库驱动-->
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <version>9.7.0</version>
        </dependency>
    
        <!--log4j-->
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-api</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <scope>runtime</scope>
        </dependency>
    </dependencies>
    ```

*   定义核心配置文件

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
        <environments default="mybatis">
            <environment id="mybatis">
                <transactionManager type="JDBC"></transactionManager>
                <dataSource type="POOLED">
                    <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                    <property name="url" value="jdbc:mysql://localhost:3306/mybatis_lesson"/>
                    <property name="username" value="root"/>
                    <property name="password" value="root"/>
                </dataSource>
            </environment>
        </environments>
    
        <!--引入映射文件-->
        <mappers>
            <mapper resource="com/sonnet/dao/UserDao.xml"></mapper>
        </mappers>
    </configuration>
    ```

*   定义实体类

    ```java
    package com.sonnet.pojo;
    
    import java.util.Date;
    
    public class User {
    
        private Integer id;
        private String username;
        private Date birthday;
        private String sex;
        private String address;
    
    
        public User() {
        }
    
        public User(Integer id, String username, Date birthday, String sex, String address) {
            this.id = id;
            this.username = username;
            this.birthday = birthday;
            this.sex = sex;
            this.address = address;
        }
    
        /**
         * 获取
         * @return id
         */
        public Integer getId() {
            return id;
        }
    
        /**
         * 设置
         * @param id
         */
        public void setId(Integer id) {
            this.id = id;
        }
    
        /**
         * 获取
         * @return username
         */
        public String getUsername() {
            return username;
        }
    
        /**
         * 设置
         * @param username
         */
        public void setUsername(String username) {
            this.username = username;
        }
    
        /**
         * 获取
         * @return birthday
         */
        public Date getBirthday() {
            return birthday;
        }
    
        /**
         * 设置
         * @param birthday
         */
        public void setBirthday(Date birthday) {
            this.birthday = birthday;
        }
    
        /**
         * 获取
         * @return sex
         */
        public String getSex() {
            return sex;
        }
    
        /**
         * 设置
         * @param sex
         */
        public void setSex(String sex) {
            this.sex = sex;
        }
    
        /**
         * 获取
         * @return address
         */
        public String getAddress() {
            return address;
        }
    
        /**
         * 设置
         * @param address
         */
        public void setAddress(String address) {
            this.address = address;
        }
    
        public String toString() {
            return "User{id = " + id + ", username = " + username + ", birthday = " + birthday + ", sex = " + sex + ", address = " + address + "}";
        }
    }
    ```

*   定义接口

    ```java
    package com.sonnet.dao;
    
    import com.sonnet.pojo.User;
    
    import java.util.List;
    
    public interface UserDao {
    
        public List<User> findAll();
    }
    ```

*   定义mapper映射文件

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    
    <mapper namespace="com.sonnet.dao.UserDao">
        <select id="findAll" resultType="com.sonnet.pojo.User">
            select * from user
        </select>
    </mapper>
    ```

*   测试

    ```java
    package com.sonnet;
    
    import com.sonnet.dao.UserDao;
    import com.sonnet.pojo.User;
    import org.apache.ibatis.io.Resources;
    import org.apache.ibatis.session.SqlSession;
    import org.apache.ibatis.session.SqlSessionFactory;
    import org.apache.ibatis.session.SqlSessionFactoryBuilder;
    import org.junit.Test;
    
    import java.io.InputStream;
    import java.util.List;
    
    public class TestMybatis {
    
        @Test
        public void test01() throws Exception{
            // 加载核心的配置文件
            InputStream inputStream = Resources.getResourceAsStream("sqlMapConfig.xml");
            // 构建SqlSessionFactoryBuilder对象
            SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
            // 构建SqlSessionFactory对象
            SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
            // 生成SqlSession对象
            SqlSession sqlSession = sqlSessionFactory.openSession();
            // 生成接口的代理对象
            UserDao userDao = sqlSession.getMapper(UserDao.class);
            List<User> userList = userDao.findAll();
            userList.forEach(System.out::println);
            sqlSession.close();
            inputStream.close();
        }
    }
    ```

### 2.2 根据id查询

*   定义接口

    ```java
    // 根据id查询用户信息
    public User findUserById(Integer id);
    ```

*   在映射文件中添加配置

    ```xml
    <!--
        根据id查询对应的用户信息
            id：定义接口的名称
            resultType：定义方法的返回值类型
            parameterType：定义接口形参的数据类型
    -->
    <select id="findUserById" resultType="com.sonnet.pojo.User" parameterType="int">
        select * from user where id = #{id}
    </select>
    ```

    >   注意：
    >
    >   sql 语句中使用#{}字符：
    >
    >   它代表占位符，相当于原来 jdbc 部分所学的?，都是用于执行语句时替换实际的数据。
    >
    >   具体的数据是由#{}里面的内容决定的。

*   在测试类中测试

    ```java
    @Test
    public void testFindUserById() throws Exception{
        InputStream in =
            Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactoryBuilder builder = new
            SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = builder.build(in);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserDao userDao = sqlSession.getMapper(UserDao.class);
        User user = userDao.findUserById(41);
        System.out.println(user);
        sqlSession.close();
    }
    ```

    为了避免重复代码，可以通过@After和@Before注解优化

    ```java
    package com.sonnet;
    
    import com.sonnet.dao.UserDao;
    import com.sonnet.pojo.User;
    import org.apache.ibatis.io.Resources;
    import org.apache.ibatis.session.SqlSession;
    import org.apache.ibatis.session.SqlSessionFactory;
    import org.apache.ibatis.session.SqlSessionFactoryBuilder;
    import org.junit.After;
    import org.junit.Before;
    import org.junit.Test;
    
    import java.io.InputStream;
    import java.util.List;
    
    public class TestMybatis {
    
        SqlSession sqlSession;
        UserDao userDao;
    
        @Before // 在@Test方法执行之前执行
        public void before() throws Exception{
            // 加载核心的配置文件
            InputStream inputStream = Resources.getResourceAsStream("sqlMapConfig.xml");
            // 构建SqlSessionFactoryBuilder对象
            SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
            // 构建SqlSessionFactory对象
            SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
            // 生成SqlSession对象
            sqlSession = sqlSessionFactory.openSession();
            // 生成接口的代理对象
            userDao = sqlSession.getMapper(UserDao.class);
    
        }
    
        @After // 在@Test方法执行之后执行
        public void after() throws Exception{
            sqlSession.close();
        }
    
        @Test
        public void test01() throws Exception{
            List<User> userList = userDao.findAll();
            userList.forEach(System.out::println);
        }
    
        @Test
        public void test02() throws Exception{
            User user = userDao.findUserById(48);
            System.out.println(user);
        }
    }
    ```

### 2.3 保存的操作

*   在接口中添加方法

    