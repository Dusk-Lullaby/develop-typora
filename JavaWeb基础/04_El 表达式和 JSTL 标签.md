# EL 表达式和JSTL 标签

## 1. EL 表达式

### 1.1 什么是 EL 表达式

EL 全称为 Expression Language（表达式语言）

### 1.2  为什么要使用 EL 表达式

在 jsp 页面中编写小脚本，会存在以下不足：

* 代码结构混乱
* 脚本与HTML混合，容易出错
* 代码不易于维护
* 获取javaBean属性必须要实例化及强制类型转化

为了解决这些不足 JSP 提供了 EL 表达式来简化编码，可以使用 EL 表达式来替换 jsp 页面中的小脚本，使得页面和业务逻辑处理相分离，同时还能实现数据类型的自动转型

### 1.3 如何使用 EL 表达式

#### 1.3.1 EL 获取变量的值

<font color = "blue">语法</font>

```jsp
${变量名}
```

<font color = "blue">示例</font>



#### 1.3.2 EL 隐式对象

![](img/EL隐式对象.png)

可以通过隐式对象来指定 EL 的取值的范围：

```jsp
<!--在session范围内取值-->
${sessionScope.user}
```

如果 EL 未指定隐式对象，则取值默认从 pageScope 取值，如果未找到，则从requestScope取值，如果还是未找到，则从 sessionScope 取值，如果仍然未找到，则从applicationScope 取值，如果最终都未找到，那么返回null值

#### 1.3.3 EL 获取对象的属性值

<font color = "blue">语法</font>

```jsp
<!--Java中的访问方式-->
${ 对象名.属性名 }

<!--JS中的访问方式-->
${ 对象名["属性名"] }
```

#### 1.3.4 EL 获取 List 集合中的值

<font color = "blue">语法</font>

```jsp
${ 集合名称[下标] }
```

#### 1.3.5 EL 获取 Map 集合中的值

<font color = "blue">语法</font>

```jsp
<!--Java中的访问方式-->
${ 集合名称.键名 }

<!--JS中的访问方式-->
${ 集合名称["键名"] }
```

#### 1.3.6 EL 表达式中的操作字符

![](img/操作符1.png)

![](img/操作符2.png)

![](img/操作符3.png)



## 2. JSTL 标签

### 2.1 什么是 JSTL

JSTL 全称为 JavaServerPages Standard Tag Library，意味 JSP标准标签库

### 2.2 为什么要使用 JSTL

EL 能够简化 jsp 页面编码，但是，却不能进行逻辑判断，也不能进行循环处理，为了弥补 EL 这方面的不足，jsp 提供了 JSTL 标签，JSTL 标签通常都会与 EL 配合使用，解决页面的逻辑问题。

### 2.3 JSTL 标签库分类

![](img/JSTL标签库.png)

经常使用的标签就是**核心标签**和**格式化标签库**

### 2.4 JSTL 标签库的使用步骤

* 引入 JSTL 标签库支持的jar包：`jstl.jar`和`standard.jar`
* jsp 页面引入标签，如`<%@tagliburi="http://java.sun.com/jsp/jstl/core"prefix="c"%>`

### 2.5 JSTL 核心标签库

#### 2.5.1 通用标签

![](img/通用标签.png)

* `<c:set>`标签

  <font color = "blue">语法</font>

  ```jsp
  <!--将value值存储到范围为scope的变量variable中-->
  <c:setvar="变量名"value="变量值"scope="变量的作用范围"/>
  <!--将value值设置到对象的属性中-->
  <c:settarget="目标对象"property="对象属性"value="对象属性值"/>
  ```

  <font color = "blue">示例</font>

  

* `<c:remove>`标签

  <font color = "blue">语法</font>

  ```jsp
  <c:removevar="变量名"scope="变量的作用范围"/>
  ```

  <font color = "blue">示例</font>



#### 2.5.2 条件标签

