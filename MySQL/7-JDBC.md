# `JDBC`

## 1. `JDBC`

### 1.1  `JDBC API`

#### 1.1.1  `Driver`

`java.sql.Driver`：数据库厂商提供的`JDBC`驱动包中必须包含该接口的实现，该接口中就包含连接数据库的功能

```java
// 根据给定的数据库url地址连接数据库
Connection connect(String url, java.util.Properties info) throws SQLException;
```

#### 1.1.2 `DriverManager`

`java.sql.DriverManager`：数据库厂商提供的`JDBC`驱动交给`DriverManager`来管理，`DriverManager`主要负责获取数据库连接对象`Connection`

```java
// 通过给定的账号、密码和数据库来获取一个连接
public static Connection getConnection(String url, String user, String password) throws SQLException;
```

#### 1.1.3 `Connection`

`java.sql.Connection`：连接接口，数据库厂商提供的`JDBC`驱动包中必须包含该接口的实现，该接口主要提供与数据库的交互功能

```java
// 创建一个SQL语句执行对象
Statement createStatement() throws SQLException;
// 创建一个预处理SQL语句执行对象
PreparedStatement prepareStatement(String sql) throws SQLException;
// 创建一个存储过程SQL语句执行对象
CallableStatement prepareCall(String sql) throws SQLException;
// 设置连接上的所有操作是否执行自动提交
void setAutoCommit(boolean autoCommit) throws SQLException;
// 提交该连接上至上次提交以来所作出的所有改动
void commit() throws SQLException;
// 回滚事务，数据库回滚到原来的状态
void rollback() throws SQLException;
// 关闭连接
void close() throws SQLException;
// 设置事务隔离级别
void setTransactionIsolation(int level) throws SQLException;
```

```java
// 不支持事务
int TRANSACTION_NONE = 0;
// 读取未提交的数据
int TRANSACTION_RAED_UNCOMMITTED = 1;
// 读取已提交的数据
int TRANSACTION_READ_COMMITED = 2;
// 可重复读
int TRANSACTION_REPEATABLE_READ = 4;
// 串行化
int TRANSACTION_SERIALIZABLE = 8;
```

#### 1.1.4 `Statement`

`java.sql.Statement`：SQL语句执行接口，数据库厂商提供的`JDBC`驱动包中必须包含该接口的实现，该接口主要提供执行`SQL`语句的功能

```java
// 执行查询，得到一个结果集
ResultSet executeQuery(String sql) throws SQLException;
// 执行更新，得到受影响的行数
int executeUpdate(String sql) throws SQLException;
// 关闭SQL语句执行器
void close() throws SQLException;
// 将SQL语句添加到批处理执行SQL列表中
void addBatch(String sql) throws SQLException;
// 执行批处理，返回列表中每一条SQL语句的执行结果
int[] executeBatch() throws SQLException;
```

#### 1.1.5 `ResultSet`

`java.sql.ResultSet`：查询结果集接口，数据厂商提供的`JDBC`驱动包中必须包含该类接口的实现，该接口主要提供查询结果的获取功能

```java
// 光标从当前位置（默认位置为0）向前移动一行，如果存在数据，则返回true，否则返回false
boolean next() throws SQLException;
// 关闭结果集
void close() throws SQLException;
// 获取指定列的字符串值
String getString(int columnIndex) throws SQLExeption;
// 获取指定列的布尔值
boolean getBoolean(int columnIndex) throws SQLExeption;
// 获取指定列的整数值
int getInt(int columnIndex) throws SQLException;
// 获取指定列的对象
Object getObject(int columnIndex, Class type) throws SQLException;
// 获取结果集元数据：查询结果的列名称、列数量、列别名等等
ResultSetMetaData getMetaData() throws SQLException;
// 光标从当前位置（默认位置为0）向后移动一行，如果存在数据，则返回true，否则返回false
```



### 1.2 `JDBC`操作步骤

#### 1.2.1 引入驱动包

新建工程后，将`mysql-connector-java.jar`引入工程中

#### 1.2.2 加载驱动

```java
// mysql 5.0
Class.forName("com.mysql.jdbc.Driver");
// mysql 8.0
Class.forName("com.mysql.cj.jdbc.Driver");
```

#### 1.2.3 获取连接

```java
Connection connection = DriverManager.getConnection(url, username, password);
```

#### 1.2.4 创建`SQL`语句执行器

```java
Statement statement = connection.createStatement();
```

#### 1.2.5 执行`SQL`语句

```java
// 查询
ResultSet rs = statement.executeQuery(sql);
while (rs.next()) {
    // 获取列信息
}

// 更新
int affecteRows = statement.executeUpdate();
```

#### 1.2.6 释放资源

```java
resultSet.close();
statement.close();
connection.close();
```

#### 1.2.7 示例

<font color = "blue">查询</font>

```java
package com.lullaby.jdbc;

import com.mysql.cj.result.BufferedRowList;

import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class JDBCTest {

    public static void main(String[] args) {
        // jdbc: 使用jdbc连接技术
        // http://www.baidu.com
        // mysql://localhost:3306 使用的是MySQL数据库协议，访问的是本地计算机端口3306
        String url = "jdbc:mysql://localhost:3306/lesson?serverTimezone=Asia/Shanghai";
        String username = "root";
        String password = "root";
        List<Account> accountList = new ArrayList<>();
        // mysql80
        try {
            // 加载驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            // 获取连接
            Connection connection = DriverManager.getConnection(url, username, password);
            // 在连接上创建SQL语句执行器
            Statement statement = connection.createStatement();
            String sql = "SELECT account, balance, state FROM account";
            // 使用执行器执行查询，得到一个结果集
            ResultSet resultSet = statement.executeQuery(sql);
            while (resultSet.next()) {  // 光标移动
                // 通过列名称获取列的值
                String account = resultSet.getString("account");
                double balance = resultSet.getDouble(2);
                int state = resultSet.getInt("state");
                Account a = new Account(account, balance, state);
                accountList.add(a);
            }
            resultSet.close();
            statement.close();
            connection.close();
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
        accountList.forEach(System.out::println);
    }
}
```

<font color = "blue">更新</font>

```java
package com.lullaby.jdbc;

import com.mysql.cj.result.BufferedRowList;

import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class JDBCTest {

    public static void main(String[] args) {
        // jdbc: 使用jdbc连接技术
        // http://www.baidu.com
        // mysql://localhost:3306 使用的是MySQL数据库协议，访问的是本地计算机端口3306
        String url = "jdbc:mysql://localhost:3306/lesson?serverTimezone=Asia/Shanghai";
        String username = "root";
        String password = "root";
        List<Account> accountList = new ArrayList<>();
        // mysql80
        try {
            // 加载驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            // 获取连接
            Connection connection = DriverManager.getConnection(url, username, password);
            // 在连接上创建SQL语句执行器
            Statement statement = connection.createStatement();
//            String sql = "SELECT account, balance, state FROM account";
//            // 使用执行器执行查询，得到一个结果集
//            ResultSet resultSet = statement.executeQuery(sql);
//            while (resultSet.next()) {  // 光标移动
//                // 通过列名称获取列的值
//                String account = resultSet.getString("account");
//                double balance = resultSet.getDouble(2);
//                int state = resultSet.getInt("state");
//                Account a = new Account(account, balance, state);
//                accountList.add(a);
//            }
//            resultSet.close();
            String updateSql = "UPDATE account SET balance = balance + 2000 WHERE account = 123456";
            // 执行更新时返回的都是收影响的行数
            int affectedRows = statement.executeUpdate(updateSql);
            System.out.println(affectedRows);
            statement.close();
            connection.close();
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
        accountList.forEach(System.out::println);
    }
}
```



### 1.3 预处理`SQL`

```java
package com.lullaby.jdbc;

import java.sql.*;
import java.util.Scanner;

public class PreparedStatementTest {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入商品名称:");
        String goodsName = scanner.nextLine();
        goodsName = "'" + goodsName + "'";

        String url = "jdbc:mysql://localhost:3306/lesson?serverTimezone=Asia/Shanghai";
        String username = "root";
        String password = "root";
        try {
            // 加载驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            // 获取连接
            Connection connection = DriverManager.getConnection(url, username, password);
            // 在连接上创建SQL语句执行器
            Statement statement = connection.createStatement();
            // 输入： 小米10' or 1='1
            // 输入结果："SELECT id, name, number, price, agent_id FROM goods WHERE name =" + "'" + 小米10' or 1='1 + "'" + " LIMIT 0, 20"
            // SQL注入
            String sql = "SELECT id, name, number, price, agent_id FROM goods WHERE name =" + goodsName + " LIMIT 0, 20";
            // 使用执行器执行查询，得到一个结果集
            ResultSet resultSet = statement.executeQuery(sql);
            while (resultSet.next()) {
                long id = resultSet.getLong("id");
                String name = resultSet.getString("name");
                int number = resultSet.getInt("number");
                double price = resultSet.getDouble("price");
                long agentId = resultSet.getLong("agent_id");
                System.out.println(id + ", " + name + ", " + number + ", " + price + ", " + agentId);
            }
            resultSet.close();
            statement.close();
            connection.close();
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```

当输入为：`小米10' or 1='1`时，明显查询的结果发生了变化，这样的情况被称为`SQL`注入。为了防止`SQL`注入，Java提供了`PreparedStatement`接口对`SQL`进行预处理，该接口是`Statement`接口的子接口，其常用方法如下：

```java
// 执行查询，得到一个结果集
ResultSet executeQUery() throws SQLException;
// 执行更新，得到受影响的行数
int executeUpdate() throws SQLException;
// 使用给定的整数设置给定位置的参数
void setInt(int parameterIndex, int x) throws SQLException;
// 使用给定的长整数设置给定位置的参数
void setLong(int parameterIndex, Long x) throws SQLException;
// 使用给定的双精度浮点数值设置给定位置的参数
void setDouble(int parameterIndex, double x) throws SQLException;
// 使用给定的字符串值设置给定位置的参数
void setString(int parameterIndex, String x) throws SQLException;
// 使用给定的对象设置给定位置的参数
void setObject(int parameterIndex, Object x) throws SQLException;
// 获取结果集元数据
ResultSetMetaData getMetaData() throws SQLException;
```

获取`PreparedStatement`接口对象：

```java
PreparedStatement ps = connection.preparedStatement(sql);
```

`PreparedStatement`进行预处理的方式：

使用`PreparedStatement`时，`SQL`语句中的参数一律使用`?`号来进行占位，然后通过调用`setXxx（）`方法来对占位的`?`号进行替换/从而将参数作为一个整体进行查询。

上面的示例使用`PreparedStatement`编写`SQL`语句为：

```java
package com.lullaby.jdbc;

import java.sql.*;
import java.util.Scanner;

public class PreparedStatementTest {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入商品名称:");
        String goodsName = scanner.nextLine();

        String url = "jdbc:mysql://localhost:3306/lesson?serverTimezone=Asia/Shanghai";
        String username = "root";
        String password = "root";
        try {
            // 加载驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            // 获取连接
            Connection connection = DriverManager.getConnection(url, username, password);
            String sql = "SELECT id, name, number, price, agent_id FROM goods WHERE name= ? LIMIT 0, 20";
            // 创建预处理执行器
            PreparedStatement preparedStatement = connection.prepareStatement(sql);
            // 设置占位符替换的值
            preparedStatement.setString(1, goodsName);
            // 使用执行器执行查询，得到一个结果集
            ResultSet resultSet = preparedStatement.executeQuery();
            while (resultSet.next()) {
                long id = resultSet.getLong("id");
                String name = resultSet.getString("name");
                int number = resultSet.getInt("number");
                double price = resultSet.getDouble("price");
                long agentId = resultSet.getLong("agent_id");
                System.out.println(id + ", " + name + ", " + number + ", " + price + ", " + agentId);
            }
            resultSet.close();
            preparedStatement.close();
            connection.close();
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }

    }

    private static void sqlInjection() {
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入商品名称:");
        String goodsName = scanner.nextLine();
        goodsName = "'" + goodsName + "'";

        String url = "jdbc:mysql://localhost:3306/lesson?serverTimezone=Asia/Shanghai";
        String username = "root";
        String password = "root";
        try {
            // 加载驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            // 获取连接
            Connection connection = DriverManager.getConnection(url, username, password);
            // 在连接上创建SQL语句执行器
            Statement statement = connection.createStatement();
            // 输入： 小米10' or 1='1
            // 输入结果："SELECT id, name, number, price, agent_id FROM goods WHERE name =" + "'" + 小米10' or 1='1 + "'" + " LIMIT 0, 20"
            // SQL注入
            String sql = "SELECT id, name, number, price, agent_id FROM goods WHERE name =" + goodsName + " LIMIT 0, 20";
            // 使用执行器执行查询，得到一个结果集
            ResultSet resultSet = statement.executeQuery(sql);
            while (resultSet.next()) {
                long id = resultSet.getLong("id");
                String name = resultSet.getString("name");
                int number = resultSet.getInt("number");
                double price = resultSet.getDouble("price");
                long agentId = resultSet.getLong("agent_id");
                System.out.println(id + ", " + name + ", " + number + ", " + price + ", " + agentId);
            }
            resultSet.close();
            statement.close();
            connection.close();
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```



## 2. 反射

### 2.1 Class类

```java
public class Class {
    
    private String name;	// 类名
    
    private Package pk;		// 包名
    
    private Constructor[] constructor;	// 构造方法，因为可能存在多个，所以使用数组
    
    private Field[] fields;	// 字段，因为可能存在多个，所以使用数组
    
    private Method[] methods;	// 方法，因为可能存在多个，所以使用数组
    
    private class<?> interfaces;	// 实现的接口，因为可能存在多个，所以使用数组
    
    private Class<?> superClass;	// 继承的父类
}
```

获取一个类对应的Class对象：

```java
Class<类名> clazz = 类名.class;
Class<类名> clazz = 对象名.getClass();
Class<类名> clazz = clazz.getSuperClass();
Class clazz = class.forName("类的全限定名");	// 类的全限定名=包名 + “.” + 类名
Class<类名> clazz = 包装类.TYPE;
```

<font color = "blue">示例</font>

```java
package com.lullaby.reflection;

public class Student {

    private String name;

    private int age;


    public Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    /**
     * 获取
     * @return name
     */
    public String getName() {
        return name;
    }

    /**
     * 设置
     * @param name
     */
    public void setName(String name) {
        this.name = name;
    }

    /**
     * 获取
     * @return age
     */
    public int getAge() {
        return age;
    }

    /**
     * 设置
     * @param age
     */
    public void setAge(int age) {
        this.age = age;
    }

    public String toString() {
        return "Student{name = " + name + ", age = " + age + "}";
    }
}
```

```java
package com.lullaby.reflection;

public class ReflectionTest {

    public static void main(String[] args) {
        Class<Student> c1 = Student.class;
        System.out.println(c1.getName());
        Student student = new Student("张三", 20);
        Class<? extends Student> c2 = student.getClass();
        // 获取父类
        Class<? super Student> c3 = c1.getSuperclass();
        System.out.println(c3.getName());
        try {
            Class c4 = Class.forName("com.lullaby.reflection.Student");
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        }
        Class c5 = Integer.TYPE;
        System.out.println(c5.getName());
    }
}
```

<font color = "blue">Class类常用方法</font>

```java
// 获取类中使用public修饰的字符
public Field[] getFields() throws SecurityException;
// 获取类中定义的所有字段
public Field[] getDeclaredFields() throws SecurityExcption;
// 通过给定的字段名获取类中定义的字段
public Field getField(String name) throws NoSuchFieldException, SecurityException;
// 获取类中使用public修饰的方法
public Method[] getMethod() throws SecurityException;
// 获取类中定义的所有方法
public Method[] getDeclaredMethods() throws SecurityException;
// 通过给定的方法名和参数列表类型获取类中定义的方法
public Method getDeclaredMethod(String name, Class<?>···parameterTypes) throws NoSuchMethodException, SecurityException;
// 获取类中使用public修饰的构造方法
public Constructor<?>[] getConstructors() throws SecurityException;
// 通过给定的参数列表类型获取类中定义的构造方法
public Constructor<T> getConstructor(Class<?>···parameterTypes) throws NoSuchMethodException, SecurityException;
// 获取类的全限定名
public String getName();
// 获取类所在的包
public Package getPackage();
// 判断该类是否是基本数据类型
public native boolean isPrimitive();
// 判断该类是否是接口
public native boolean isInterface();
// 判断该类是否是数组
public native boolean isArray();
// 通过类的无参构造创建一个实例
public T newInstance() throws InstantiatonException, IllegalAccessException;
```

<font color = "blue"> 示例：</font>

```java
package com.lullaby.reflection;

public class Student {

    private String name;

    private int age;


    private Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    /**
     * 获取
     * @return name
     */
    public String getName() {
        return name;
    }

    /**
     * 设置
     * @param name
     */
    public void setName(String name) {
        this.name = name;
    }

    /**
     * 获取
     * @return age
     */
    public int getAge() {
        return age;
    }

    /**
     * 设置
     * @param age
     */
    public void setAge(int age) {
        this.age = age;
    }

    public String toString() {
        return "Student{name = " + name + ", age = " + age + "}";
    }
}
```

```java
package com.lullaby.reflection;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.util.Arrays;

public class ReflectionTest {

    public static void main(String[] args) {
        // 构建一个学生对象，并为每一个字段赋值
        Class<Student> clazz = Student.class;
        try {
            Constructor<? extends Student> constructor = clazz.getDeclaredConstructor();
            // Student类中的无参构造方法是私有的,因此需要先修改访问权限
            constructor.setAccessible(true);
            Student student = constructor.newInstance();
            Field nameField = clazz.getDeclaredField("name");
            nameField.setAccessible(true);
            // 给指定的对象中的该字段赋值
            nameField.set(student, "李四");

            Field ageField = clazz.getDeclaredField("age");
            ageField.setAccessible(true);
            ageField.set(student,  20);

            // get name => get + N + name
            String fieldName = nameField.getName();
            String methodName = "get" + fieldName.substring(0, 1).toUpperCase() + fieldName.substring(1);
            Method method = clazz.getDeclaredMethod(methodName);
            method.setAccessible(true);
            String name = (String) method.invoke(student);
            System.out.println(name);

            methodName = "set" + fieldName.substring(0, 1).toUpperCase() + fieldName.substring(1);
            method = clazz.getDeclaredMethod(methodName, nameField.getType());
            method.invoke(student, "李刚");
            System.out.println(student);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static void getMethod() {
        Class<Student> clazz = Student.class;
        Method[] methods = clazz.getDeclaredMethods();
        for (Method method : methods) {
            System.out.print(method.getModifiers() + " ");
            System.out.print(method.getName() + " (");
            Class[] type = method.getParameterTypes();
            for (Class c : type) {
                System.out.print(c.getName() + " ");
            }
            System.out.println(")");
        }
        System.out.println("-------------");
        try {
            Method method = clazz.getDeclaredMethod("setName", String.class);
            System.out.print(method.getModifiers() + " ");
            System.out.print(method.getName() + " (");
            Class[] type = method.getParameterTypes();
            for (Class c : type) {
                System.out.print(c.getName() + " ");
            }
            System.out.println(")");
        } catch (NoSuchMethodException e) {
            throw new RuntimeException(e);
        }
    }

    private static void getField() {
        Class<Student> clazz = Student.class;
        Field[] fields = clazz.getDeclaredFields();
        for (Field field : fields) {
            System.out.print(field.getModifiers() + " ");
            System.out.print(field.getType().getName() + " ");
            System.out.println(field.getName());
        }
        System.out.println("-------------------");
        try {
            Field field = clazz.getDeclaredField("name");
            System.out.print(field.getModifiers() + " ");
            System.out.print(field.getType().getName() + " ");
            System.out.println(field.getName());
        } catch (NoSuchFieldException e) {
            throw new RuntimeException(e);
        }
    }

    private static void getConstructor() {
        Class<Student> clazz = Student.class;
        // 获取在类中定义的构造方法
        Constructor[] constructors = clazz.getConstructors();
        for (Constructor constructor : constructors) {
            System.out.println(constructor.getModifiers());
            String name = constructor.getName();
            Class[] types = constructor.getParameterTypes();
            System.out.println(name + " ");
            System.out.println(Arrays.toString(types));
        }
        System.out.println("---------------");
        constructors = clazz.getConstructors();
        for (Constructor constructor : constructors) {
            System.out.println(constructor.getModifiers());
            String name = constructor.getName();
            Class[] types = constructor.getParameterTypes();
            System.out.println(name + " ");
            System.out.println(Arrays.toString(types));
        }
        System.out.println("------------------------");
        try {
            Constructor constructor = clazz.getConstructor(String.class, int.class);
            System.out.println(constructor.getModifiers());
            String name = constructor.getName();
            Class[] types = constructor.getParameterTypes();
            System.out.println(name + " ");
            System.out.println(Arrays.toString(types));
        } catch (NoSuchMethodException e) {
            throw new RuntimeException(e);
        }
    }

    private static void getClazz() {
        Class<Student> c1 = Student.class;
        System.out.println(c1.getName());
        Student student = new Student("张三", 20);
        Class<? extends Student> c2 = student.getClass();
        // 获取父类
        Class<? super Student> c3 = c1.getSuperclass();
        System.out.println(c3.getName());
        try {
            Class c4 = Class.forName("com.lullaby.reflection.Student");
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        }
        Class c5 = Integer.TYPE;
        System.out.println(c5.getName());
    }
}
```

### 2.2 反射与数据库

数据库中的每一条数据基本都会封装为一个对象，数据库中的每一个字段值都会存储在对象相应的属性中。如果查询结果的每一个字段都与对象中的属性名保持一致，那么可以使用反射来完成万能查询

`JdbcUtil`

```java
package com.lullaby.reflection;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class JdbcUtil {

    private static final String url = "jdbc:mysql://localhost:3306/lesson?serverTimezone=Asia/Shanghai";
    private static final String username = "root";
    private static final String password = "root";

    static {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            throw new RuntimeException("驱动程序未加载", e);
        }
    }

    public static void main(String[] args) {
        String sql = "SELECT id, name, number, price, agent_id agentId FROM goods WHERE name LIKE ? AND  price > ?";
        Object[] params = {"%魅%", 1000};
        List<Goods> goodsList = getQuery(sql, Goods.class, params);
        goodsList.forEach(System.out::println);
        System.out.println("----------------------");
        sql = "SELECT id, name, region_id regionId FROM agent WHERE name LIKE ?";
        params = new Object[]{"%魅%"};
        List<Agent> agentList = getQuery(sql, Agent.class, params);
        agentList.forEach(System.out::println);
    }

    /**
     * 关闭连接，执行器，结果集
     * @param closeables
     */
    public static void close(AutoCloseable...closeables) {
        if (closeables != null && closeables.length > 0) {
            for (AutoCloseable autoCloseable : closeables) {
                if (autoCloseable != null) {
                    try {
                        autoCloseable.close();
                    } catch (Exception e) {
                        throw new RuntimeException(e);
                    }
                }
            }
        }
    }

    public static int update(String sql, Object...params) {
        int result = 0;
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        try {
            connection = DriverManager.getConnection(url, username, password);
            preparedStatement = getPreparedStatement(sql, connection, params);
            result = preparedStatement.executeUpdate();

        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            close(preparedStatement, connection);
        }
        return result;
    }

    private static PreparedStatement getPreparedStatement(String sql, Connection connection, Object... params) throws SQLException {
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        if (params != null && params.length > 0) {
            for (int i = 0; i < params.length; i++) {
                preparedStatement.setObject(i + 1, params[i]);
            }
        }
        return preparedStatement;
    }

    /**
     * 万能查询通过反射实现，必须要保证类中定义字段名与查询结果展示的列名称相一致
     * @param sql
     * @param clazz
     * @param params
     * @return
     * @param <T>
     */
    public static <T> List<T> getQuery(String sql,Class<T> clazz, Object...params) {
        List<T> dataList = new ArrayList<>();
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet set = null;
        try {
            connection = DriverManager.getConnection(url, username, password);
            preparedStatement = getPreparedStatement(sql, connection, params);
            set = preparedStatement.executeQuery();
            while (set.next()) {
                T t = createInstance(clazz, set);
                dataList.add(t);
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            close(set, preparedStatement, connection);
        }
        return dataList;
    }

    private static <T> T createInstance(Class<T> clazz, ResultSet set) throws NoSuchMethodException, InstantiationException, IllegalAccessException, InvocationTargetException {
        // 获取无参构造
        Constructor<T> constructor = clazz.getConstructor();
        // 通过无参构造创建一个对象
        T t = constructor.newInstance();
        // 获取类中定义的字段(包括私有)
        Field[] fields = clazz.getDeclaredFields();
        for (Field field : fields) {
            String fieldName = field.getName();
            // setId => set id => set + I + d
            String methodName = "set" + fieldName.substring(0, 1).toUpperCase() + fieldName.substring(1);
            Method method = clazz.getDeclaredMethod(methodName, field.getType());
            try {
                Object value = set.getObject(fieldName, field.getType());
                method.invoke(t, value);
            } catch (Exception e) {

            }
        }
        return t;
    }

//    public static List<Goods> getGoods() {
//        String url = "jdbc:mysql://localhost:3306/lesson?serverTimezone=Asia/Shanghai";
//        String username = "root";
//        String password = "root";
//        List<Goods> goodsList = new ArrayList<>();
//        try {
//            Class.forName("com.mysql.cj.jdbc.Driver");
//            Connection connection = DriverManager.getConnection(url, username, password);
//            String sql = "SELECT id, name, number, price, agent_id agentId FROM goods WHERE name LIKE ? AND  price > ?";
//            PreparedStatement preparedStatement = connection.prepareStatement(sql);
//            preparedStatement.setString(1, "%魅%");
//            preparedStatement.setDouble(2, 1000.00);
//            ResultSet set = preparedStatement.executeQuery();
//            while (set.next()) {
//                Goods goods = new Goods();
//                goods.setId(set.getLong("id"));
//                goods.setName(set.getString("name"));
//                goods.setNumber(set.getInt("number"));
//                goods.setPrice(set.getDouble("price"));
//                goods.setAgentId(set.getLong("agent_id"));
//                goodsList.add(goods);
//            }
//            set.close();
//            preparedStatement.close();
//            connection.close();
//        } catch (ClassNotFoundException e) {
//            throw new RuntimeException(e);
//        } catch (SQLException e) {
//            throw new RuntimeException(e);
//        }
//        goodsList.forEach(System.out::println);
//        return goodsList;
//    }
//
//    public static List<Agent> getAgents() {
//        String url = "jdbc:mysql://localhost:3306/lesson?serverTimezone=Asia/Shanghai";
//        String username = "root";
//        String password = "root";
//        List<Agent> agentList = new ArrayList<>();
//        try {
//            Class.forName("com.mysql.cj.jdbc.Driver");
//            Connection connection = DriverManager.getConnection(url, username, password);
//            String sql = "SELECT id, name, region_id FROM agent WHERE name LIKE ?";
//            PreparedStatement preparedStatement = connection.prepareStatement(sql);
//            preparedStatement.setString(1, "%魅%");
//            ResultSet set = preparedStatement.executeQuery();
//            while (set.next()) {
//                Agent agent = new Agent();
//                agent.setId(set.getLong("id"));
//                agent.setName(set.getString("name"));
//                agent.setRegionId(set.getInt("region_id"));
//                agentList.add(agent);
//            }
//            set.close();
//            preparedStatement.close();
//            connection.close();
//        } catch (ClassNotFoundException e) {
//            throw new RuntimeException(e);
//        } catch (SQLException e) {
//            throw new RuntimeException(e);
//        }
//        agentList.forEach(System.out::println);
//        return agentList;
//    }
}
```

<font color = "blue"> 改进</font>

```java
package com.lullaby.layer.util;

import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.lang.reflect.Modifier;
import java.sql.*;
import java.util.ArrayList;
import java.util.List;

/**
 * JDBC 数据库操作工具类
 * 封装了数据库连接的获取、资源释放以及通用的增删改查（CRUD）操作。
 */
public class JdbcUtil {

    // 数据库连接配置信息
    private static String url = "jdbc:mysql://localhost:3306/lesson?serverTimezone=Asia/Shanghai";
    private static String username = "root";
    private static String password = "root";

    // 静态代码块：类加载时执行一次，用于注册 MySQL 驱动
    static {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            throw new RuntimeException("数据库驱动加载失败", e);
        }
    }

    /**
     * 获取数据库连接
     *
     * @return Connection 数据库连接对象
     */
    private static Connection getConnection() {
        try {
            return DriverManager.getConnection(url, username, password);
        } catch (SQLException e) {
            throw new RuntimeException("获取数据库连接失败", e);
        }
    }

    /**
     * 安全关闭数据库资源
     * 接收可变参数，适用于关闭 ResultSet, PreparedStatement, Connection 等实现了 AutoCloseable 接口的对象
     *
     * @param autoCloseables 待关闭的资源数组
     */
    private static void close(AutoCloseable... autoCloseables) {
        if (autoCloseables != null && autoCloseables.length > 0) {
            for (AutoCloseable autoCloseable : autoCloseables) {
                // 增加非空判断，防止空指针异常
                if (autoCloseable != null) {
                    try {
                        autoCloseable.close();
                    } catch (Exception e) {
                        // 仅打印堆栈信息，禁止向上抛出异常，防止阻断后续其他资源的关闭
                        e.printStackTrace();
                    }
                }
            }
        }
    }

    /**
     * 构建并初始化 PreparedStatement
     *
     * @param sql        包含占位符（?）的 SQL 语句
     * @param params     替换占位符的具体参数数组
     * @param connection 数据库连接对象
     * @return 预编译的 PreparedStatement 对象
     * @throws SQLException 数据库访问异常
     */
    private static PreparedStatement getPreparedStatement(String sql, Object[] params, Connection connection) throws SQLException {
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        // 动态填充 SQL 占位符参数
        if (params != null && params.length > 0) {
            for (int i = 0; i < params.length; i++) {
                // JDBC 参数索引从 1 开始
                preparedStatement.setObject(i + 1, params[i]);
            }
        }
        return preparedStatement;
    }

    /**
     * 万能查询方法（支持动态对象映射）
     *
     * @param sql    查询的 SQL 语句
     * @param clazz  映射的目标实体类的 Class 对象
     * @param params 替换占位符的参数
     * @param <T>    泛型，代表实体类类型
     * @return 包含实体对象的 List 集合
     */
    public static <T> List<T> query(String sql, Class<T> clazz, Object... params) {
        List<T> dataList = new ArrayList<>();
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
            connection = getConnection();
            preparedStatement = getPreparedStatement(sql, params, connection);
            resultSet = preparedStatement.executeQuery();

            // 遍历结果集中的每一行记录
            while (resultSet.next()) {
                // 通过反射调用无参构造函数，实例化目标对象
                T instance = clazz.getDeclaredConstructor().newInstance();
                // 获取该实体类中声明的所有字段（包含私有属性）
                Field[] fields = clazz.getDeclaredFields();

                for (Field field : fields) {
                    // 性能优化：过滤掉静态字段和常量（如 serialVersionUID），避免无意义的底层异常触发
                    if (Modifier.isStatic(field.getModifiers()) || Modifier.isFinal(field.getModifiers())) {
                        continue;
                    }

                    String fieldName = field.getName();
                    try {
                        // 尝试从结果集中获取与属性同名的列值（利用 JDBC 4.1 的泛型获取机制转换类型）
                        Object value = resultSet.getObject(fieldName, field.getType());

                        // 若数据库中该字段值为非空，则通过 Setter 方法注入对象
                        if (value != null) {
                            // 按照 Java Bean 规范，拼接 Setter 方法名 (例如: name -> setName)
                            String methodName = "set" + fieldName.substring(0, 1).toUpperCase() + fieldName.substring(1);
                            // 获取对应的 Setter 方法实例
                            Method method = clazz.getDeclaredMethod(methodName, field.getType());
                            // 执行方法完成赋值
                            method.invoke(instance, value);
                        }
                    } catch (SQLException | NoSuchMethodException e) {
                        // 预期内异常：SQL 结果集中没有该列，或实体类中没有对应的 Setter 方法
                        // 属于正常现象，静默跳过，继续处理下一个字段
                        continue;
                    } catch (Exception e) {
                        // 预期外异常（如安全管理器拦截、反射执行异常），记录日志供排查
                        e.printStackTrace();
                    }
                }
                // 将组装好的对象加入集合
                dataList.add(instance);
            }
            return dataList;
        } catch (Exception e) {
            throw new RuntimeException("执行查询操作失败，SQL：" + sql, e);
        } finally {
            // 确保资源必然被释放
            close(resultSet, preparedStatement, connection);
        }
    }

    /**
     * 万能更新方法（涵盖 INSERT、UPDATE、DELETE）
     *
     * @param sql    执行更新的 SQL 语句
     * @param params 替换占位符的参数
     * @return 数据库受影响的行数
     */
    public static int update(String sql, Object... params) {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        try {
            connection = getConnection();
            preparedStatement = getPreparedStatement(sql, params, connection);
            // 执行并返回受影响的行数
            return preparedStatement.executeUpdate();
        } catch (SQLException e) {
            throw new RuntimeException("执行更新操作失败，SQL：" + sql, e);
        } finally {
            // 确保发生异常时连接也能被释放，防止连接池泄漏
            close(preparedStatement, connection);
        }
    }
}
```
