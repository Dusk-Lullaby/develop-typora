# MySQL常用函数

## 1. 常用数学函数

![常用数学函数](D:\Typora\develop\MySQL\image\常用数学函数.png)

## 2. 常用字符串函数

![字符串函数](D:\Typora\develop\MySQL\image\常用字符串函数.png)

<font color = 'blue'>示例：</font>

查询计科和软工人数

```sql
SELECT LEFT(class,2), COUNT(*) FROM stu GROUP BY LEFT(class,2);
```

查询名字有4个字的学生信息

```sql
-- 查询名字有4个字的学生信息
SELECT * FROM stu WHERE `name` LIKE '____';
SELECT * FROM stu WHERE CHAR_LENGTH(`name`)=4;
```

查询年龄被10整除的学生信息

```sql
-- 查询年龄被10整除的学生信息
SELECT * FROM stu WHERE MOD(2026-LEFT(birthday,4),10)=0;
```

## 3. 日期和时间函数

![日期和时间函数](D:\Typora\develop\MySQL\image\日期和时间函数.png)

<font color = "blue">示例：</font>

查询年龄在35岁以上的学生信息

```sql
SELECT * FROM stu WHERE TIMESTAMPDIFF(YEAR,birthday,NOW())>35;
```

查询今天过生日的同学信息

```sql
-- 查询今天过生日的同学信息
SELECT * FROM stu WHERE MONTH(birthday)=MONTH(NOW()) AND DAY(birthday)=DAY(NOW())
```

查询本周过生日的同学信息

```sql
-- 查询本周过生日的同学信息
SELECT * FROM stu WHERE WEEKOFYEAR(birthday)=WEEKOFYEAR(NOW());

SELECT RIGHT(DATE_FORMAT(ADDDATE(NOW(),-DAYOFWEEK(NOW())), '%Y-%m-%d'), 5);
SELECT RIGHT(DATE_FORMAT(ADDDATE(NOW(),7-DAYOFWEEK(NOW())), '%Y-%m-%d'), 5);
SELECT * FROM stu WHERE RIGHT(birthday,5)>RIGHT(DATE_FORMAT(ADDDATE(NOW(),-DAYOFWEEK(NOW())), '%Y-%m-%d'), 5) AND RIGHT(birthday,5)<RIGHT(DATE_FORMAT(ADDDATE(NOW(),7-DAYOFWEEK(NOW())), '%Y-%m-%d'), 5);
```

## 4. 条件判断函数

### 4.1 IF函数

**IF（条件， 表达式1， 表达式2）**

如果条件满足则使用表达式1，否则使用表达式2

<font color = "blue">示例：</font>将学生成绩展示为及格和不及格

```sql
-- 将学生成绩展示为及格和不及格
SELECT id, stu_name, course, score, IF(score>=60,'及格', '不及格') score FROM score;
```

**IFNULL(字段， 表达式)**

如果字段值为空，则使用表达式，否则字段值

<font color = "blue">示例：</font>将未参加考试的学生成绩展示为缺考

```sql
-- 将未参加考试的学生成绩展示为缺考
SELECT id, stu_name, course, score, IFNULL(score, '缺考') score FROM score;
```

### 4.2 CASE···WHEN语句

**CASE WHEN**

<font color = "blue">语法：</font>

```sql
CASE WHEN 条件1 THEN 表达式1 [WHEN 条件2 THEN 表达式2···] ELSE 表达式n END;
```

如果条件1满足，则使用表达式1；[如果条件2满足，则使用表达式2···]否则使用表达式n。相当于Java中的多重if···else语句

<font color = "blue">示例</font>

```sql
SELECT (CASE WHEN(course='Java') THEN score ELSE 0 END) Java FROM score;
```

<font color = "blue">行转列：</font>查询每位学生的各科成绩

```sql
SELECT id, stu_name, course, 
  MAX(CASE WHEN (course = 'Java') THEN score ELSE 0 END) Java, 
  MAX(CASE WHEN (course = 'Html') THEN score ELSE 0 END) Html, 
  MAX(CASE WHEN (course = 'Jsp') THEN score ELSE 0 END) Jsp, 
  MAX(CASE WHEN (course = 'Spring') THEN score ELSE 0 END) Spring
FROM score 
GROUP BY stu_name;
```



**CASE···WHEN语句**

<font color = "blue">语法</font>

```sql
CASE 表达式 WHEN 值1 THEN 表达式1 [WHEN 值2 THEN 表达式2 ···] ELSE 表达式n END;
```

如果表达式的执行结果为值1，则使用表达式1；[执行结果为值2，则使用表达式2， ····]否则，使用表达式n。相当于Java中的switch语句

<font color = "blue">示例</font>

```sql
SELECT (CASE course WHEN 'Java' THEN score ELSE 0) Java FROM score；
```

<font color = "blue">行转列：</font>查询每位学生的各科成绩

```sql
SELECT id, stu_name, course, 
  MAX(CASE course WHEN 'Java' THEN score ELSE 0 END) Java,
  MAX(CASE course WHEN 'Html' THEN score ELSE 0 END) Html,		
  MAX(CASE course WHEN 'Jsp' THEN score ELSE 0 END) Jsp,
  MAX(CASE course WHEN 'Spring' THEN score ELSE 0 END) Spring
FROM score 
GROUP BY stu_name;
```



<font color = "blue"> 练习</font>

查询各班级性别人数，查询结果格式如图

| 班级    | 男   | 女   | 其他 |
| ------- | ---- | ---- | ---- |
| 计科2班 | 811  | 822  | 845  |
| 软工1班 | 825  | 868  | 809  |
| 软工2班 | 833  | 841  | 841  |
| 计科1班 | 789  | 862  | 845  |

```sql
SELECT 
  class, 
  SUM(CASE sex WHEN 0 THEN 1 ELSE 0 END) '男', 
  SUM(CASE sex WHEN 1 THEN 1 ELSE 0 END) '女', 
  SUM(CASE sex WHEN 2 THEN 1 ELSE 0 END) '其他'
FROM stu
GROUP BY class;
```

## 5. 其他函数

### 5.1 数字格式化函数

FORMAT(X,D)，将数字X格式化，将X保留到小数点后2位，截断时进行四舍五入

<font color = "blue">示例</font>

```sql
SELECT FORMAT(1.2353, 2);
```

### 5.2 系统信息函数

![系统信息函数](D:\Typora\develop\MySQL\image\系统信息函数.png)



## 6. 练习

1. 求字符串的字符数

   ```sql
   SELECT CHAR_LENGTH('凌晨一点的猫');
   ```

2. 将字符串“黄昏”和“摇篮曲”拼接成新的字符串

   ```sql
   SELECT CONCAT('黄昏','摇篮曲');
   ```

3. 求“2025-05-09”到现在一共有多少天

   ```sql
   SELECT TIMESTAMPDIFF(DAY,'2025-05-09',NOW());
   ```

4. 如果字段score的值大于90，则展示为优秀，否则展示为良好

   ```sql
   SELECT id, stu_name, course, score, IF(score>90,'优秀','良好') score FROM score;
   ```

   
