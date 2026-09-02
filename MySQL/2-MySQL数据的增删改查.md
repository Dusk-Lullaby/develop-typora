# MySQL数据的增删改查

## 1. DML语句

### 1.1 什么是DML

DML全称为Data Manipulation Languge， 表示数据操作语言。主要体现于对数据表的增删改操作。因此DML仅包括INSERT,UPDATE,DELETE语句。

### 1.2 INSERT语句

```sql
-- 需要注意的是,VALUES后的字段必须与表名后的字段一一对应
INSERT INTO 表名(字段名1，字段名2，···，字段名n) VALUES(字段值1，字段值2，···，字段值n);
-- 需要注意的是，VALUES后的字段值必须与创建表时的字段顺序保持一一对应
INSERT INTO 表名 VALUES(字段值1， 字段值2，···，字段值n);

-- 一次性插入多条数据
INSERT INTO 表名(字段名1，字段名2，···，字段名n) VALUES(字段值1，字段值2，···，字段值n)，(字段值1， 字段值2，···，字段值n)，···，(字段值1， 字段值2，···，字段值n);
INSERT INTO 表名 VALUES (字段值1， 字段值2，···，字段值n),(字段值1， 字段值2，···，字段值n),···，(字段值1， 字段值2，···，字段值n);
```

<font color = "blue">示例：</font>

向课程表中插入数据

```sql
INSERT INTO course (`number`, name, score, `time`) VALUES (1, 'Java基础', 4, 40);
INSERT INTO course VALUES(2, '数据库', 3, 20);
INSERT INTO course (`number`, score, name, `time`) VALUES (3, 5, 'Jsp', 40);
INSERT INTO course (`number`, name, score, `time`) VALUES (4, 'Spring', 4, 5), (5, 'Spring Mvc', 2, 50);
INSERT INTO course VALUES(6, 'SSM', 2, 3), (7, 'Spring Boot', 2, 2);
-- 当列为自增长列或者列有默认值时，可以不给该列赋值
INSERT INTO course (name, score, `time`) VALUES ('HTML', 4, 20);
```

### 1.3 UPDATE语句

```sql
UPDATE 表名 SET 字段名1=字段值1[，字段名2=字段值2，···，字段名n=字段值n] [WHERE 修改条件];
```

#### 1.3.1 WHERE条件字句

在Java中，条件的表示通常都是使用关系运算符来表示，在SQL语句中也是一样，使用>,<,>=,<=,!=来表示。不同的是除此之外，SQL还可以使用SQL专用的关键字来表示条件

在Java中，条件之间的衔接通常都是使用逻辑运算符来表示，在SQL语句也是一样，但通常使用AND来表示逻辑与（&&），使用OR来表示逻辑或（||）

<font color = "blue">示例：</font>

```sql
WHERE time > 20 && time < 40; <=> WHERE time >20 AND time < 40;
```

#### 1.3.2 UPDATE语句

<font color = "blue">示例：</font>

将数据库的学分更改为4，学时更改为15

```sql
UPDATE course SET score=4, `time`=15 WHERE name='数据库';
```

### 1.4 DELETE语句

```sql
DELETE FROM 表名 [WHERE 删除条件];
```

<font color = "blue">示例：</font>

删除课程表中课程编号为1的数据

```sql
DELETE FROM course WHERE `number`=1;
```

### 1.5 TRUNCATE语句

```sql
-- 清空表中数据
TRUNCATE [TABLE] 表名;
```

<font color = "blue">示例：</font>

清空课程表数据

```sql
TRUNCATE course;
TRUNCATE TABLE course;
```

### 1.6 DELETE与TRUNCATE的区别

* DELETE语句根据条件删除表中的数据，而TRUNCATE语句则是将表中的数据全部清空；如果DELETE语句要删除表中的所有数据，那么在效率要低于TRUNCATE语句
* 如果表中有自增长列，TRUNCATE语句则会重置自增长的计数器，但DELETE语句不会
* TRUNCATE语句执行后，数据无法恢复，而DELETE语句执行后，可以使用事务回滚进行恢复



## 2. DQL语句

### 2.1什么是DQL

DQL全称是Data Query Language，表示数据查询语言。体现在数据的查询操作，因此DQL仅包含SELECT语句

### 2.2 SELECT语句

```sql
SELECT ALL/DISTINCT * | 字段名1 AS 别名1[， ···， 字段名n AS 别名n] FROM 表名 WHERE 查询条件
```

<font color = "blue">解释说明</font>

ALL表示查询所有满足条件的记录，可以省略；DISTINCT表示去掉查询结果中重复的记录

AS可以给数据列、数据表取一个别名

<font color = "blue">示例：</font>

从数据表查询编号小于5的课程名称

```sql
SELECT name FROM course WHERE `NUMBER`<5;
```

从课程表中查询课程名称为“Java”的学分和学时

```sql
SELECT score, `time` FROM course WHERE name='Java基础';
```

### 2.3 比较操作符

| 操作符        | 语法                             | 说明                                                         |
| ------------- | -------------------------------- | ------------------------------------------------------------ |
| IS NULL       | 字段名IS NULL                    | 如果字段值为NULL则条件满足                                   |
| IS NOT NULL   | 字段名IS NOT NULL                | 如果字段的值不为NULL则条件满足                               |
| BETWEEN...AND | 字段名 BETWEEN 最小值 AND 最大值 | 如果字段值在最小值与最大值之间（能够取到最小值和最大值），则条件满足 |
| LIKE          | 字段名 LIKE '%匹配内容%'         | 如果字段值包含匹配内容，则条件满足                           |
| IN            | 字段名 IN(值1，值2，···，值n)    | 如果字段值在值1，值2，···，值n中，则条件满足                 |

<font color = "blue">示例</font>

从课程表查询课程名为NULL的课程信息

```sql
SELECT * FROM course WHERE name IS NULL;
```

从课程表查询课程名不为NULL的课程信息

```sql
SELECT * FROM course WHERE name IS NOT NULL;
```

从课程表查询学分在3~5之间的课程信息

```sql
SELECT * FROM course WHERE score BETWEEN 3 AND 5;
-- 假设页面上有2个输入框，表示学分的最小值和最大值，而用户只输入了其中一个框中的值
SELECT * FROM course WHERE score>=3 AND score<=5;
```

从课程表查询课程名包含“V”的课程信息

```sql
SELECT * FROM course WHERE name LIKE '%V%';
```

···

```sql
-- 查询课程名包含ing的课程信息
SELECT * FROM course WHERE name LIKE '%ing%';
-- 查询课程名以ing结尾的课程信息
SELECT * FROM course WHERE name LIKE '%ing';
-- 查询课程名以ing开始的课程信息
SELECT * FROM course WHERE name LIKE 'ing%';
-- 查询课程名以只有3个字符的课程信息
SELECT * FROM course WHERE name LIKE '___';
-- 查询课程名以S开始且只有3个字符的课程信息
SELECT * FROM course WHERE name LIKE 'S__';
-- 查询课程编号为1，3，5的课程信息
SELECT * FROM course WHERE `number` IN(1,3,5);
-- 查询课程编号为1，3，5,10,12的课程信息
SELECT * FROM course WHERE `number` IN(1,3,5,10,12);
```

### 2.4 分组

数据表准备：新建学生表student，包含字段学号（no），类型为长整数，长度为20，是主键，非空；姓名（name），类型为字符串，长度为20，非空；性别（sex），类型为字符串，长度为2，默认为男；年龄（age），类型为整数，长度为3，默认值为0；成绩（score），类型为浮点数，长度为5，小数点后面保留2位有效数字

```sql
CREATE TABLE student(
	`no` BIGINT(20)  AUTO_INCREMENT PRIMARY KEY NOT NULL COMMENT '学号',
	name VARCHAR(20) NOT NULL COMMENT '姓名',
	sex VARCHAR(2) DEFAULT '男' COMMENT '性别',
	age INT(3) DEFAULT 0 COMMENT '年龄',
	score DOUBLE(5, 2) COMMENT '成绩'
) ENGINE=InnoDB CHARSET=UTF8 COMMENT='学生表';
```

插入测试数据：

```sql
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '张三', '男', 20, 59);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '李四', '女', 19, 62);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '王五', '其他', 21, 62);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '龙华', '男', 22, 75);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '金凤', '女', 18, 80);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '张华', '其他', 27, 88);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '李刚', '男', 28, 81);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '潘玉明', '女', 28, 81);
INSERT INTO student(no, name, sex, age, score) VALUES (DEFAULT, '凤飞飞', '其他', 32, 90);
```

#### 2.4.1 分组查询

```sql
SELECT ALL/DISTINCT * | 字段名1 AS 别名1[,字段名2 AS 别名2，···，字段名n AS 别名n] FROM 表名 WHERE 查询条件 GROUP BY 字段名1，字段名2，···，字段名n;
```

分组查询得到的结果只是该组中的第一条数据

<font color = "blue">示例：</font>

从学生表查询成绩在80分以上的学生并按性别分组

```sql
SELECT * FROM student WHERE score>80 GROUP BY sex;
```

从学生表查询成绩在60~80之间的学生并按性别和年龄分组

```sql
SELECT * FROM student WHERE score>=60 AND score<=80 GROUP BY sex, age;
```

#### 2.4.2 聚合函数

* **<font color = "red">COUNT()：</font>**统计满足条件的数据总条数

  <font color = "blue">示例：</font>从学生表查询80分以上的学生人数

  ```sql
  SELECT COUNT(*) FROM student WHERE score>80;
  ```

* **<font color = "red">SUM()：</font>**只能用于数值类型的字段或者表达式，计算该满足条件的字段值的总和

  <font color = "blue">示例：</font>从学生表查询不及格的学生的人数和总成绩

  ```sql
  SELECT COUNT(*) totalCount, SUM(score) totalScore FROM student WHERE score<60;
  ```

* **<font color = "red">AVG()：</font>**只能用于数值类型的字段或者表达式，计算该满足条件的字段值的平均值

  <font color = "blue">示例：</font>从学生表查询男生、女生、其他类型的学生的平均成绩

  ```sql
  SELECT sex, AVG(score) averageScore FROM student GROUP BY sex;
  ```

* **<font color = "red">MAX()：</font>**只能用于查询数值类型的字段或者表达式，计算该满足条件的最大值

  <font color = "blue">示例：</font>从学生表查询学生的最大年龄

  ```sql
  SELECT MAX(age) FROM student;
  ```

* **<font color = "red">MIN()：</font>**只能用于查询数值类型的字段或者表达式，计算该满足条件的最小值

  <font color = blue>示例：</font>从学生表中查询学生的最低分

  ```sql
  SELECT MIN(score) FROM student;
  ```

#### 2.4.3 分组查询结果筛选

```sql
SELECT ALL/DISTINCT * | 字段名1 AS 别名1[字段名2 AS 别名2， ···， 字段名n AS 别名n] FROM 表名 GROUP BY 字段名1[， 字段名2， ···， 字段名n] HAVING 筛选条件;
```

分组后如果还需要满足其他分组条件，需要使用HAVINBG语句

<font color = "blue">示例：</font>从学生表查询年龄在20~30之间的学生信息并按性别分组，找出组内平均分在74分以上的组

```sql
SELECT * FROM student WHERE age BETWEEN 20 AND 30 GROUP BY sex HAVING AVG(score)>74;
```

### 2.5 排序

```sql
-- ASC升序 DESC降序
SELECT ALL/DISTINCT * | 字段名1 AS 别名1[, 字段名2 AS 别名2， ···，字段名n AS 别名n] FROM 表名 WHERE 查询条件 ORDER BY 字段名1 ASC|DESC， 字段名2 ASC|DESC，···，字段名n ASC|DESC;
```

ORDER BY 必须位于 WHERE 条件之后

<font color = "blue">示例：</font>从学生表查询年龄在18~30之间的学生信息并按成绩从高到低排列，如果成绩相同，则按年龄从小到大排列

```sql
SELECT * FROM student WHERE age BETWEEN 18 AND 30 ORDER BY score DESC, age ASC;
```

### 2.6 分页

```sql
SELECT ALL/DISTINCT * | 字段名1 AS 别名1[， 字段名2 AS 别名2 ，···，字段名n AS 别名n] FROM 表名 WHERE 查询条件 LIMIT 偏移量，查询条数;
```

LIMIT 的第一个参数表示偏移量，也就是跳过的行数。

LIMIT 的第二个参数表示查询返回的最大行数，可能没有给定的数量那么多行

<font color = "blue">示例：</font>从学生表分页查询成绩及格的同学信息，每页显示3条，查询第二页学生信息

```sql
SELECT * FROM student WHERE score>=60 LIMIT 3, 3;
```

<font color = "red">**注意：**</font>

如果一个查询中包含分组、排序和分页，那么它们之间必须按照<font color = "red">**分组->排序->分页**</font>的先后顺序排列



## 3. 练习

现有员工表 `emp` ，包含字段员工编号（no），类型为整数，长度为20，是主键，自增长，非空；姓名（name），类型为字符串，长度为20，非空；性别（sex），类型为字符串，长度为2, 默认值为"男"; 年龄（age），类型为整数，长度为3, 非空；所属部门（dept），类型为字符串，长度为20, 非空；薪资（salary），类型为浮点数，长度为10, 小数点后面保留2位有效数字，非空

```sql
DROP TABLE IF EXISTS emp;
CREATE TABLE IF NOT EXISTS emp (
	`no` INT(20) AUTO_INCREMENT  NOT NULL PRIMARY KEY COMMENT '编号',
	name VARCHAR(20) NOT NULL COMMENT '姓名',
	sex VARCHAR(2) DEFAULT '男' COMMENT '性别',
	age INT(3) NOT NULL COMMENT '年龄',
	dept VARCHAR(20) NOT NULL COMMENT '所属部门',
	salary DOUBLE(10, 2) NOT NULL
) ENGINE=InnoDB CHARSET=UTF8 COMMENT='员工表';
-- 吴梅工作出色被提升为测试主管，薪资调整为11000
```

1. 向员工表插入如下数据：

| 姓名 | 性别 | 年龄 | 部门   | 薪资  |
| :--- | :--- | :--- | :----- | :---- |
| 张三 | 男   | 22   | 研发部 | 13000 |
| 李刚 | 男   | 24   | 研发部 | 14000 |
| 金凤 | 女   | 23   | 财务部 | 8000  |
| 肖青 | 女   | 26   | 财务部 | 9000  |
| 张华 | 男   | 28   | 研发部 | 15000 |
| 董钰 | 女   | 24   | 研发部 | 12000 |
| 吴梅 | 女   | 24   | 测试部 | 9000  |
| 王玲 | 女   | 26   | 测试部 | 9500  |

```sql
INSERT INTO emp(`no`, name, sex, age, dept, salary) VALUES (DEFAULT, '张三', '男', 22, '研发部', 13000);
INSERT INTO emp(`no`, name, sex, age, dept, salary) VALUES (DEFAULT, '李刚', '男', 24, '研发部', 14000);
INSERT INTO emp(`no`, name, sex, age, dept, salary) VALUES (DEFAULT, '金凤', '女', 23, '财务部', 8000);
INSERT INTO emp(`no`, name, sex, age, dept, salary) VALUES (DEFAULT, '肖青', '女', 26, '财务部', 9000);
INSERT INTO emp(`no`, name, sex, age, dept, salary) VALUES (DEFAULT, '张华', '男', 28, '研发部', 15000);
INSERT INTO emp(`no`, name, sex, age, dept, salary) VALUES (DEFAULT, '董钰', '女', 24, '研发部', 12000);
INSERT INTO emp(`no`, name, sex, age, dept, salary) VALUES (DEFAULT, '吴梅', '女', 24, '测试部', 9000);
INSERT INTO emp(`no`, name, sex, age, dept, salary) VALUES (DEFAULT, '王玲', '女', 26, '测试部', 9500);
```

2. 吴梅工作出色被提升为测试主管，薪资调整为11000

   ```sql
   UPDATE emp SET salary=11000 WHERE name='吴梅'; 
   ```

3. 研发部金凤离职

   ```sql
   DELETE FROM emp WHERE name='金凤';
   ```

4. 从员工表中查询出平均年龄小于25的部门

   ```sql
   SELECT dept FROM emp GROUP BY dept HAVING AVG(age)<25;
   ```

5. 从员工表中统计研发部的最高薪资、最低薪资、平均薪资和总薪资

   ```sql
   SELECT MAX(salary), MIN(salary), AVG(salary), SUM(salary) FROM emp WHERE dept='研发部';
   ```

6. 从员工表统计各个部门的员工数量

   ```sql
   SELECT COUNT(*), dept FROM emp GROUP BY dept;
   ```

7. 从员工表中查询薪资在10000以上的员工信息并按薪资从高到低排序

   ```sql
   SELECT * FROM emp WHERE salary>10000 ORDER BY salary DESC;
   ```

8. 从员工表中查询员工信息，每页显示5条员工信息，按薪资从高到低排序，查询第二页员工信息

   ```sql
   SELECT * FROM emp ORDER BY salary DESC LIMIT 5, 5;
   ```

   
