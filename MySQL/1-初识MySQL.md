

# 初认数据库

## 1. 数据库基础知识

### 1.1 连接数据库

找到`MySQL`安装目录下的bin目录，然后打开命令窗口，在命轮窗口中按如下语法输入命令：

```tex
mysql -h MySQL数据库服务器的IP地址 -u 用户名 -p
```

```sql
-- MySQL修改root密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
```



### 1.2 `SQL`分类

结构化查询语句，英文名称为Structured Query Language，简称`SQL`。结构化查询语句分为数据定义语言、数据操作语言、数据查询语言和数据控制语言四大类。

| 名称                  | 描述                             | 命令                    |
| --------------------- | -------------------------------- | ----------------------- |
| 数据定义语言（`DDL`） | 数据库、数据表的创建、修改和删除 | CREATE、ALTER、DROP     |
| 数据操作语言（`DML`） | 数据的增加、修改和删除           | INSERT、UPDATE、DELETE  |
| 数据查询语言(`DQL`)   | 数据的查询                       | SELECT                  |
| 数据控制语言(`DCL`)   | 用户授权、事务的提交和回滚       | GRANT、COMMIT、ROLLBACK |



## 2. 数据库的操作

### 2.1 创建数据库的语法

```sql
CREATE DATABASE [IF NOT EXISTS] 数据库名称 DEFAULT CHARACTER SET 字符集 COLLATE 排序规则;
```

<font color = "blue">示例：</font> 创建数据库lesson，并指定字符集为`GBK`，排序规则为`GBK_CHINESE_CI`

```sql
CREATE DATABASE IF NOT EXISTS lesson DEFAULT CHARACTER SET GBK COLLATE GBK_CHINESE_CI;
```

### 2.2 修改数据库的语法

```sql
ALTER DATABASE 数据库名称 CHARACTER SET 字符集 COLLATE 排序规则;
```

<font color = "blue">示例：</font>修改数据库lesson的字符集为`UTF8`，排序规则为`UTF8_GENERAL_CI`

```sql
ALTER DATABASE lesson CHARACTER SET UTF8 COLLATE UTF8_GENERAL_CI;
```

### 2.3 删除数据库的语法

```sql
DROP DATABASE [IF EXISTS] 数据库名称;
```

<font color = "blue"> 示例：</font>删除数据库lesson

```SQL
DROP DATABASE IF EXISTS lesson;
```

### 2.4 查看数据库语法

```sql
SHOW DATABASES;
```

### 2.5 使用数据库的语法

```sql
USE 数据库名称;
```

<font color = "blue">示例：</font>使用数据库lesson

```sql
USE lesson;
```



## 3. 列类型

在`MySQL`中，常用列类型主要分为数值类型、日期时间类型、字符串类型

### 3.1 数值类型

| 类型        | 说明               | 取值范围                                           | 存储需求 |
| ----------- | ------------------ | -------------------------------------------------- | -------- |
| `tinyint`   | 非常小的数据       | 有符号值：-2^7~2^7 - 1 无符号值：0~2^8 - 1         | 1字节    |
| `smallint`  | 较小的数据         | 有符号值：-2^15~2^15 - 1 无符号值：0~2^16 - 1      | 2字节    |
| `mediumint` | 中等大小的数据     | 有符号值：-2^23~2^23 - 1 无符号值：0~2^24 - 1      | 3字节    |
| `int`       | 标准整数           | 有符号值：-2^31~2^31 - 1 无符号值：0~2^32 - 1      | 4字节    |
| `bigint`    | 较大的整数         | 有符号值：-2^63~2^63 - 1 无符号值：0~2^64 - 1      | 8字节    |
| `float`     | 单精度浮点数       | 无符号值：1.1754351 * 10^-38~3.402823466 * 10^38   | 4字节    |
| `double`    | 双精度浮点数       | 无符号值：2.22507385 * 10^-308~1.79769313 * 10^308 | 8字节    |
| `decimal`   | 字符串形式的浮点数 | decimal(m,d)                                       | m个字节  |

### 3.2 日期时间类型

| 类型        | 说明                                   | 取值范围                                                  |
| :---------- | :------------------------------------- | :-------------------------------------------------------- |
| `DATE`      | `YYYY-MM-dd`, 日期格式                 | `1000-01-01 ~ 9999-12-31`                                 |
| `TIME`      | `HH:mm:ss`, 时间格式                   | `-838:59:59.000000 ~ 838:59:59.000000`                    |
| `DATETIME`  | `YYYY-MM-dd HH:mm:ss`                  | `1000-01-01 00:00:00.000000 ~ 9999-12-31 23:59:59.999999` |
| `TIMESTAMP` | `YYYY-MM-dd HH:mm:ss` 格式表示的时间戳 | `1970-01-01 00:00:01.000000 ~ 2038-01-19 03:14:07.999999` |
| `YEAR`      | `YYYY`格式的年份值                     | `1901~2155`                                               |

### 3.3 字符串类型

| 类型            | 说明                                        | 最大长度   |
| :-------------- | :------------------------------------------ | :--------- |
| `char [(M)]`    | 固定长字符串，检索快但费空间，0 <= M <= 255 | M字符      |
| `varchar [(M)]` | 可变字符串0 <= M <= 65535                   | 变长度     |
| `text`          | 文本串                                      | 2^16-1字节 |

### 3.4 列类型修饰属性

| 属性名           | 说明                                                         | 示例                                                         |
| :--------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| `UNSIGNED`       | 无符号，只能用来修饰数值类型，表明该列数据不能出现负数       | `UNSIGNED INT(4)`, 表示只能为4位大于等于0的整数              |
| `ZEROFILL`       | 不足的位数使用0来填充                                        | `INT(4) ZEROFILL`, 如果给定的值为10，此时只有2位，而该列需要4位，不足的2位由0来填充，最终值为0010 |
| `NOT NULL`       | 表示该列类型的值不能为空                                     | `VARCHAR(20) NOT NULL`, 表示该列数据不能为空值               |
| `DEFAULT`        | 表示设置默认值                                               | `INT(4) DEFAULT 0`, 表示该列不赋值时默认为0                  |
| `AUTO_INCREMENT` | 表示自增长，只能应用于数值列类型，该列类型必须为键，且不能为空 | `INT(11) AUTO_INCREMENT NOT NULL PRIMARY KEY`。第一次为该列中插入值时为1，第二次为2 |



## 4. 数据表操作

### 4.1 数据表类型

`MySQL`中的数据表类型有许多，如`MyISAM`、`InnoDB`、`HEAP`、`BOB`、`CSV`等。其中最常用的是`MyISAM`和`InnoDB`



### 4.2 `MyISAM`与`InnoDB`的区别

| 名称       | `MyISAM` | `InnoDB`    |
| ---------- | -------- | ----------- |
| 事务处理   | 不支持   | 支持        |
| 数据行锁定 | 不支持   | 支持        |
| 外键约束   | 不支持   | 支持        |
| 全文索引   | 支持     | 不支持      |
| 表空间大小 | 较小     | 较大，约2倍 |

事务：涉及的所有操作是一个整体，要么都执行，要么都不执行

数据行锁定：一行数据，当一个用户在修改数据时，可以直接将该条数据锁定



如何选择数据表的类型？

```tex
当涉及的业务操作以查询居多，修改和删除较少时，可以使用MyISAM。当涉及的业务操作经常会有修改和删除操作时，使用InnoDB
```



### 4.3 创建数据表

```sql
CREATE TABLE [IF NOT EXISTS] 数据表名称(
	字段名1 列类型(长度) [键/索引] [注释],
    字段名2 列类型(长度) [键/索引] [注释],
    字段名3 列类型(长度) [键/索引] [注释],
    ······
    字段名n 列类型(长度) [键/索引] [注释]
) [ENGINE = 数据表类型][CHARSET=字符集编码] [COMMENT=注释];
```

<font color = "blue">示例：</font>创建学生表，表中有字段学号、姓名、性别、年龄和成绩

```sql
CREATE TABLE IF NOT EXISTS student(
	`number` VARCHAR(30) NOT NULL PRIMARY KEY COMMENT '学号，主键',
	name varchar(30) NOT NULL COMMENT '姓名',
	sex TINYINT(1) UNSIGNED DEFAULT 0 COMMENT '性别：0-男，1-女，2-其他',
	age TINYINT(3) UNSIGNED DEFAULT 0 COMMENT '年龄',
	score DOUBLE(5, 2) UNSIGNED COMMENT '成绩'
)ENGINE=InnoDB CHARSET=UTF8 COMMENT='学生表';
```

### 4.4 修改数据库

* **修改表名**

```sql
ALTER TABLE 表名 RENAME AS 新表名;
```

<font color = "blue">示例：</font>将student表名修改为`stu`

```sql
ALTER TABLE student RENAME AS stu;
```


* **增加字段**

```sql
ALTER TABLE 表名 ADD 字段名 列类型(长度) [键/索引] [注释];
```

<font color = "blue">示例：</font>在`stu`表中添加字段联系电话（phone），列类型为字符串，长度11，非空

```sql
ALTER TABLE stu ADD phone VARCHAR(11) NOT NULL COMMENT '电话';
```

* **查看表结构**

```sql
DESC 表名;
```

* **修改字段**

```sql
-- MODIFY 只能修改字段的修饰属性
ALTER TABLE 表名 MODIFY 字段名 列类型(长度) [修饰属性] [键/索引] [注释];
-- CHANGE 可以修改字段的名字已经修饰属性
ALTER TABLE 表名 CHANGE 字段名 新字段名 列类型(长度) [修饰属性] [键/索引] [注 释];
```

<font color = "blue">注释：</font>将`stu`表中的`sex`字段的类型设置为`VARCHAR`，长度 为2，默认值为男，注释为：性别，男，女，其他

```sql
ALTER TABLE stu MODIFY sex VARCHAR(2) DEFAULT '男' COMMENT '性别，男，女， 其他';
```

<font color = "blue">示例：</font>将`stu`表中`phone`字段修改为`mobile`，属性不变

```sql
ALTER TABLE stu CHANGE phone mobile VARCHAR(11) NOT NULL COMMENT '电话';
```

* **删除字段**

```sql
ALTER TABLE 表名 DROP 字段名;
```

<font color = "blue">示例：</font>删除`stu`中的`mobile`

```sql
ALTER TABLE stu DROP mobile;
```

* **删除数据表**

```sql
DROP TABLE [IF EXISTS] 表名;
```

<font color = "blue">示例：</font>删除数据表`stu`

```sql
DROP TABLE IF EXISTS stu;
```



## 5. 练习

1. 在数据库exercise中创建课程表`stu_course`，包含字段课程编号（number），类型为整数，长度为11，是主键，自增长，非空、课程名称（name），类型为字符串，长度为20，非空、学分（score），类型为浮点数，小数点保留2位有效数字，长度为5，非空

   ```sql
   -- 如果数据库不存在就创建数据库
   CREATE DATABASE IF NOT EXISTS exercise DEFAULT CHARACTER SET UTF8 COLLATE UTF8_GENERAL_CI;
   -- 使用数据库
   USE exercise;
   -- 在数据库中创建数据表stu_course
   CREATE TABLE IF NOT EXISTS stu_course(
   	`number` INT(11) AUTO_INCREMENT PRIMARY KEY NOT NULL COMMENT '课程编号',
   	name varchar(20) NOT NULL COMMENT '课程名称',
   	score DOUBLE(5, 2) NOT NULL COMMENT '学分'
   ) ENGINE=InnoDB CHARSET=UTF8 COMMENT '课程表';
   ```

2. 将课程表重命名为course

   ```sql
   ALTER TABLE stu_course RENAME AS course;
   ```

3. 在课程表中添加字段学时（time），类型为整数，长度为3，非空

   ```sql
   ALTER TABLE course ADD `time` INT(3) NOT NULL COMMENT '学时';
   ```

4. 修改课程表学分类型为浮点数，小数点后面保留1位有效数字，长度为3，非空

   ```sql
   ALTER TABLE course MODIFY score DOUBLE(3, 1) NOT NULL;
   ```

5. 删除课程表

   ```SQL
   DROP TABLE IF EXISTS course;
   ```

6. 删除数据库exercise

   ```sql
   DROP DATABASE IF EXISTS exercise;
   ```

   
