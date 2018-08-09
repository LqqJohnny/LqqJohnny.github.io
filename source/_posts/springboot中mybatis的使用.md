---
title: springboot中mybatis的使用
date: 2018-08-07 19:21:49
tags: [springboot,mybatis]
---

还记得之前使用springMVC的时候，用mybatis连接mysql吗？

<!-- more -->

就是 dao + bean + mapper


你要是想修改一个返回类型，你需要修改三个文件 emmmmm！ fuck U ，

在SpringBoot 中，已经将这些配置省去了 ，使用注解的方式可以将 mapper和interface结合在一起，开发便利了不止一点哦

### pom添加依赖

要使用mybatis 当然先要添加相关依赖

```XML
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.1.1</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>

```
### 配置
现在 application.properties 中配置sql的相关信息

```
mybatis.type-aliases-package=com.com.example.lqq.entity
spring.datasource.driverClassName = com.mysql.jdbc.Driver
spring.datasource.url = jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf-8
spring.datasource.username = root
spring.datasource.password = root

```

先新建一个 mapper 包 ，这是一个存放 接口的包： `com.example.lqq.mapper`

```java
@SpringBootApplication
@MapperScan("com.example.lqq.mapper")   // 添加这句
public class DemowithtemplateApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemowithtemplateApplication.class, args);
    }
}
```
### 创建bean
bean还是需要自己创建的 在entity包中 创建一个 Person 类 里面包好 name和age属性

### 编写接口

在接口中直接用注解来设置sql语句

```java
public interface PersonMapper {

    @Select("select * from person")
    List<Person> getPersonList();

    @Select("select * from person where name=#{name}")
    Person getPersonInfo(String name);
}

```

### 调用

在需要的地方直接用
```java
@Autowired
private PersonMapper PersonMapper;

// ...

List<Person> persons = PersonMapper.getPersonList()
Person me = PersonMapper.getPersonInfo("lqq");

```


### 结语

springboot 对于 mybatis 的配置还有很多，上面只是基础使用，还有许多需要注意的细节与实用的功能。之后遇见再补上。


> 参考： http://www.ityouknow.com/springboot/2016/11/06/spring-boo-mybatis.html
