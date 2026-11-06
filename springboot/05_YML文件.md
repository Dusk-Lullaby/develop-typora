# 05_YML文件

springboot使用一个全局的配置文件，配置文件名是固定的

*   application.properties
*   application.yml

## 1. YML 文件语法

*   k:(空格)v：表示一对键值对（空格必须要有）
*   以缩进控制层级关系；只要是左对齐的一列数据，都是同一个层级的；
*   属性和值也是大小写敏感；
*   \#表示注释；
*   字符串无需加引号，如果要加，' '与" "表示字符串内容 会被 转义/不转义

>   YML:
>
>   k: v

## 2. YML文件的值的写法

### 2.1 字面量：普通的值（字符串，数字，布尔值）

*   k: v：字面直接来写；

*   字符串默认不用加上单引号或者双引号；

*   ""：双引号；会转义字符串里面的特殊字符；特殊字符会作为本身想表示的意思

*   name: "zhangsan \n lisi"：输出；zhangsan 换行 lisi

*   ''：单引号；不会转义特殊字符，特殊字符终只是一个普通的字符串数据

*   name: ‘zhangsan \n lisi’：输出；zhangsan \n lisi

```yml
username: eric # 不用带双引号
name1: 'zhangsan \n lisi' # 将/n转义成换行
name2: "zhangsan \n lisi" # 不会转义
```

```java
@Component
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Demo1 {
    @Value("${username}")
    private String username;
    @Value("${name1}")
    private String name1;
    @Value("${name2}")
    private String name2;
}
```

```java
@RestController
public class HelloController {
    @Autowired
    Demo1 demo1;
    @RequestMapping("demo1")
    public String demo1(){
        System.out.println(demo1);
        return "hello";
    }
}
```

### 2.2 复杂数据类型的写法

```yml
#定义复杂数据类型
#定义对象 第一种方式
animal:
  name: dahuang
  age: 2

#定义对象 第二种写法
animal2: {name: xiaohuang, age: 2}

#数组 List set 方式一
pets:
  - cat
  - dog
  - fish

#数组 List set 方式二
pets2: [cat,dog,fish]

#map集合
map1: {k1: v1, k2: v2}
```

### 2.3 复杂类型数据的注入

*   定义配置文件

    ```yml
    person:
      userName: zhangsan
      boss: false
      birth: 2019/12/12 20:12:33
      age: 18
      pet:
        name: tomcat
        weight: 23.4
      interests: [篮球,游泳]
      animal:
      - jerry
      - mario
      score:
        english:
          first: 30
          second: 40
          third: 50
        math: [131,140,148]
        chinese: {first: 128,second: 136}
      salarys: [3999,4999.98,5999.99]
      allPets:
        sick:
        - {name: tom}
        - {name: jerry,weight: 47}
        health: [{name: mario,weight: 47}]
    ```

*   定义Java类

    ```java
    package com.sonnet.demos.pojo;
    
    import lombok.Data;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.stereotype.Component;
    
    @Component
    @Data
    @ConfigurationProperties(prefix = "person.pet")
    public class Pet {
        private String name;
        private Double weight;
    }
    ```

    ```java
    package com.sonnet.demos.pojo;
    
    import lombok.Data;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.stereotype.Component;
    
    import java.util.Date;
    import java.util.List;
    import java.util.Map;
    import java.util.Set;
    
    @Component
    @Data
    @ConfigurationProperties(prefix = "person")
    public class Person {
        private String userName;
        private Boolean boss;
        private Date birth;
        private Integer age;
        private Pet pet;
        private String[] interests;
        private List<String> animal;
        private Map<String, Object> score;
        private Set<Double> salarys;
        private Map<String, List<Pet>> allPets;
    }
    ```

*   控制类

    ```java
    @Autowired
    Person  person;
    
    @RequestMapping("/person")
    public Person person() {
        return person;
    }
    ```