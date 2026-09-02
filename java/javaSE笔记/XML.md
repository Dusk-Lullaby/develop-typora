# XML

## 1. XML

```tex
XML全称是Extensible Markup Language, 可拓展标记语言
```



**XML语法**

1. xml是一种文本文档，其后缀名是xml
2. xml文档内容的第一行必须是对整个文档类型的声明
3. xml文档内容中有且仅有一个根标签
4. xml文档内容中的标签必须严格闭合
5. xml文档内容中的标签属性值必须使用单引号或者双引号引起来
6. xml文档内容中的标签名区分大小写

<font color = "blue">示例</font>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<students> 
	<student name="zhansan" age="20" sex="nan"></student>
	<STUDENT>
		<NAME>zhangsan</NAME>
		<AGE>20</AGE>
		<SEX>nan</SEX>
	</STUDENT>
</students>
```

<font color = "blue">如果XML文档内容出现了像(<)这类似的符号，怎么办？</font>

<font color = "red">**使用标签CDATA来完成，CDATA标签中的内容会按照原样展示**</font>

<font color = "blue">语法</font>

```xml
<![CDATA[
	<!--内容 -->
]]>
```

XML文档可以自定义标签，为了更规范的使用XML文档，可以使用XML约束来限定XML文档中的标签使用。

XML约束可以通过DTD文档和Schema文档来实现。其中DTD文档比较简单，后缀名为dtd，而Schema技术则比较复杂，后缀名为xsd。



## 2. DTD约束

### 2.1DTD约束元素

<font color = blue>元素类型</font>

**EMPTY (空元素)**，元素不包含任何数据，但是可以有属性，如：

```xml
<student name="李四" sex="女" age="20" />
```

**PCDATA(字符串)**，PCDATA是指被解析器解析的文本也就是字符串的内容，不能包含其他元素的类型，如：

```xml
<name>张三</name>
<sex>男</sex>
<age>20</age>
```

**ANY(任何内容都可以)**

<font color = "blue">DTD约束元素出现顺序及次数</font>

| 情景       | 语法                     | 描述                                           |
| ---------- | ------------------------ | ---------------------------------------------- |
| 顺序出现   | `<!ELEMENT name (a, b)>` | 子元素a、b必须同时出现，且a必须在b之前出现     |
| 选择出现   | `<!ELEMENT name (a|b)>`  | 子元素a、b只能出现一个，要么a，要么b           |
| 只出现一次 | `<!ELEMENT name (a)>`    | 子元素a只能且必须出现一次                      |
| 一次或多次 | `<!ELEMENT name (a+)>`   | 子元素a要么出现一次，要么出现多次              |
| 零次或多次 | `<!ELEMENT name (a*)>`   | 子元素a可以出现任意次（包括不出现，即出现0次） |
| 零次或一次 | `<!ELEMENT name (a?)>`   | 子元素a可以出现一次或不出现                    |

<font color = "blue">元素格式</font>

```dtd
<!ELEMENT 元素名称 元素类型>
```

<font color = "blue">示例</font>

```dtd
<!ELEMENT students (student*)>
<!ELEMENT student (name, sex, age) ANY>
<!ELEMENT name (#PCDATA)>
<!ELEMENT sex (#PCDATA)>
<!ELEMENT age (#PCDATA)>
```



### 2.2 DTD约束元素属性

<font color = "blue">属性值类型</font>

**CDATA**，属性值为普通文本字符串。

**Enumerated**，属性值的类型是一组取值的列表，XML文件中设置的属性值只能是这个列表中的一个值。

**ID**，表示属性值必须唯一，且不能以数字开头

<font color = "blue">属性值设置</font>

**#REQUIRED**，必须设置该属性。

**#IMPLIED**，该属性可以设置也可以不设置。

**#FIXED**，该属性的值为固定的。

**使用默认值**

<font color = "blue">属性格式</font>

```tex
<!ATTLIST 元素名 属性名 属性值类型 设置说明>
```

<font color = "blue">示例</font>

```dtd
<!ELEMENT students (student*)>
<!--<!ELEMENT student (name, sex, age) ANY>-->
<!--<!ELEMENT name (#PCDATA)>-->
<!--<!ELEMENT sex (#PCDATA)>-->
<!--<!ELEMENT age (#PCDATA)>-->


<!ELEMENT student EMPTY>
<!ATTLIST student number ID #REQUIRED>
<!ATTLIST student name CDATA>
<!ATTLIST student sex(男|女|其他) #IMPLIED>
<!ATTLIST student age CDATA>
<!ATTLIST student country(中国) CDATA #FIXED>
```



### 2.3 引入DTD约束

<font color = "blue">语法</font>

```dtd
<!DOCTYPE 根标签名 SYSTEM "约束名文档.dtd"
```

<font color = "blue">示例</font>

```xml-dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE students SYSTEM "student.dtd">
<students>
<!--    <student>-->
<!--        <name>张三</name>-->
<!--        <sex>男</sex>-->
<!--        <age>20</age>-->
<!--    </student>-->

    <student number="aaa1" name="张三" sex="其他" age="20" country="中国"/>
    <student number="aaa2" name="张三" sex="其他" age="20"/>
    <student number="aaa3" name="张三" sex="其他" age="20"/>
    <student number="aaa4" name="张三" sex="其他" age="20"/>
</students>
```



## 3. XML解析

### 3.1 解析方式

<font color = "blue">DOM解析</font>

DOM解析将XML文档一次性加载进内存，在内存中形成一棵dom树，优点是操作方便，可以对文档进行CRUD的所有操作，缺点就是占内存。

<font color = "blue">SAX解析</font>

SAX解析将XML文档逐行读取，是基于事件驱动的，有点是不占内存，缺点是只能读取，不能删改

对于XML操作，我们通常都是读取操作，正删改的情况较少。



### 3.2 `Dom4j`解析XML

<font color = "blue">示例</font>

```java
package com.lullaby.xml;

import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

import java.io.InputStream;
import java.util.Iterator;

public class XmlParser {

    public static void main(String[] args) {
        // 构建一个SAX读取器
        SAXReader reader = new SAXReader();
        // 通过类的字节码对象获取一个给的资源，并将给资源读取到流的通道中
        InputStream inputStream = XmlParser.class.getResourceAsStream("student.xml");
        try {
            // SAX读取器从通道中读取一个文档对象
            Document document = reader.read(inputStream);
            // 获取文档的根元素，因为XML只会有一个个根元素
            Element root = document.getRootElement();
            // 获取根元素的标签名
            String tagName = root.getQualifiedName();
            System.out.println("XML文档根标签: " + tagName);
            // 获取根元素的下一级子元素
//            List<Element> elements = root.elements();
//            for (Element element : elements) {
//                // 获取元素的标签名
//                String tag = element.getQualifiedName();
//                System.out.println(tag);
//                // 获取元素的所有属性
//                List<Attribute> attributes = element.attributes();
//                for (Attribute attribute : attributes) {
//                    // 获取属性名
//                    String attrName = attribute.getName();
//                    // 获取属性值
//                    String value = attribute.getValue();
//                    System.out.print("属性: " + attrName + "=>" + value + "\t");
//                }
//                System.out.println();
//            }
            Iterator<Element> iterator = root.elementIterator("student");
            while (iterator.hasNext()) {
                Element element = iterator.next();
                // 获取元素的标签名
                String tag = element.getQualifiedName();
                System.out.println(tag);
//                // 获取元素的所有属性
//                List<Attribute> attributes = element.attributes();
//                for (Attribute attribute : attributes) {
//                    // 获取属性名
//                    String attrName = attribute.getName();
//                    // 获取属性值
//                    String value = attribute.getValue();
//                    System.out.print("属性: " + attrName + "=>" + value + "\t");
//                }
//                System.out.println();
                String name = element.attributeValue("name");
                String sex = element.attributeValue("sex");
                String age = element.attributeValue("age");
                System.out.println(name + "\t" + sex + "\t" + age);
            }
        } catch (DocumentException e) {
            throw new RuntimeException(e);
        }
    }
}
```
